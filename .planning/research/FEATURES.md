# Feature Landscape: Mindful Breathing v0.6

**Domain:** Web breathing app (vanilla JS, single-file, offline-first)
**Researched:** 2026-06-30
**Mode:** Ecosystem analysis of 4 planned features
**Overall confidence:** HIGH

---

## Table Stakes

Features users expect for v0.6 to feel complete and competitive.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| **2-column landscape layout** | Tablets and desktops waste horizontal space with 1-column layout; users expect better use of screen real estate in landscape orientation | Medium | CSS media query switch from `flex-direction: column` (portrait) to side-by-side layout (landscape). Ring stays left and scalable; controls/history right and scrollable. Common pattern in breathing/wellness apps. Potential for mobile reflow complexity. |
| **Light theme WCAG contrast** | v0.4 shipped light theme with custom palette; not verified against WCAG; users with low vision expect readable text (4.5:1 for normal, 3:1 for large text) | Medium | Requires hex value audits of all text/background pairs, SVG stroke/fill pairs, and button states. Testing tools available (WebAIM, Level Access, Accessible Web). v0.5 uses `clamp()` for fluid typography; large text (24pt+) already qualifies for 3:1. Normal text (14-18pt) must meet 4.5:1. |
| **Import/export session history** | Users want portable data; no vendor lock-in; offline-first means no cloud sync → file-based backup/restore is table stake | High | JSON export straightforward (serialize array, create Blob, download). CSV export requires header row + manual string construction or PapaParse. Import requires FileReader API + JSON.parse or CSV parsing. Edge cases: corrupted JSON, malformed CSV headers, data versioning. |
| **Clear history with confirmation** | Data deletion is destructive; users expect confirmation modal to prevent accidental loss | Low | Native `<dialog>` element recommended for accessibility (built-in focus trapping, Escape key, backdrop). Alternative: custom modal with ARIA attributes. Keyboard/screen reader support critical. |

---

## Differentiators

Features that elevate the product beyond table stakes. Not expected, but valuable if done well.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| **Landscape 2-column + persistent scroll state** | Remember user's last scroll position in history panel across sessions | Low | sessionStorage for scroll position; reapply on reload in landscape. Reduces friction when users switch between portrait/landscape. |
| **Contrast ratio accessibility summary UI** | Show users WCAG AA/AAA status in settings panel; let them toggle high-contrast mode via custom CSS variable override | Medium | Requires computed contrast ratio calculation in JS (formula: (L1 + 0.05) / (L2 + 0.05) where L = luminance). Display as badge ("WCAG AA", "WCAG AAA") in settings. Can toggle `--high-contrast` CSS var to boost all ratios to 7:1+. |
| **Batch import + data merge** | Import multiple history files without overwriting existing data; show conflict resolution UI (keep newer, keep older, merge all) | High | Requires version/timestamp comparison, deduplication logic, and conflict UI. Risk: complexity spirals; data integrity harder to guarantee. |
| **CSV export with custom columns** | Let users choose which fields to export (date, duration, cycles, phase breakdown) | Medium | Requires column picker UI in settings. CSV generation becomes templated. Adds flexibility but increases surface area for bugs. |
| **Dark mode SVG optimization** | Optimize ring/icon rendering for dark mode (reduced opacity, glow effects, better contrast against dark bg) | Low | CSS-only via `prefers-color-scheme` media queries. Ring stroke-width or opacity can vary per theme. Minimal code impact. |

---

## Anti-Features

Features explicitly NOT to build in v0.6.

| Anti-Feature | Why Avoid | What to Do Instead |
|--------------|-----------|-------------------|
| **Cloud sync / user accounts** | Project constraints: localStorage only, single-user, no backend. Violates design premise (offline-first, zero dependencies) | Stick to file-based import/export. Users manage their own backups. |
| **Batch clear with partial selection** | UI complexity explodes; users likely to delete unintended sessions; confirmation modal already prevents accidents | Keep "Clear All" as single destructive action. Users can restore from backup if needed. |
| **Real-time CSV editing** | Opens Pandora's box: in-app CSV viewer, cell editing, validation, undo/redo. Beyond scope of breathing app. | Export to CSV for spreadsheet editing; re-import cleaned data. Keep app's job narrow. |
| **Data encryption** | Adds complexity without solving real problem; localStorage is per-origin, same-site policy already isolates data. Users on shared devices can use private browsing. | Document localStorage isolation in privacy section. Don't oversell security. |
| **Multiple export formats beyond JSON/CSV** | Excel, YAML, XML parsing/generation bloats the app. 2 formats cover 95% of use cases. | Export JSON (universal, nested structure) and CSV (spreadsheet compatible). That's it. |

---

## Feature Dependencies & Interaction Map

### Import/Export ↔ Clear History

```
User workflow:
  1. User has 100 sessions in history
  2. User exports to JSON (backup.json)
  3. Later: wants fresh start → Clear History (with confirmation)
  4. User can re-import from backup.json if they change their mind
```

**Dependency:** Clear History is destructive but safe because import/export exists. Without import, clear would be catastrophic.

### 2-Column Layout ↔ History Rendering

```
Portrait mode:
  - 1 column: ring top, controls middle, history bottom (scrollable)
  
Landscape mode:
  - 2 columns: ring + controls left (50%), history right (50%, scrollable)
  - Each column scrolls independently
```

**Dependency:** History already renders paginated (5 items per page). In landscape, make right column scrollable; in portrait, keep full-page scroll.

### Light Theme Contrast ↔ SVG Ring

```
Light theme pitfall:
  - Ring progress stroke (#FFD700 gold on #F5F1E8 cream) = low contrast
  - SVG fill/stroke don't inherit text color automatically
  - Must explicitly set stroke color via CSS or inline styles
  
Solution:
  - SVG <circle> should have stroke="currentColor" or style="stroke: var(--accent)"
  - Fallback to a darker shade if light palette fails contrast
```

**Dependency:** Ring is SVG; light theme requires SVG-specific styling. Text contrast is CSS-only.

---

## Complexity Breakdown

### 2-Column Landscape Layout

**Complexity: Medium**

**Why Medium (not Low):**
- Simple CSS media query (`@media (orientation: landscape) { }`) handles flex-direction or grid-column changes
- Risk: existing portrait layout CSS may have unintended interactions with landscape (z-index, absolute positioning, scrollbar width)
- Ring SVG scaling in left column must remain aspect-ratio-locked
- History panel right column may need new scrollable container with `overflow-y: auto; max-height: 100vh`
- Testing required: portrait → landscape rotation on real devices (iPhone, iPad, Android tablets)

**Straightforward parts:**
- Flexbox layout switch: `flex-direction: row` in landscape
- Ring size remains responsive via CSS `clamp(200px, 50vw, 400px)`
- Control buttons stay in left column

**Edge cases:**
- Small tablets in landscape (width: 768px) may still need 1-column layout; needs second breakpoint
- Scroll position reset when rotating portrait ↔ landscape
- History pagination stays on page 1 vs remembers last page in landscape

### Light Theme WCAG Contrast

**Complexity: Medium**

**Why Medium (not Low):**
- Simple: run color pairs through WebAIM Contrast Checker, adjust hex values if needed
- Hard part: v0.4 light palette already chosen; adjusting may break aesthetic
- SVG ring stroke requires separate testing; CSS `stroke` property doesn't inherit from text color by default
- Button states (hover, active, disabled) each need contrast check
- Testing tools are free and easy to use; implementation is manual color tweaks

**Straightforward parts:**
- Text color on background: test foreground vs background hex values
- Large text (24pt+) needs only 3:1 (easier to pass than 4.5:1)

**Edge cases:**
- Disabled buttons: may fall below 4.5:1 due to reduced opacity; might need exception under WCAG "incidental" rule
- SVG progress ring: animated stroke may flicker or show anti-aliasing artifacts; test in multiple browsers
- Dark mode already passes (dark bg + light text); light mode is the risk

### Import/Export Session History

**Complexity: High**

**Why High (not Medium):**
- JSON export: straightforward, 10 lines of code
- CSV export: requires header row + CSV string escaping (quotes, commas, newlines in field values)
- JSON import: requires FileReader API + JSON.parse error handling
- CSV import: requires parsing logic (PapaParse library) or custom parser; CSV edge cases are notorious
- Data validation: must check that imported sessions have required fields (date, durationMs, cycles); silently ignore or reject malformed entries?
- Data versioning: if v0.7 changes session data structure, v0.6 exports may be incompatible with v0.7

**Straightforward parts:**
- JSON.stringify(history) → Blob → download
- FileReader API to read uploaded file
- localStorage save after import

**Edge cases:**
- CSV with quoted fields: `"Smith, John"` should parse as single field, not split on comma
- CSV with newlines inside quotes: `"Address:\nLine 1\nLine 2"` should preserve newline, not break row
- Empty CSV (no data rows, only header)
- JSON with unknown fields (v0.7 backward compatibility)
- File size: importing 10MB JSON will crash some browsers; should warn user
- Duplicate sessions: does user want to deduplicate by (date + duration) or allow duplicates?

### Clear History with Confirmation Dialog

**Complexity: Low**

**Why Low:**
- Confirmation modal is a standard UX pattern
- Native HTML5 `<dialog>` element provides focus management, Escape key, backdrop
- Two buttons: "Cancel" (closes dialog, does nothing) and "Confirm" (calls `clearHistory()`, closes dialog)
- Accessibility: set focus to "Cancel" by default (safer default; user must explicitly choose to delete)

**Straightforward parts:**
- Show modal when user clicks "Clear History" button
- Wire up button events to close modal or delete data
- Play beep + vibration on confirm (consistent with phase transitions)

**Edge cases:**
- User rapidly clicks "Clear History" twice; second modal should not appear (track `isConfirmDialogOpen` state)
- Screen reader user: modal must announce its purpose (aria-labelledby)
- Keyboard-only user: Tab must not escape the modal; Escape must close without deleting

---

## MVP Recommendation for v0.6

### Prioritize (Phase 4 & 5)

1. **Light theme WCAG contrast improvements** (Phase 4)
   - Run all colors through WebAIM Contrast Checker
   - Adjust hex values if needed (prefer darkening accent or text, not lightening background)
   - Test SVG ring stroke against light background
   - Estimated effort: 2–4 hours (color audits + tweaks)
   - Unblocks Phase 5 (users can trust light theme as accessible)

2. **2-column landscape layout** (Phase 4)
   - Add `@media (orientation: landscape)` rule
   - Set flex-direction to row, establish 50/50 split
   - Make history panel scrollable in right column
   - Test on iPad, Android tablet, desktop browser resizing
   - Estimated effort: 4–6 hours (CSS + testing)

3. **Import/export session history** (Phase 5)
   - JSON export: 5-line implementation
   - CSV export: manual header + loop (no library, keep vanilla)
   - JSON import: FileReader + parse
   - CSV import: warn user or skip (too risky without PapaParse)
   - Estimated effort: 6–8 hours (vanilla implementation, no edge case handling for CSV yet)

4. **Clear history with confirmation dialog** (Phase 5)
   - Native `<dialog>` element or custom modal with ARIA
   - Two buttons, focus management
   - Estimated effort: 1–2 hours (straightforward)

### Defer to v0.7

- **Batch import + merge logic** — complexity requires phase-specific research
- **Contrast ratio UI badge** — nice-to-have; prioritize actual contrast fixes first
- **CSV parsing edge cases** — defer to v0.7 if import sees heavy use
- **Scroll position memory in landscape** — nice-to-have; test after layout ships

---

## Dependency Order

```
Phase 4 (Foundation):
  1. Light theme contrast fixes (blocks confident light mode usage)
  2. 2-column landscape layout (unblocks better tablet/desktop UX)

Phase 5 (Features):
  3. Clear history with confirmation (depends on existing history UI)
  4. Import/export (depends on stable history structure)
  5. CSV parsing (depends on import infrastructure)
```

**Why this order:**
- Contrast fixes are lowest risk (color tweaks only) and highest impact (enables light theme for all users)
- Landscape layout is medium risk (CSS, but affects existing portrait layout) and medium impact (better tablet UX)
- Confirmation dialog is low risk (standard pattern) and low impact (just prevents accidents)
- Import/export is high risk (data validation, edge cases) and high impact (user autonomy over data)

---

## Sources

### Layout & Responsive Design
- [Flex Two Columns: Ridiculously easy row and column layouts with Flexbox - DEV Community](https://dev.to/drews256/ridiculously-easy-row-and-column-layouts-with-flexbox-1k01)
- [orientation CSS media feature - MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/orientation)
- [Understanding orientation in CSS Media Queries for Responsive Design](https://www.c-sharpcorner.com/article/understanding-orientation-in-css-media-queries-for-responsive-design/)
- [Media queries | web.dev](https://web.dev/learn/design/media-queries)

### Export Patterns
- [Export JSON to CSV file using Javascript - Medium](https://medium.com/@danny.pule/export-json-to-csv-file-using-javascript-a0b7bc5b00d2)
- [How to Convert JSON Object to CSV in JavaScript - GeeksforGeeks](https://www.geeksforgeeks.org/javascript/how-to-convert-json-object-to-csv-in-javascript/)
- [Export JSON to CSV file using Javascript - GitHub Gist](https://gist.github.com/dannypule/48418b4cd8223104c6c92e3016fc0f61)
- [JSON file to CSV download with vanilla JavaScript](https://openjavascript.info/2022/12/18/json-file-to-csv-download-with-vanilla-javascript/)

### Import Patterns
- [Papa Parse - Powerful CSV Parser for JavaScript](https://www.papaparse.com/)
- [How to Import and Export CSV Files Using JavaScript - SpreadJS](https://developer.mescius.com/blogs/how-to-import-and-export-csv-files-using-javascript)
- [Read CSV Files in JavaScript - Medium](https://medium.com/@ryan_forrester_/read-csv-files-in-javascript-how-to-guide-8d0ac6df082a)
- [How to Convert CSV to JSON in JavaScript - ChangeThisFile](https://changethisfile.com/blog/convert-csv-to-json-javascript)
- [Reading CSV File in JavaScript - ImportCSV](https://www.importcsv.com/blog/reading-csv-javascript)
- [FileReader API and Large File Handling - Treehouse Blog](https://blog.teamtreehouse.com/reading-files-using-the-html5-filereader-api)

### Confirmation Dialogs & Accessibility
- [Mastering Accessible Modals with ARIA and Keyboard Navigation - A11Y Collective](https://www.a11y-collective.com/blog/modal-accessibility/)
- [Mastering Dialog Accessibility - Vispero](https://vispero.com/resources/mastering-dialog-accessibility/)
- [How to Build Accessible Modals with Focus Traps - UXPin](https://www.uxpin.com/studio/blog/how-to-build-accessible-modals-with-focus-traps/)
- [Accessible Modal Dialogs: Focus Trapping and Screen Reader Support - TestParty](https://testparty.ai/blog/modal-dialog-accessibility)
- [Building an Accessible Widget: WAI-ARIA Modal Alert Dialogs - Deque](https://www.deque.com/blog/aria-modal-alert-dialogs-a11y-support-series-part-2/)

### WCAG Contrast & Light Theme
- [WebAIM: Contrast and Color Accessibility](https://webaim.org/articles/contrast/)
- [Color Contrast Requirements: WCAG Guide - TestParty](https://testparty.ai/blog/color-contrast-requirements)
- [Contrast requirements for WCAG 2.2 Level AA - Make Things Accessible](https://www.makethingsaccessible.com/guides/contrast-requirements-for-wcag-2-2-level-aa/)
- [WCAG 2.2 Contrast Ratio Explained: 4.5:1 & 3:1 Rules - Accessibility Assistant](https://accessibilityassistant.com/blog/accessibility-insights/how-to-apply-wcag-22-colour-contrast-accessibility/)
- [Font Contrast Ratios Guide - font-converters.com](https://font-converters.com/accessibility/contrast-ratios)

### Contrast Testing Tools
- [WebAIM: Contrast Checker](https://webaim.org/resources/contrastchecker/)
- [Color Contrast Checker - Level Access](https://www.levelaccess.com/color-contrast-checker-new/)
- [Accessible Web Color Contrast Checker](https://accessibleweb.com/color-contrast-checker/)
- [Color Converter and WCAG Contrast Checker Tool](https://hidekazu-konishi.com/tools/color_converter_contrast_checker_tool.html)
- [Contrast Finder](https://app.contrast-finder.org/?lang=en)

### SVG & Light Theme
- [Accessible SVGs in High Contrast Mode - CSS-Tricks](https://css-tricks.com/accessible-svgs-high-contrast-mode/)
- [Accessible SVGs: Inclusiveness Beyond Patterns - Smashing Magazine](https://www.smashingmagazine.com/2020/03/accessible-svgs-inclusiveness-beyond-patterns/)
- [Designing Icons That Work in Both Light and Dark Mode - DEV Community](https://dev.to/albert_nahas_cdc8469a6ae8/designing-icons-that-work-in-both-light-and-dark-mode-23d6)
- [Give your SVGs light/dark style support - Cassidy James](https://cassidyjames.com/blog/prefers-color-scheme-svg-light-dark/)
- [Adaptive SVG logos for light and dark mode - Getpublii](https://getpublii.com/docs/prepare-svg-for-light-dark-mode.html)
- [Implementing Accessible SVG Elements - A11Y Collective](https://www.a11y-collective.com/blog/svg-accessibility/)
- [prefers-color-scheme CSS media feature - MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/@media/prefers-color-scheme)

### Storage & Error Handling
- [Storage quotas and eviction criteria - MDN Web APIs](https://developer.mozilla.org/en-US/docs/Web/API/Storage_API/Storage_quotas_and_eviction_criteria)
- [Handling localStorage errors (such as quota exceeded errors) - Matteo Mazzarolo](https://mmazzarolo.com/blog/2022-06-25-local-storage-status/)
- [Understanding and Resolving LocalStorage Quota Exceeded Errors - Medium](https://medium.com/@zahidbashirkhan/understanding-and-resolving-localstorage-quota-exceeded-errors-5ce72b1d577a)
- [How to Fix QuotaExceededError in localStorage - BSWEN](https://docs.bswen.com/blog/2026-04-07-fix-quotaexceedederror-localstorage/)

### CSV Parsing Edge Cases
- [Handling Large CSV Files in React - ImportCSV](https://www.importcsv.com/blog/large-csv-react)
- [A Full-Stack Guide to Reading and Wrangling CSV Data with JavaScript - LinuxHaxor](https://linuxhaxor.net/code/read-display-csv-files-using-javascript.html)

