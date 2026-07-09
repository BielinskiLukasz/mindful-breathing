# Requirements: Mindful Breathing v1.1

**Milestone:** v1.1 — Landscape Polish, Custom Presets & Streaks  
**Status:** Planning  
**Created:** 2026-07-10

---

## v1 Requirements

### Landscape Layout

- [ ] **LAYOUT-02**: User sees fullscreen button positioned below the breathing ring in landscape orientation (not in the top-right corner overlay)
  - **Acceptance**: Fullscreen button renders in the left column below the SVG ring in landscape; top-right corner controls are unchanged

- [ ] **LAYOUT-03**: Start and Reset buttons are vertically centered within the right column in landscape orientation
  - **Acceptance**: Controls row is aligned to the vertical midpoint of the right column, not top-aligned or bottom-aligned

- [ ] **LAYOUT-04**: Session info row (cycle count and elapsed duration) appears below the breathing ring in the left column in landscape orientation
  - **Acceptance**: Info row renders beneath the ring in the left column; right column is unaffected

- [ ] **LAYOUT-05**: The 2-column landscape grid has no overlapping components at any common viewport size (320px–2560px wide)
  - **Acceptance**: No component clips, overflows, or overlaps another at 600px, 768px, 1024px, and 1440px landscape viewports

- [ ] **LAYOUT-06**: Compact landscape layout breakpoint uses viewport height (≤500px) not width as the trigger condition
  - **Acceptance**: `@media (orientation: landscape) and (max-height: 500px)` (or equivalent) activates compact layout; width-based queries do not trigger it

### UX Polish

- [ ] **UX-01**: Interactive controls (buttons, toggles) display a visually distinct hover state on pointer devices
  - **Acceptance**: All clickable controls show a hover style change (background, border, brightness, or scale) on `pointer: fine` devices; touch devices are unaffected

- [ ] **UX-02**: Countdown timer digits animate with a smooth transition between number changes
  - **Acceptance**: Each digit change during the 3-second countdown has a visible transition (fade, slide, or scale); no abrupt jump

### Accessibility

- [ ] **A11Y-01**: User can navigate all interactive controls using keyboard only (Tab, Shift+Tab, Enter, Space)
  - **Acceptance**: All buttons, toggles, preset selectors, and sliders are reachable and activatable via keyboard in a logical order

- [ ] **A11Y-02**: Interactive elements have ARIA labels and semantic HTML attributes for screen reader compatibility
  - **Acceptance**: Buttons have `aria-label` or visible text; dynamic state changes (running/stopped, phase name) are announced via `aria-live` or equivalent

- [ ] **A11Y-03**: All interactive elements display a visible focus indicator when focused via keyboard
  - **Acceptance**: `:focus-visible` outline is present and meets minimum 3:1 contrast against adjacent background on all interactive elements

### History Data

- [ ] **HIST-12**: User can import session history from a CSV file previously exported from the app
  - **Acceptance**: Import accepts the app's own CSV export format; valid rows are merged (dedup by date); invalid rows are skipped with feedback; existing sessions are not overwritten

### Custom Preset Builder

- [ ] **PRESET-01**: User can create a custom breathing preset with a name and up to 4 standard phases (Inhale, Hold, Exhale, Hold2) with individual durations
  - **Acceptance**: UI allows naming the preset and enabling/disabling each of the 4 phase types with a duration input per active phase; at least 2 phases required

- [ ] **PRESET-02**: User can select a custom preset for a breathing session (custom presets appear alongside built-in presets in the selector)
  - **Acceptance**: Custom preset buttons appear in the preset row; selecting one loads the custom phases and durations into the session

- [ ] **PRESET-03**: User can edit an existing custom preset's name, active phases, and durations
  - **Acceptance**: Edit UI pre-fills current name, phase toggles, and durations; saving persists changes; session is not affected if running

- [ ] **PRESET-04**: User can delete a custom preset they created
  - **Acceptance**: Delete action requires confirmation; on confirm, preset is removed from localStorage and selector; if active, app falls back to the first built-in preset

- [ ] **PRESET-05**: Custom presets persist in localStorage across browser sessions
  - **Acceptance**: Custom presets survive page reload and browser restart; stored under a dedicated key separate from `savedDurations`

### Streak Tracking

- [ ] **STREAK-01**: User can see their current consecutive-day streak (number of calendar days with at least one completed session)
  - **Acceptance**: Streak counts calendar days (local timezone); a day with ≥1 non-incomplete session increments the streak; missing a day resets it to 0

- [ ] **STREAK-02**: User can see their longest-ever consecutive-day streak in the streak panel
  - **Acceptance**: Longest streak is computed from full session history and displayed alongside the current streak

- [ ] **STREAK-03**: User can see whether they have completed a session today
  - **Acceptance**: Streak panel displays "Done today" (with today's session count) or "Not yet — keep the streak alive" based on today's history

- [ ] **STREAK-04**: User can see their total completed session count in the streak panel
  - **Acceptance**: Count excludes incomplete sessions; updates immediately after each completed session

- [ ] **STREAK-05**: Streak panel opens via a dedicated button that matches the info-panel interaction pattern (button → overlay/panel toggle)
  - **Acceptance**: Streak button and panel follow the same open/close, positioning, and dismiss behavior as the info panel button

- [ ] **STREAK-06**: Streak button displays the current streak count as a glanceable badge (e.g. flame icon + "5")
  - **Acceptance**: Badge reflects the current streak at page load and updates after each completed session; shows "0" or is visually muted when streak is 0

---

## Future Requirements (deferred from v1.1)

- **HIST-05**: User can filter session history by preset — backlog item, not scoped for v1.1
- **HIST-07**: User can delete individual sessions from history — backlog item, not scoped for v1.1
- **PRESET-custom-labels**: User can define phases with arbitrary custom labels — too complex for v1.1; fixed phase types sufficient for all standard techniques
- **STREAK-calendar**: Calendar heatmap visualization of daily session activity — out of scope for v1.1

---

## Out of Scope (v1.1)

| Feature | Reason |
|---------|--------|
| Cloud sync / cross-device streaks | localStorage only — confirmed constraint |
| Multiple user accounts | Single-user per browser |
| Vibration API on Android | Samsung/Android OS restriction; code retained but UI hidden |
| Custom phase labels (arbitrary text) | Fixed Inhale/Hold/Exhale/Hold2 covers all standard techniques |
| Full WCAG AAA compliance | AA is the target; AAA deferred |

---

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| LAYOUT-02 | Phase 7 | Pending |
| LAYOUT-03 | Phase 7 | Pending |
| LAYOUT-04 | Phase 7 | Pending |
| LAYOUT-05 | Phase 7 | Pending |
| LAYOUT-06 | Phase 7 | Pending |
| UX-01 | Phase 7 | Pending |
| UX-02 | Phase 7 | Pending |
| A11Y-01 | Phase 8 | Pending |
| A11Y-02 | Phase 8 | Pending |
| A11Y-03 | Phase 8 | Pending |
| HIST-12 | Phase 8 | Pending |
| PRESET-01 | Phase 9 | Pending |
| PRESET-02 | Phase 9 | Pending |
| PRESET-03 | Phase 9 | Pending |
| PRESET-04 | Phase 9 | Pending |
| PRESET-05 | Phase 9 | Pending |
| STREAK-01 | Phase 10 | Pending |
| STREAK-02 | Phase 10 | Pending |
| STREAK-03 | Phase 10 | Pending |
| STREAK-04 | Phase 10 | Pending |
| STREAK-05 | Phase 10 | Pending |
| STREAK-06 | Phase 10 | Pending |

**Coverage:** 0/22 v1 requirements complete (0%) — 22/22 requirements mapped to phases

---

*Created: 2026-07-10 — v1.1 milestone planning*  
*Traceability updated: 2026-07-10 — roadmap finalized*
