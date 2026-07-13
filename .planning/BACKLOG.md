# Backlog

Ideas and scope items captured outside the active roadmap. Anything here is *not* in v1 — it has either been deferred by explicit decision, surfaced during UAT, or earmarked for a later milestone. Items graduate to a `ROADMAP.md` phase when picked up (`/gsd-review-backlog` to promote, `/gsd-phase add` to materialize).

Last updated: 2026-07-13 (all items reformatted to standard backlog template)
Last assigned ID: **B-024** — next new item must be **B-025**

---

## How to use this file

- **Adding an item:** increment the "Last assigned ID" counter at the top, then drop a new `### B-NNN` block with Status / Earliest sensible slot / What / Why / Open questions / Implementation notes. IDs are monotonic and never reused — even if the previous entry was promoted or removed.
- **Promoting an item:** `/gsd-review-backlog` (interactive) — moves a chosen item into the active milestone roadmap. Or manually run `/gsd-phase add` and reference the backlog ID in the phase description.
- **Removing an item:** delete the block or move it under a `## Rejected` heading with a one-line rationale (decisions cost; keep the rationale).
- **Memory ↔ backlog:** memory captures "this idea exists and here's the context"; this file is the project-level decision queue. Memory is the source for cross-session continuity; this file is the source for milestone planning. Update both when an item lands.

## Related

- `ROADMAP.md` — active milestone phases
- `milestones/v1.0-REQUIREMENTS.md` — v1.0 archived requirements (all 51 complete)
- `PROJECT.md` — core constraints (single subject v1, no build step, no frameworks)
- `CLAUDE.md` — v1/v2 split rules

---

## Staged for v1.1

Promoted during 2026-07-10 backlog review. Include in v1.1 milestone planning.

---

### B-004 · Improve button hover states

**Status:** promoted → v1.1
**Earliest sensible slot:** v1.1 — no prerequisites

**What:** Add and refine CSS hover interactions for all actionable controls — preset buttons, start/stop, reset, theme toggle, sound/vibe toggles. Transitions should be short (100–150 ms) and consistent across the UI.

**Why:** Several controls currently have no hover state or only a browser-default outline. Visual hover feedback confirms interactivity, reduces mis-taps on touch-adjacent pointer devices (styluses, trackpads), and makes the app feel less prototype-grade.

**Implementation notes:**

- All styles live in `index.html` `<style>`. Add `:hover` rules with background or opacity transitions to each control family.
- Wrap hover rules in `@media (hover: hover)` so they do not apply on pure touch screens and leave no sticky-hover residue.

---

### B-005 · Refine countdown timing animations

**Status:** promoted → v1.1
**Earliest sensible slot:** v1.1 — no prerequisites

**What:** Improve the 3-second countdown overlay that appears between pressing Start and the first breath phase — smoother digit transitions, better fade-in/out, and tighter timing so the overlay exits exactly on beat.

**Why:** The current countdown is functional but abrupt. A polished entry animation signals the transition from rest to active session more clearly and sets the calm tone the app aims for.

**Implementation notes:**

- Countdown overlay is shown and hidden in `start()` via `setTimeout` chains. Timing constants live in the `COUNTDOWN` config object.
- Add CSS `@keyframes` or `transition` on the overlay element for digit scale/fade. Keep total countdown duration unchanged — only presentation changes.

---

### B-007 · Move fullscreen button below breathing ring

**Status:** promoted → v1.1
**Earliest sensible slot:** v1.1 (B-001 basic landscape 2-col layout shipped in v0.6)

**What:** Relocate the fullscreen toggle button from the top-right corner to the left column, below the breathing ring, in landscape mode. Portrait layout is unaffected.

**Why:** The top-right position competes visually with the ring and is awkward to reach on mobile in landscape. Placing it below the ring keeps it in the ring's natural zone and out of the way during sessions.

**Implementation notes:**

- Change DOM order in `index.html` or use `order` in the landscape CSS grid to reposition the button without duplicating markup.
- Cross-check with the fullscreen icon `aria-label` — its purpose does not change, only its position.

---

### B-008 · Vertically center Start/Reset controls in landscape

**Status:** promoted → v1.1
**Earliest sensible slot:** v1.1 (B-001 done)

**What:** Vertically center the Start and Reset buttons within the right column in landscape mode. Currently they cluster at the top while the ring fills the left column, making the layout look top-heavy.

**Why:** Balanced vertical alignment makes the two-column layout feel intentional and symmetrical, matching the geometric character of the breathing ring.

**Implementation notes:**

- The right column is a flex or grid container in the landscape media query (`max-height: 500px`). Add `align-items: center` (flexbox) or `align-content: center` (grid) to the right column container.

---

### B-009 · Reposition session information row in landscape

**Status:** promoted → v1.1
**Earliest sensible slot:** v1.1 (B-001 done)

**What:** Move the session-info row (cycle count, elapsed time) from its current position to below the breathing ring in landscape layout, so it sits inside the left column with the ring.

**Why:** Session info describes the in-progress session and belongs visually near the ring rather than above or between the two columns. Current placement breaks the visual narrative of the ring being the primary focus.

**Implementation notes:**

- Change DOM position or apply `order` in the landscape media query. The session-info element is rendered inline in the HTML layout.
- Confirm the row still reflows correctly at the 360px portrait breakpoint after the DOM order change.

---

### B-010 · Refine landscape grid layout

**Status:** promoted → v1.1
**Earliest sensible slot:** v1.1, after B-007, B-008, and B-009 land

**What:** Finalize the 2-column landscape layout holistically: eliminate any remaining component overlaps, lock in column proportions, and audit spacing on real device sizes after the individual repositioning items (B-007/B-008/B-009) are applied.

**Why:** Repositioning individual elements in isolation risks introducing new overlaps or proportion imbalances. A final holistic pass over the grid confirms nothing collides and proportions feel right across target devices.

**Open questions when this gets planned:**

- Column split ratio — 55/45 (ring dominant) or 50/50? Decide by visual testing on iPhone SE landscape and a wide-format tablet.
- Does the preset selector row belong in the left or right column in landscape?

**Implementation notes:**

- Audit the `@media (max-height: 500px) and (orientation: landscape)` block in `index.html` `<style>`.
- Test on iPhone SE (375px × 667px rotated), iPhone 14 Pro Max (430px × 932px rotated), and a 768px landscape tablet.
- Adjust `grid-template-columns`, gap, and padding as needed; avoid magic-number px values — use `fr` units or `min-content`/`max-content`.

---

### B-014 · Import history from exported files

**Status:** promoted → v1.1
**Earliest sensible slot:** v1.1 (export history shipped in v0.6, B-012 done)

**What:** Allow users to import a previously exported JSON session history file back into the app. The import replaces or merges local history and enforces the 14-entry cap defined in `SESSION` config.

**Why:** Export shipped in v0.6 without a matching import path. Users who clear localStorage, reinstall, or switch devices lose their history even though they have a backup file. Import closes the data-portability loop.

**Open questions when this gets planned:**

- Merge vs. replace: if the imported file contains records that overlap with local ones (same `date` value), which wins? Merge by de-duplicating on `date`, or prompt the user to choose?
- Validation strictness: reject the entire file on any malformed record, or skip bad records and import the rest?

**Implementation notes:**

- Add a hidden `<input type="file" accept=".json">` triggered by a visible "Import" button in the history panel.
- Parse JSON, validate each record against `{ date, durationMs, cycles }` shape, then persist via `saveHistory()`.
- Cap at `SESSION.maxHistory` (currently 14) entries after merge — drop oldest if over the limit.

---

### B-016 · Fix landscape media-query breakpoint logic

**Status:** promoted → v1.1
**Earliest sensible slot:** v1.1, after B-010 is stable

**What:** Switch the landscape-layout activation condition from a viewport-width check to a viewport-height check (≤500px after device rotation), so tall phones like iPhone 14 Pro Max correctly receive the compact landscape layout.

**Why:** The current width-based breakpoint fires incorrectly on tall phones in landscape — they have wide viewports but still need the compact treatment. The result is that iPhone 14 Pro Max users see the portrait layout stacked vertically in a sideways screen.

**Implementation notes:**

- In `index.html` `<style>`, update the landscape media query from `@media (max-width: NNNpx) and (orientation: landscape)` to `@media (max-height: 500px) and (orientation: landscape)`.
- Cross-check against the existing 480px and 360px portrait breakpoints to ensure no regressions on narrow portrait devices.
- The 500px height threshold aligns with the `UI.landscapeMaxHeight` constant — update that constant if one exists, rather than hardcoding the value in CSS.

---

### B-017 · Add keyboard focus states

**Status:** promoted → v1.1
**Earliest sensible slot:** v1.1 — no prerequisites

**What:** Add visible, high-contrast `:focus-visible` outlines to all interactive controls so keyboard users can clearly see which element has focus. Currently focus styles are browser-default or suppressed by global resets.

**Why:** Visible focus is an accessibility baseline (WCAG 2.4.7 — Focus Visible). Without it, keyboard-only users cannot tell where they are in the UI, making the app effectively unusable without a pointer device.

**Implementation notes:**

- Add `*:focus-visible { outline: 2px solid var(--accent); outline-offset: 2px; }` in `index.html` `<style>`.
- Suppress default outline on non-keyboard interaction: `*:focus:not(:focus-visible) { outline: none; }`.
- Test by tabbing through all controls in Chrome and Firefox without a mouse.

---

### B-018 · Implement logical tab navigation

**Status:** promoted → v1.1
**Earliest sensible slot:** v1.1, after B-017 (focus visibility must be in place to audit tab order)

**What:** Audit and fix the keyboard tab order so focus moves in a logical reading sequence across all controls: preset selector → duration sliders → start/stop → reset → sound/vibe toggles → fullscreen → theme toggle.

**Why:** Tab order follows DOM order, which may not match visual layout — especially after landscape CSS reordering via `order` property (visual ≠ DOM order). An illogical tab sequence confuses keyboard and screen-reader users.

**Open questions when this gets planned:**

- Should duration sliders be in the tab order during an active session, or skipped? (They are not actionable while breathing.)

**Implementation notes:**

- Audit DOM order in `index.html`; reorder elements if visual layout allows it, rather than patching with `tabindex`.
- Avoid positive `tabindex` values — use `tabindex="0"` only to add natively non-focusable elements. Use `tabindex="-1"` to temporarily remove sliders from tab order during active sessions.

---

### B-019 · Add ARIA labels and accessibility attributes

**Status:** promoted → v1.1
**Earliest sensible slot:** v1.1 — no prerequisites

**What:** Add `aria-label`, `aria-live`, and `role` attributes to the SVG ring, phase label, countdown overlay, and any icon-only buttons so that screen readers can announce breathing state and session progress.

**Why:** The breathing ring, progress indicator, and phase state are entirely visual. A screen reader user hears nothing meaningful during a session. ARIA attributes expose this dynamic state without changing the visual design.

**Implementation notes:**

- SVG ring: add `role="img"` and `aria-label="Breathing progress"` (or update dynamically with phase + progress percentage).
- Phase label: add `aria-live="polite"` so phase transitions are announced after current speech ends.
- Countdown overlay: add `aria-live="assertive"` so the 3-2-1 countdown interrupts and is read immediately.
- Icon-only buttons (fullscreen, theme toggle): add descriptive `aria-label` attributes.

---

## Not scheduled

---

### B-020 · Add landscape layout position toggle

**Status:** captured · not scheduled
**Earliest sensible slot:** post-v1.1 — landscape layout must be stable (B-010, B-016) before adding a toggle on top of it

**What:** Add a setting that lets users choose whether the breathing ring appears on the left or right side in landscape mode. Default: ring left.

**Why:** Left-handed users and certain grip styles may prefer the ring on the right so the dominant thumb can reach the Start/Stop controls more naturally. A toggle costs little and eliminates a friction point for a meaningful subset of mobile users.

**Open questions when this gets planned:**

- Persist as a named boolean in `STORAGE_KEY` settings, or infer from device handedness API (non-standard, avoid)?
- Does the toggle appear in a settings panel or as a quick-tap icon in the landscape layout itself?

**Implementation notes:**

- Store as a boolean (`ringOnRight`) in `saveSettings()` / `loadSettings()`.
- CSS: toggle a `data-ring-side="right"` attribute on `appEl`; the landscape media query swaps `grid-template-columns` order or uses `flex-direction: row-reverse` based on that attribute.
- Settings UI: a two-option segmented toggle (Left / Right) added to the existing settings panel.

---

### B-022 · Store extended history metadata

**Status:** captured · not scheduled
**Earliest sensible slot:** post-v1.1 — no migration path exists for existing records; define one before adding new fields

**What:** Extend the history record shape to include the preset used (`preset` key) and optionally per-phase durations alongside the existing `{ date, durationMs, cycles }`. Display preset name in the history list UI.

**Why:** History currently records only total duration and cycle count. Knowing which preset was used (Relax / Box / 4-7-8) makes history entries meaningful at a glance and lays the groundwork for future analytics or streak features.

**Open questions when this gets planned:**

- Backwards compatibility: old records lack `preset`. Display as "Unknown" in history UI, or backfill using `savedDurations` heuristics?
- Should per-phase durations be stored as an object keyed by phase label, or as an ordered array? Array is more compact; object is self-documenting.
- Does storing more data per record require bumping the 14-entry cap to avoid filling localStorage faster?

**Implementation notes:**

- Modify the record written in `saveHistory()` to include `preset: activePresetKey`.
- Update `renderHistory()` to display the preset name alongside duration and cycle count.
- Consider a `HISTORY_KEY` schema version field to detect and handle legacy records gracefully.

---

### B-024 · Dedicated layout for ultra-narrow phones (<360px)

**Status:** captured · not scheduled
**Earliest sensible slot:** post-v1.1; low priority until user reports surface on specific devices

**What:** Add a targeted CSS layout for portrait viewports narrower than 360px (e.g., older budget Android phones at 320px width) where the current layout clips the ring or overflows the duration sliders.

**Why:** The 360px breakpoint is already the narrowest, but some phones at 320px still exhibit a clipped ring SVG or horizontally scrolling sliders. A dedicated suB-0360px rule eliminates these layout breaks without touching the broader responsive system.

**Open questions when this gets planned:**

- Which specific device(s) are the trigger? Knowing the exact viewport lets us write a precise breakpoint rather than guessing.
- Can the ring be scaled down purely via CSS (SVG `viewBox` + `width: 100%`) or does `RING.radius` need a smaller value passed in from JS?

**Implementation notes:**

- Add `@media (max-width: 359px) and (orientation: portrait)` block to `index.html` `<style>`.
- Key targets: ring SVG sizing (ensure `width: 100%; height: auto` is set so `viewBox` scales it), slider track `min-width` removal, phase-label `font-size` reduction.
- Avoid changing `RING` config constants — use CSS scaling so the JS geometry calculations remain consistent.

---

## Removed

B-002 · Fix Vibration API on Android/Chrome — removed 2026-07-10. Android OS suppresses `navigator.vibrate()` at the system level on Samsung/Android devices; vibration toggle already hidden via `display:none`; not actionable at the app level.
