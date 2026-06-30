# Domain Pitfalls: Adding Features to an Existing Single-File App

**Domain:** Vanilla JS single-file web app feature additions (layout redesign, theme update, import/export, data deletion)
**Researched:** 2026-06-30
**Project:** Mindful Breathing v0.6 (Layout & History)

## Executive Summary

When adding features to an existing single-file app, the most common failures stem from tight coupling in the codebase, unintended side-effects from CSS changes, data format incompatibility, and insufficient user confirmation workflows. The v0.6 features—2-column landscape layout, light theme contrast fixes, import/export, and history clear—each introduce specific risks: layout changes can break portrait or previous breakpoints; theme colors must be tested exhaustively across all contexts; import/export must validate and handle corruption; clear history must prevent irreversible data loss. These are not generic pitfalls; they are specific to the transition from a stable single-screen app to one with variable layouts, multiple themes at scale, and destructive operations.

## Critical Pitfalls

### Pitfall 1: Media Query Specificity & Breakpoint Cascade Breaks
**What goes wrong:** Adding a 2-column landscape layout via media queries (`@media (orientation: landscape) and (min-width: ...)`) can unintentionally override portrait or smaller-screen styles. Without careful CSS organization, breakpoint rules interact unpredictably, causing:
- Portrait layout to shift unexpectedly when landscape rules are evaluated
- Existing 50/50 layout assumptions (per PROJECT.md) to break for tablets at edge breakpoints
- Controls or history panel to overflow or misalign on certain device sizes

**Why it happens:**
- The current codebase (2,272 lines) already uses `@media (max-height: 500px)` for landscape. Adding another landscape rule with different selectors or widths can create conflicting declarations.
- CSS specificity conflicts: if a new landscape rule has higher specificity than the existing layout, older breakpoints lose. Conversely, older max-height rules may override newer landscape rules if cascaded incorrectly.
- Breakpoint testing is done via browser DevTools resize, not actual devices. Pixel-density and orientation-lock behavior differ on real phones vs. emulation.

**Consequences:**
- Users on iPad or Android tablets in landscape see misaligned controls or overlapping panels
- Portrait mode on those same devices works fine, creating inconsistent experience users blame on app quality
- Navigation or settings become inaccessible if history panel expands beyond viewport

**Prevention:**
1. **Use mobile-first approach with min-width, not max-width.** Organize CSS so default (narrow) layout applies first, then add landscape/wide rules via min-width.
2. **Test on real devices** (phone, tablet, desktop) in both orientations before shipping. Browser DevTools emulation misses orientation-lock quirks and touch behavior.
3. **Document breakpoint intent.** Comment each media query with its target device class: `/* landscape tablets 800px+: 2-column layout */`.
4. **Isolate breakpoint zones.** Keep landscape layout rules together in one media block, not scattered across the stylesheet. Reduce cascade re-entries.
5. **Use CSS Grid or Flexbox with clear container widths.** Avoid absolute positioning or float-based layout for responsive design; Grid/Flexbox fail gracefully when viewport changes.

**Detection:**
- Portrait mode layout shifts when device rotates to landscape
- Scrolling required horizontally on tablets (should not be)
- Controls hidden or overflow on landscape at specific breakpoints
- Browser DevTools shows correct layout, but real device shows misalignment

---

### Pitfall 2: Light Theme Color Palette Breaks WCAG Requirements Silently
**What goes wrong:** When updating light theme colors to meet WCAG 4.5:1 contrast (normal text) and 3:1 (large text) requirements, developers often change only primary colors but miss secondary text, UI components, borders, or status indicators. This results in:
- Light background + light gray text fails contrast ratio (e.g., #f5f1ed bg + #a89e8c text = 2.8:1, below 4.5:1)
- Interactive elements (buttons, phase labels, ring progress) may not meet 3:1 UI component ratio
- Light theme passes initial spot-check on main text but fails on:
  - Phase label color (#d4a574 on #f5f1ed may not be 4.5:1)
  - Countdown text, status indicators, error messages
  - Ring SVG stroke color for progress visualization
  - Disabled state colors (often lighter, more prone to fail)

**Why it happens:**
- WCAG contrast testing tools (WebAIM, Color Palette Tester) test pairwise, not holistic. Developer tests 5 colors, misses 12 other color pairs in the interface.
- CSS Custom Properties in current code (`--text-light: #5c4033`, `--textSoft-light: #a89e8c`) are set once, but not all text elements use them. Some hard-coded colors remain in inline styles or old rules.
- Light palette is designed for aesthetics (warm cream/gold per PROJECT.md), which conflicts with accessibility. Accessible light palettes tend to be more muted or high-contrast.
- Testing contrast in dark mode works (high contrast by default). Light mode requires active testing; passive loading of light theme doesn't trigger contrast validation.
- Designers often pick "accessible" colors in tools, but those tools don't simulate vision deficiencies (protanopia, deuteranopia, achromatopsia). A 4.5:1 ratio on paper doesn't guarantee readability for colorblind users.

**Consequences:**
- Light theme passes automated WCAG scanning (only checks a few text elements) but fails manual accessibility audit
- Users with low vision or colorblindness report text "unreadable" in light mode
- Legal/compliance team flags accessibility failure before v0.6 ships
- Dark mode users unaffected, so bug reports cluster around light mode only, appearing to be theme-specific

**Prevention:**
1. **Test every color pair involved in the UI.** Document all colors used (background, text, accent, border, ring stroke, button states, disabled states) and test each pair with a tool like [Color Palette Tester](https://www.colorpalettetester.com/) or [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/).
2. **Use CSS Custom Properties consistently.** Replace all hard-coded colors with CSS variables. Verify no color bypasses a variable (e.g., `<circle stroke="#d4a574">` in SVG hard-codes color and won't update with theme toggle).
3. **Test light theme across contexts:** on solid background, over images, in light ambient conditions (not just laptop screen), at zoom levels (100%, 150%, 200%).
4. **Test for colorblindness.** Use a tool like [Stark](https://www.getstark.co/) or browser extension to simulate protanopia/deuteranopia. A 4.5:1 ratio doesn't guarantee colorblind users can distinguish.
5. **Verify all text roles.** Test primary text, secondary (helper) text, labels, placeholders, error messages, and disabled text. Disabled text often fails first due to reduced contrast.
6. **Document the approved light palette.** Create a matrix: `(background color, text color, target ratio, actual ratio, approved: Y/N)`. Share with QA.

**Detection:**
- Automated accessibility scanner (axe, WAVE, Lighthouse) flags contrast failures in light mode only
- Manual contrast checker shows 3.2:1 on secondary text (e.g., `--textSoft-light`)
- Colorblind users report UI elements are indistinguishable (e.g., accent button on light card is too similar in hue)
- Light mode works on high-brightness monitors but is illegible in natural light (outdoor, window-side desk)

---

### Pitfall 3: Import/Export File Validation & Corruption Silent Failures
**What goes wrong:** When building import/export for JSON/CSV session history, developers often:
- Accept any JSON without schema validation, allowing import of corrupted, incomplete, or malformed data
- Merge imported sessions into localStorage without checking for duplicates (user imports same file twice, history doubles)
- Fail silently on file read errors (FileReader API errors, truncated uploads), leaving user unaware import failed
- Allow CSV import with missing columns or data type mismatches, causing numeric fields to become strings (durations parse as "600000" instead of 600000, breaking duration math)
- Overwrite existing history without rollback option if import fails mid-way (user loses current history)

**Why it happens:**
- FileReader API and Blob operations are callback-based and async; errors are easy to miss if wrapped in generic try/catch with empty handlers (common pattern in existing codebase).
- CSV is untyped; column headers are string keys. Parsing "durationMs: '600000'" as a string is valid CSV but breaks code expecting a number.
- No schema definition for session history format. Current code saves `{ date, durationMs, cycles }`, but imported data might have extra fields, missing fields, or renamed fields (e.g., "duration" instead of "durationMs").
- Duplicate detection requires comparing sessions by (date + duration) or ID, but if a user exports and re-imports the same file, duplicates silently accumulate. No hash or ID in current format.
- Testing import/export is manual. Developers test with clean files, not with hand-edited JSON, truncated files, or files with typos.

**Consequences:**
- User imports a backup, app crashes because duration is a string and code tries `totalMs += session.durationMs` (concatenates instead of adding)
- User imports same backup twice, history count is inflated by 50% (user imported 5 sessions, now shows 10)
- User imports file, sees success message, but 20% of sessions silently failed to parse and are missing from new history
- CSV import adds column headers as a session entry: `{ date: "durationMs", durationMs: "cycles" }`, creating a nonsense entry
- User's existing history is overwritten, then import fails on a corrupted file. History is now lost, with no undo.

**Prevention:**
1. **Validate JSON schema before import.** After parsing JSON, verify:
   - Top-level is an array
   - Each session has required fields: `date` (string), `durationMs` (number), `cycles` (number)
   - Optional fields like `preset` (string) are present but not required
   - No extraneous fields that might break code
   - Implement a validator function: `validateSession(s) => s.date && typeof s.durationMs === 'number' && Array.isArray(...)`
2. **Detect duplicates before merge.** Before adding imported sessions to localStorage, check if `(date, durationMs)` pair already exists. Deduplicate or ask user for confirmation.
3. **Handle file read errors visibly.** Wrap FileReader and Blob operations in try/catch, and display user-facing error message (not silent failure):
   ```javascript
   reader.onerror = () => showError("File upload failed. Please try again.");
   ```
4. **Preserve existing history during import.** Load current history, validate and merge imported data, then write combined result to localStorage. If validation fails mid-way, keep current history intact (no partial overwrite).
5. **Add import preview/review step.** Before committing import, show user: "Ready to import X sessions. 2 duplicates will be skipped. Continue?" Allow cancel.
6. **Test with edge cases.** Manually create:
   - JSON with missing fields: `[{ date: "2026-06-30" }]` (missing durationMs, cycles)
   - CSV with wrong column count: `date,duration\n2026-06-30\n2026-06-29,600000` (misaligned)
   - Corrupted file: truncate JSON at mid-character, upload
   - Duplicate import: export, then import same file twice
   - Type mismatch: set durationMs as string "600000" instead of number

**Detection:**
- Import shows "Success" but session count unchanged (silent failure)
- After import, duration calculations are wrong (durationMs is a string, causing `+=` concatenation)
- CSV import creates garbage entries (header row becomes a session)
- History is cleared after failed import (no rollback)

---

### Pitfall 4: Clear History Confirmation Dialog Bypassed by User Reflex
**What goes wrong:** A simple confirmation dialog (`<button>Are you sure? Yes / No</button>`) is not sufficient to prevent accidental deletion. Users:
- Click "Yes" reflexively without reading the dialog (muscle memory: yes = proceed)
- Tab-navigate and space-bar the first button without realizing it's destructive
- On mobile, tap the dialog and immediately tap the first visible button (often delete) before their brain processes the warning
- Undo is unavailable; deleted history cannot be recovered unless user has a backup

**Why it happens:**
- Most web apps use generic confirmation dialogs for non-destructive actions (e.g., "Save changes?"). Users are conditioned to confirm reflexively.
- A simple "Are you sure? Yes / No" dialog takes <500ms to dismiss, too fast for conscious decision-making.
- No secondary confirmation (e.g., typing "delete" or holding a button) to add intentional friction.
- No undo or recovery option. localStorage is cleared immediately; there is no trash/recovery mechanism.
- Mobile UX is cramped: buttons are small, dialogs are full-screen, users tap rapidly.

**Consequences:**
- User accidentally clears 6 months of session history while toggling settings
- No way to recover; user has to restart tracking from scratch
- User requests feature to "undo delete" or "trash before permanent delete," adding scope creep
- Support/issue reports for "lost history" due to accidental clear

**Prevention:**
1. **Use specific, action-oriented language.** Instead of "Are you sure?", use "Permanently delete all X session entries? This cannot be undone."
2. **Add friction for high-risk deletions.** Use one of these patterns:
   - **Typing confirmation:** Require user to type "delete" or a specific word to confirm (e.g., "Type DELETE to confirm")
   - **Hold-to-confirm:** Button requires 2-3 second hold before triggering delete (CSS `animation-delay` or JS timer)
   - **Two-step confirm:** First click shows "Are you sure?" with timeout (3s), second click (if within timeout) executes
3. **Provide data export before delete.** Automatically offer: "Export history to CSV before deleting? Download / Cancel / Delete Anyway"
4. **Implement soft-delete with recovery window.** Instead of immediate deletion, move history to a "Archived" state for 7 days. Show banner: "Deleted 42 sessions. Undo (7 days)?" User can recover within window.
5. **Mobile-specific UX.** On small screens, increase button size and spacing. Use a full-screen modal dialog (not inline popup) to reduce accidental taps.
6. **Test with rapid interactions.** Try:
   - Tap/click Yes button as fast as possible (should still require full dialog interaction)
   - Tab-navigate to buttons and press Space/Enter (should not skip to delete)
   - Rotate device mid-dialog (dialog should persist, not dismiss)

**Detection:**
- User reports "I didn't mean to delete all my history"
- Multiple similar bug reports suggest pattern (accidental deletion)
- Metrics show clear history is clicked often but rarely completed (if button has analytics)
- History size fluctuates unexpectedly (large drop suggests accidental clear)

---

### Pitfall 5: Landscape Layout Breaks Portrait or Existing Tablet Breakpoints
**What goes wrong:** Adding a 2-column layout for landscape orientation (per PROJECT.md: "ring left, controls/history right") can inadvertently:
- Make portrait mode narrower or shift controls to unexpected positions
- Break existing tablet breakpoints (landscape tablet at 1024px may activate new rule, hiding controls)
- Cause history panel to exceed viewport height even with scrolling (overflow: auto on scrollable panel within a grid can create double-scroll or hidden content)
- Force container width to grow beyond `560px` max (set in current CSS), causing oversized layouts on desktop

**Why it happens:**
- Current CSS uses `width: min(560px, 92vw)` for `.container`, designed for portrait. A 2-column landscape layout needs wider container, but changing max-width affects portrait on wide screens (ultra-widescreen monitors, desktop).
- History panel scrolling: if history is a grid cell with `overflow: auto`, but the grid itself has limited height, the panel may not scroll properly or may overflow its container.
- Existing breakpoints use `max-height: 500px` (landscape). A new landscape rule with different width might activate at the same height, causing conflicts.
- No explicit `flex-shrink` or `min-width` on grid cells, so panels compress unexpectedly on mid-size tablets.

**Consequences:**
- Portrait mode on iPhone/Android shifts slightly due to changed `.container` width
- Tablet landscape has history panel taller than screen, no scroll handle visible
- Desktop ultra-wide (2560px+) shows oversized layout with tons of whitespace, destroying minimalist aesthetic
- Existing 50/50 layout promise (from PROJECT.md) breaks: ring and controls are no longer balanced on tablets

**Prevention:**
1. **Use CSS Grid with explicit cell widths.** Define layout as:
   ```css
   @media (orientation: landscape) and (min-width: 768px) {
     .container { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
     .ringWrap { /* ring on left */ }
     .rightPanel { /* controls + history on right */ overflow-y: auto; max-height: 100vh; }
   }
   ```
   Ensure `.rightPanel` has constrained height so scrolling works.
2. **Preserve portrait `.container` width constraints.** Keep `width: min(560px, 92vw)` for portrait. For landscape, use:
   ```css
   @media (orientation: landscape) {
     .container { width: min(1200px, 96vw); /* wider for landscape */ }
   }
   ```
3. **Test all breakpoint combinations:**
   - iPhone 14 (390px, portrait) — should use original layout
   - iPhone 14 (844px, landscape) — should use 2-column
   - iPad (1024px, portrait) — should use original layout
   - iPad (1366px, landscape) — should use 2-column
   - Desktop 1920x1080 (1920px, landscape) — should not overflow
4. **Verify scrolling behavior.** For scrollable panels, test:
   - Panel content taller than viewport — scrollbar appears and scrolls smoothly
   - Rapid scroll on mobile — no janky behavior
   - Touch scroll + keyboard scroll both work (arrow keys in focused panel)
5. **Use physical devices for landscape testing.** Browser resize doesn't test orientation-lock behavior, touch-scroll performance, or real DPI.

**Detection:**
- Portrait mode on phone has shifted controls or text
- Tablet landscape shows single-column layout instead of 2-column (media query didn't fire)
- History panel scrolls but scrollbar is cut off or unreachable
- Desktop displays oversized layout compared to v0.5

---

## Moderate Pitfalls

### Pitfall 6: localStorage Quota Exceeded During Import
**What goes wrong:** When importing a large session history (e.g., user exports 1 year of daily sessions = 365 entries ≈ 100+ KB), merging into existing localStorage can exceed the 5–10 MB quota, throwing `QuotaExceededError`. Current codebase (v0.5) has error handling for `QuotaExceededError`, but import doesn't:
- Import validation passes (file is valid JSON)
- User clicks "Import"
- Mid-merge to localStorage, quota is exceeded
- Error is silent or shows generic message
- User's original history + import are both partially written (corrupted state)

**Why it happens:**
- v0.5 added `QuotaExceededError` handling for saveSettings (per PROJECT.md), but only for the save path, not for bulk operations.
- Import doesn't check localStorage capacity before merging. It loads current history, combines with imported data, and writes back. If combined size exceeds quota, entire write fails.
- Error handling in current codebase uses try/catch with empty handlers or generic error display. Bulk import isn't treated specially.

**Consequences:**
- Import reports "Success" but data is partially written (corruption)
- User's history size is inconsistent; some imported sessions are missing
- localStorage is in a bad state; subsequent saves may also fail with quota error
- User is confused: "I imported, but not all sessions are there"

**Prevention:**
1. **Pre-check localStorage available space.** Before import, estimate combined size:
   ```javascript
   const currentSize = JSON.stringify(currentHistory).length;
   const importSize = JSON.stringify(importedHistory).length;
   if (currentSize + importSize > 4 * 1024 * 1024) {
     showError("Import too large. Current history: 2 MB, import: 1.5 MB. Total would exceed 5 MB limit.");
     return;
   }
   ```
2. **Handle QuotaExceededError in import.** Wrap the localStorage write in try/catch, specifically for quota errors:
   ```javascript
   try {
     saveHistory(mergedHistory);
   } catch (e) {
     if (e.name === 'QuotaExceededError') {
       showError("Storage full. Delete some sessions first, then try importing again.");
     }
   }
   ```
3. **Offer cleanup suggestions.** If quota is exceeded, suggest: "Clear old sessions from before 2026-01-01 to free up space."
4. **Atomic writes.** Don't write partially. Load, validate, merge, check quota, write all-or-nothing.

**Detection:**
- After import, total session count is less than expected
- Random gaps in history (imported sessions are missing)
- Subsequent saves fail with quota error

---

### Pitfall 7: CSV Export with Special Characters (Commas, Quotes) Breaks Parse on Re-import
**What goes wrong:** When exporting history to CSV, session data (date, duration, cycles) are simple, but if future sessions include notes, preset names, or other text, CSV formatting breaks:
- Session with comma in note: `2026-06-30,600000,10,My session, Part 2` parses as 5 columns instead of 4
- Session with quote: `2026-06-30,600000,10,"My session "Custom""` parses with unescaped quotes
- UTF-8 characters (emoji, accents) in export cause encoding issues on re-import (especially on Windows with different default encoding)

**Why it happens:**
- Current session format has only date, durationMs, cycles (no text fields). CSV export is simple.
- Future feature creep (v0.7 or later) adds session notes or custom preset names. CSV export code is not updated to escape special characters.
- Developers often assume "CSV is simple" and hand-build CSV by string concatenation: `"${date},${duration},${cycles}"` instead of using proper CSV parsing/writing library.
- Re-import uses basic split(`,`) instead of a CSV parser that handles escaped commas and quotes.

**Consequences:**
- User adds session note "Session before meeting, then lunch break" and exports CSV
- Re-import fails or parses incorrectly, losing session data
- User has to manually edit CSV in spreadsheet app to fix format
- Data loss if user deletes original history and imports broken CSV

**Prevention:**
1. **Use CSV format with proper escaping now.** Even though current sessions have no text fields, export with quoted fields and escaped quotes:
   ```javascript
   const csvRow = `"${date}","${durationMs}","${cycles}"`;
   // If future note field: "${note.replace(/"/g, '""')}"`
   ```
2. **Comment the format in export.** Include a header row in CSV: `date,durationMs,cycles`. User and future maintainers know expected columns.
3. **Use JSON for export by default, CSV as secondary option.** JSON is unambiguous and doesn't require escaping. CSV is only for spreadsheet import (less common for breathing app).
4. **Test re-import of special cases.** Manually create session with:
   - Comma: "Session, Part 2"
   - Quote: 'Session "Custom"'
   - UTF-8: "Séance 呼吸"
   - Export, re-import, verify data integrity

**Detection:**
- CSV export has fewer sessions on re-import than on export
- Re-import shows parsing errors (unexpected columns)
- User reports notes/custom data lost after export/import cycle

---

### Pitfall 8: Existing Layout Assumptions Break During Refactor
**What goes wrong:** The current codebase has hardcoded assumptions about layout in JavaScript (e.g., `.ringWrap` width is 240px, history panel is below the ring). When refactoring to 2-column, code that depends on layout order can break:
- History rendering code assumes vertical stack: ring on top, controls below, history at bottom
- Ring sizing assumes portrait (240px hardcoded in RING config). In landscape, ring may need to be larger or smaller based on available space.
- Dynamic height calculations (e.g., for history panel scrolling) are based on portrait assumptions and overflow when refactored to 2-column grid

**Why it happens:**
- Single-file app (2,272 lines) has tight coupling between DOM structure, CSS, and JavaScript
- RING config: `{ size: 240 }` is hardcoded as a constant, not responsive to layout
- JavaScript functions like `renderHistory()` append to a specific DOM element (e.g., `historyListEl.innerHTML = ...`) without considering if that element's height/position has changed
- No clear separation of concerns between layout (CSS) and behavior (JS)

**Consequences:**
- Ring size doesn't scale in landscape, making it too large or too small relative to controls
- History panel scrolling doesn't work because height calculation is wrong (based on portrait viewport)
- Controls overlap the ring in landscape because layout order changed but JavaScript still positions elements as if in portrait

**Prevention:**
1. **Decouple JavaScript from layout structure.** Instead of assuming vertical stack, use CSS Grid or Flexbox and let CSS define order. JS should render data, not manage layout positioning.
2. **Make ring size responsive.** Calculate ring size based on available space:
   ```javascript
   const getRingSize = () => {
     const isLandscape = window.matchMedia('(orientation: landscape)').matches;
     return isLandscape ? 200 : 240; // adjust dynamically
   };
   ```
   Or use CSS `clamp()` to scale ring SVG viewBox: `viewBox="0 0 ${ringSize} ${ringSize}"`
3. **Use CSS variables for layout-dependent values.** Define in CSS:
   ```css
   :root { --ringSize: 240px; }
   @media (orientation: landscape) { :root { --ringSize: 200px; } }
   ```
   Reference in JS: `ringSize = getCSSVariableValue('--ringSize')`
4. **Test layout refactor with browser DevTools.** Use responsive design mode to resize and verify JS behavior is correct at each breakpoint.

**Detection:**
- Ring is too large/small in landscape (doesn't fit next to controls)
- History panel scrolling is broken in landscape (content is hidden)
- Controls overlap or misalign after CSS refactor

---

## Minor Pitfalls

### Pitfall 9: Theme Toggle UI Hidden But Code Still Present (Vibration API Pattern)
**What goes wrong:** v0.5 hid the vibration UI due to Samsung/Android OS limitation (per PROJECT.md). If light theme improvements are incomplete, similar patterns can occur:
- Light theme button exists in HTML but has `display: none` in CSS (dead code)
- Or light theme CSS custom properties are defined but not used in all elements (inconsistency)
- Code comment says "vibration deferred," but vibration code is still called in `playBeep()` or phase transition, wasting CPU

**Why it happens:**
- Single-file app makes it easy to comment out UI without removing underlying code
- CSS `display: none` hides UI but doesn't clean up HTML or JS
- Developers copy patterns from v0.5 (hide vibration, keep code) without evaluating if that pattern makes sense for new features

**Consequences:**
- Code bloat: ~50 lines of unused vibration code still in file (minor)
- Confusion for future maintainers: "Why is vibration button in HTML if it's hidden?"
- Potential for accidental re-enable without testing (someone removes `display: none`, vibration fails on users' devices)

**Prevention:**
1. **Remove UI elements that are disabled.** Don't hide with `display: none`; remove the HTML entirely.
2. **Comment code as `/* DEFERRED v0.7 */`, not as hidden.** If vibration will be re-enabled later, document when and why.
3. **If code is truly dead, delete it.** Easier to restore from git history than to maintain dead code.

**Detection:**
- HTML has elements with `display: none` in inline style
- CSS rules exist for hidden elements (wasted bytes)

---

### Pitfall 10: Import/Export Dialog Opens at Top of Page, Hidden Behind Fixed Header
**What goes wrong:** If import/export buttons are at the bottom of the history panel (due to 2-column layout), opening a file input dialog or save dialog may open off-screen or behind fixed UI elements (buttons, controls), making it invisible to users.

**Why it happens:**
- File input dialogs are OS-level and not always screen-centered
- Fixed positioning on buttons may cause z-index issues with dialogs
- On mobile, dialogs may open off-screen or at the top (away from the button user just clicked)

**Consequences:**
- User clicks "Export" and nothing appears to happen (dialog is hidden)
- User clicks "Export" multiple times, unintentionally opening multiple dialogs
- User thinks feature is broken

**Prevention:**
1. **Position import/export buttons at top of history panel** (visible and accessible)
2. **Test file dialog opening on mobile** (iOS Safari, Chrome, Android)
3. **Ensure z-index is high enough** for overlays: `.fileInputDialog { z-index: 1000; }`

**Detection:**
- User reports "Export button doesn't work" (dialog is opening off-screen)
- Multiple file dialogs open if user clicks button repeatedly

---

## Phase-Specific Warnings

| Phase Topic | Likely Pitfall | Mitigation |
|-------------|---------------|------------|
| **Phase 4: Layout + Light Theme** | Media query specificity breaks portrait or tablet breakpoints | Test on real devices (phone portrait, tablet both orientations, desktop). Use mobile-first CSS with min-width. Document breakpoint intent. |
| **Phase 4: Light Theme** | Light palette fails WCAG contrast on secondary text, ring stroke, disabled states | Test all color pairs (not just primary text) with Color Palette Tester. Verify CSS Custom Properties are used consistently (no hardcoded colors in SVG or inline styles). Simulate colorblindness. |
| **Phase 5: Import/Export** | JSON/CSV import corrupts data due to missing validation or type mismatches | Validate JSON schema before import. Handle FileReader errors visibly. Test with edge cases (duplicates, truncated files, wrong data types, special characters). |
| **Phase 5: Import/Export** | localStorage quota exceeded during large import, leaving data in partial/corrupted state | Pre-check quota. Handle QuotaExceededError. Use atomic writes (all-or-nothing). Offer cleanup suggestions if quota full. |
| **Phase 5: Clear History** | User reflexively confirms and loses data irreversibly | Use specific destructive language ("Permanently delete X sessions?"). Add friction (typing confirmation, hold button, 2-step confirm). Offer export-before-delete. Consider soft-delete with recovery window. Test rapid interactions (tab-navigate, mobile tap). |
| **Phase 4: Layout** | Existing JavaScript assumptions about portrait layout break (ring size, history height, element positioning) | Decouple JS from layout. Use CSS Grid/Flexbox. Make sizing responsive with CSS variables or clamp(). Calculate heights dynamically based on viewport. Test JS behavior at each breakpoint. |
| **Phases 4 & 5** | Single-file codebase becomes harder to maintain as features accumulate (line count growing, coupling increases) | Consider modular sections in code comments. Keep sections isolated (CONFIG, STATE, DOM, etc.). Avoid side-effects across sections. Plan for future refactoring (modules, bundling) if file exceeds 3,000 lines. |

---

## Sources

- [How to Solve Common Media Query Problems in Responsive Design](https://medium.com/@Adekola_Olawale/how-to-solve-common-media-query-problems-in-responsive-design-7014ac9b8905)
- [How to Debug Breakpoint Issues in CSS Media Queries](https://medium.com/@Adekola_Olawale/how-to-debug-breakpoint-issues-in-css-media-queries-4ba87d25eb69)
- [Responsive Layouts, Fewer Media Queries](https://css-tricks.com/responsive-layouts-fewer-media-queries/)
- [Database Design Patterns for Ensuring Backward Compatibility](https://www.pingcap.com/article/database-design-patterns-for-ensuring-backward-compatibility/)
- [Migration Strategies: Moving Applications and Databases Without Breaking Things](https://dev.to/lovestaco/migration-strategies-moving-applications-and-databases-without-breaking-things-57ld)
- [localstorage-migrator: A JS library for applying migrations to localstorage](https://github.com/ragnarstolsmark/localstorage-migrator)
- [How to Import and Export CSV Files Using JavaScript](https://developer.mescius.com/blogs/how-to-import-and-export-csv-files-using-javascript)
- [How to create and download CSV file in JavaScript](https://www.geeksforgeeks.org/javascript/how-to-create-and-download-csv-file-in-javascript/)
- [Export JSON to CSV file using Javascript](https://gist.github.com/dannypule/48418b4cd8223104c6c92e3016fc0f61)
- [Client side csv download using Blob](https://riptutorial.com/javascript/example/24711/client-side-csv-download-using-blob)
- [Some basic UI patterns for preventing accidental deletion](https://miksovsky.blogs.com/flowstate/2006/02/some_basic_ui_p.html)
- [Double-check user actions: All about warning message UI](https://blog.logrocket.com/ux-design/double-check-user-actions-confirmation-dialog/)
- [The Ultimate Guide to Delete Dialog UX Design](https://almaxagency.com/user-experience-ux-design/the-ultimate-guide-to-delete-dialog-ux-design/)
- [Delete with additional confirmation](https://cloudscape.design/patterns/resource-management/delete/delete-with-additional-confirmation/)
- [How to Make Sure Users Don't Accidentally Delete](https://uxmovement.com/buttons/how-to-make-sure-users-dont-accidentally-delete/)
- [Confirmation dialogs: How to design dialogs without irritation](https://uxplanet.org/confirmation-dialogs-how-to-design-dialogues-without-irritation-7b4cf2599956)
- [How to design better destructive action modals](https://uxpsychology.substack.com/p/how-to-design-better-destructive)
- [WebAIM: Contrast Checker](https://webaim.org/resources/contrastchecker/)
- [Color Contrast Requirements: WCAG Guide for Designers and Developers](https://testparty.ai/blog/color-contrast-requirements)
- [WebAIM: Contrast and Color Accessibility](https://webaim.org/articles/contrast/)
- [Free Color Palette Tester — Check All Pairings for WCAG Contrast](https://www.colorpalettetester.com/)
- [Color Contrast for Accessibility: WCAG Guide (2026)](https://www.webability.io/blog/color-contrast-for-accessibility)
- [WCAG 2 Colour Contrast Guidelines](https://accessibilityassistant.com/blog/accessibility-insights/wcag-2-colour-contrast-accessibility-guidelines/)
- [A high level overview to refactoring monolithic code structures](https://sdremthix.medium.com/a-high-level-overview-to-refactoring-monolithic-code-structures-2d5b70a79570)
- [How to Refactor a Monolithic Codebase Over Time](https://www.cloudbees.com/blog/how-to-refactor-a-monolithic-codebase-over-time/)
- [Refactoring Old Monolith Architecture: A Comprehensive Guide](https://medium.com/insiderengineering/refactoring-old-monolith-architecture-a-comprehensive-guide-7c192d7612e8)
- [Migrating to CSS theme variables](https://v5.mui.com/material-ui/experimental-api/css-theme-variables/migration/)
- [How To Configure Application Color Schemes With CSS Custom Properties](https://www.smashingmagazine.com/2020/08/application-color-schemes-css-custom-properties/)
- [Using css custom properties for color and theming](https://griffa.dev/posts/using-css-custom-properties-for-color-and-theming/)
- [Creating Color Themes With Custom Properties, HSL, and a Little calc()](https://css-tricks.com/creating-color-themes-with-custom-properties-hsl-and-a-little-calc/)
- [Using CSS custom properties (variables)](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)
