# Requirements: Mindful Breathing v1.1

**Milestone:** v1.1 — Landscape Polish, Custom Presets & Streaks  
**Status:** Complete  
**Created:** 2026-07-10

---

## v1 Requirements

### Landscape Layout

- [x] **LAYOUT-02**: User sees fullscreen button positioned below the breathing ring in landscape orientation (not in the top-right corner overlay)
  - **Acceptance**: Fullscreen button renders in the left column below the SVG ring in landscape; top-right corner controls are unchanged

- [x] **LAYOUT-03**: Start and Reset buttons are vertically centered within the right column in landscape orientation
  - **Acceptance**: Controls row is aligned to the vertical midpoint of the right column, not top-aligned or bottom-aligned

- [x] **LAYOUT-04**: Session info row (cycle count and elapsed duration) appears below the breathing ring in the left column in landscape orientation
  - **Acceptance**: Info row renders beneath the ring in the left column; right column is unaffected

- [x] **LAYOUT-05**: The 2-column landscape grid has no overlapping components at any common viewport size (320px–2560px wide)
  - **Acceptance**: No component clips, overflows, or overlaps another at 600px, 768px, 1024px, and 1440px landscape viewports

- [x] **LAYOUT-06**: Compact landscape layout breakpoint uses viewport height (≤500px) not width as the trigger condition
  - **Acceptance**: `@media (orientation: landscape) and (max-height: 500px)` (or equivalent) activates compact layout; width-based queries do not trigger it

### UX Polish

- [x] **UX-01**: Interactive controls (buttons, toggles) display a visually distinct hover state on pointer devices
  - **Acceptance**: All clickable controls show a hover style change (background, border, brightness, or scale) on `pointer: fine` devices; touch devices are unaffected

- [x] **UX-02**: Countdown timer digits animate with a smooth transition between number changes
  - **Acceptance**: Each digit change during the 3-second countdown has a visible transition (fade, slide, or scale); no abrupt jump

### Accessibility

- [x] **A11Y-01**: User can navigate all interactive controls using keyboard only (Tab, Shift+Tab, Enter, Space)
  - **Acceptance**: All buttons, toggles, preset selectors, and sliders are reachable and activatable via keyboard in a logical order

- [x] **A11Y-02**: Interactive elements have ARIA labels and semantic HTML attributes for screen reader compatibility
  - **Acceptance**: Buttons have `aria-label` or visible text; dynamic state changes (running/stopped, phase name) are announced via `aria-live` or equivalent

- [x] **A11Y-03**: All interactive elements display a visible focus indicator when focused via keyboard
  - **Acceptance**: `:focus-visible` outline is present and meets minimum 3:1 contrast against adjacent background on all interactive elements

### History Data

- [x] **HIST-12**: User can import session history from a CSV file previously exported from the app
  - **Acceptance**: Import accepts the app's own CSV export format; valid rows are merged (dedup by date); invalid rows are skipped with feedback; existing sessions are not overwritten

### Custom Preset Builder

- [x] **PRESET-01**: User can create a custom breathing preset with a name and up to 4 standard phases (Inhale, Hold, Exhale, Hold2) with individual durations
  - **Acceptance**: UI allows naming the preset and enabling/disabling each of the 4 phase types with a duration input per active phase; at least 2 phases required

- [x] **PRESET-02**: User can select a custom preset for a breathing session (custom presets appear alongside built-in presets in the selector)
  - **Acceptance**: Custom preset buttons appear in the preset row; selecting one loads the custom phases and durations into the session

- [x] **PRESET-03**: User can edit an existing custom preset's name, active phases, and durations
  - **Acceptance**: Edit UI pre-fills current name, phase toggles, and durations; saving persists changes; session is not affected if running

- [x] **PRESET-04**: User can delete a custom preset they created
  - **Acceptance**: Delete action requires confirmation; on confirm, preset is removed from localStorage and selector; if active, app falls back to the first built-in preset

- [x] **PRESET-05**: Custom presets persist in localStorage across browser sessions
  - **Acceptance**: Custom presets survive page reload and browser restart; stored under a dedicated key separate from `savedDurations`

### Streak Tracking

- [x] **STREAK-01**: User can see their current consecutive-day streak (number of calendar days with at least one completed session)
  - **Acceptance**: Streak counts calendar days (local timezone); a day with ≥1 non-incomplete session increments the streak; missing a day resets it to 0

- [x] **STREAK-02**: User can see their longest-ever consecutive-day streak in the streak panel
  - **Acceptance**: Longest streak is computed from full session history and displayed alongside the current streak

- [x] **STREAK-03**: User can see whether they have completed a session today
  - **Acceptance**: Streak panel displays "Done today" (with today's session count) or "Not yet — keep the streak alive" based on today's history

- [x] **STREAK-04**: User can see their total completed session count in the streak panel
  - **Acceptance**: Count excludes incomplete sessions; updates immediately after each completed session

- [x] **STREAK-05**: Streak panel opens via a dedicated button that matches the info-panel interaction pattern (button → overlay/panel toggle)
  - **Acceptance**: Streak button and panel follow the same open/close, positioning, and dismiss behavior as the info panel button

- [x] **STREAK-06**: Streak button displays a glanceable badge indicating whether a streak is active
  - **Acceptance**: Badge shows 🔥 emoji with full opacity when streak ≥ 1, muted opacity (0.45) when streak is 0; updates at page load and after each completed session. Full streak count visible in streak panel. *(Design updated via Quick Task 260825-uo5: flame-only — button 34×34px too narrow for "🔥 N" text)*

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
| LAYOUT-02 | Phase 7 | Complete |
| LAYOUT-03 | Phase 7 | Complete |
| LAYOUT-04 | Phase 7 | Complete |
| LAYOUT-05 | Phase 7 | Complete |
| LAYOUT-06 | Phase 7 | Complete |
| UX-01 | Phase 7 | Complete |
| UX-02 | Phase 7 | Complete |
| A11Y-01 | Phase 8 | Complete |
| A11Y-02 | Phase 8 | Complete |
| A11Y-03 | Phase 8 | Complete |
| HIST-12 | Phase 8 | Complete |
| PRESET-01 | Phase 9 | Complete |
| PRESET-02 | Phase 9 | Complete |
| PRESET-03 | Phase 9 | Complete |
| PRESET-04 | Phase 9 | Complete |
| PRESET-05 | Phase 9 | Complete |
| STREAK-01 | Phase 10 | Complete |
| STREAK-02 | Phase 10 | Complete |
| STREAK-03 | Phase 10 | Complete |
| STREAK-04 | Phase 10 | Complete |
| STREAK-05 | Phase 10 | Complete |
| STREAK-06 | Phase 10 | Complete |

**Coverage:** 22/22 v1 requirements complete (100%)

---

*Created: 2026-07-10 — v1.1 milestone planning*  
*Traceability updated: 2026-08-27 — milestone audit complete; all requirements satisfied*
