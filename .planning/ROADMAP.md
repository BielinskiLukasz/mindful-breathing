# Mindful Breathing — Roadmap

**Current Status:** v1.1 — Landscape Polish, Custom Presets & Streaks (in progress)

---

## Milestones

- ✅ **v0.4** — Session History, Vibration & Theming (shipped 2026-06-05)
- ✅ **v0.5** — Critical Bugs & UX Polish (shipped 2026-06-29)
- ✅ **v0.6** — Layout & History (shipped 2026-07-01)
- ✅ **v1.0** — Bug Fixes & Visual Polish (shipped 2026-07-04)
- 🚧 **v1.1** — Landscape Polish, Custom Presets & Streaks (in progress — Phases 7–10)

---

## Shipped Milestones

<details>
<summary>✅ v0.4 (Phases 1–2) — SHIPPED 2026-06-05</summary>

- [x] Phase 1: Session History & Vibration (1 plan) — completed 2026-06-04
- [x] Phase 2: Theming & UX Polish (4 plans) — completed 2026-06-05

**What Shipped:**

- Paginated session history with preset badge and configurable storage cap (default 1,000 entries)
- Vibration reliability verified correct per W3C spec
- Light/dark theme toggle with 220ms smooth transitions
- Responsive typography with CSS clamp() (320px to 4K, no breakpoint jumps)
- Landscape 50/50 flexbox layout with scrollable history and fixed controls

**Archive:** `.planning/milestones/v0.4-ROADMAP.md`

</details>

<details>
<summary>✅ v0.5 (Phase 3) — SHIPPED 2026-06-29</summary>

- [x] Phase 3: Critical Bugs & UX Polish (8 plans) — completed 2026-06-29

**What Shipped:**

- Countdown timer orphan prevention (`cancelCountdown()` in `stop()`)
- `DURATION_RANGE` constant enforcing 1–300s duration bounds across all call sites
- localStorage `QuotaExceededError` surfaced with inline warning banner
- Button `:active` enhanced with inset shadow + semi-transparent overlay (CSS-only)
- Phase transition ring flash animation (200ms brightness pulse) + first-visit gesture hint
- Duration input error flash (400ms red highlight) for invalid values
- Countdown audio beeps (350 Hz), Skip button, and ESC key handler
- Vibration UI hidden as pragmatic resolution to Samsung/Android OS restriction

**Archive:** `.planning/milestones/v0.5-ROADMAP.md`

</details>

<details>
<summary>✅ v0.6 (Phases 4–5) — SHIPPED 2026-07-01</summary>

- [x] Phase 4: Layout & Light Theme (2 plans) — completed 2026-06-30
- [x] Phase 5: History Data Management (1 plan) — completed 2026-06-30

**What Shipped:**

- CSS Grid 2-column landscape layout (ring left, history right) on 600px+ landscape screens
- WCAG AA light theme contrast: accent #a0662e (5.2:1), textSoft #6b6058 (5.3:1) on cream
- Per-phase atmospheric bgLight/accentLight colors for all PRESETS (sage/lavender/sky/cream)
- JSON and CSV session history export via Blob download
- JSON import with merge/dedup/validation and feedback flash
- Native `<dialog>` clear-history confirmation with ESC and backdrop dismiss

**Archive:** `.planning/milestones/v0.6-ROADMAP.md`

</details>

<details>
<summary>✅ v1.0 (Phase 6) — SHIPPED 2026-07-04</summary>

- [x] Phase 6: Bug Fixes & Visual Polish (2 plans) — completed 2026-07-04

**What Shipped:**

- Smooth 220ms background transition on phase change — `background` shorthand in body CSS (VISUAL-01)
- Compact landscape height fix — `height: auto; min-height: 100vh` on tall phones ≤500px (LAYOUT-01)
- App version v1.0 in info panel footer — `APP_VERSION` constant JS-injected at init (INFO-01)
- Incomplete session tracking — Pause saves `{incomplete: true}` when ≥1 full cycle; rendered at 0.6 opacity with italic suffix (TRACK-01)
- Stop button renamed to Pause — reflects actual app behavior

**Archive:** `.planning/milestones/v1.0-ROADMAP.md`

</details>

---

## 🚧 v1.1 — Landscape Polish, Custom Presets & Streaks

**Milestone Goal:** Finish the landscape layout, add a custom preset builder, and introduce streak tracking for daily habit formation.

**Phases:** 7–10 | **Requirements:** 22 v1

### Phases

- [x] **Phase 7: Landscape Layout & UX Polish** - Fix landscape overlaps, reposition elements per column, and add hover/countdown animations
- [ ] **Phase 8: Accessibility & CSV Import** - Full keyboard navigation, ARIA labels, visible focus indicators, and CSV session history import
- [ ] **Phase 9: Custom Preset Builder** - Create, edit, and delete custom breathing presets that persist across sessions
- [ ] **Phase 10: Streak Tracking** - Daily streak counter with current streak, longest streak, today status, and total session count

---

## Phase Details

### Phase 7: Landscape Layout & UX Polish
**Goal**: Users experience a clean, overlap-free landscape layout with controls in their expected columns and visual polish on hover and countdown
**Depends on**: Phase 6
**Requirements**: LAYOUT-02, LAYOUT-03, LAYOUT-04, LAYOUT-05, LAYOUT-06, UX-01, UX-02
**Success Criteria** (what must be TRUE):
  1. Fullscreen button appears below the breathing ring in the left column in landscape — not in the top-right corner overlay
  2. Start/Reset buttons sit at the vertical midpoint of the right column in landscape; session info (cycle count and elapsed time) appears below the ring in the left column
  3. No layout element clips, overflows, or overlaps another in landscape at 600px, 768px, 1024px, and 1440px wide viewports
  4. Compact landscape activates when viewport height is ≤500px regardless of width
  5. Interactive controls show a visible hover style on pointer devices; the 3-second countdown digits animate smoothly between number changes
**Plans**: 2 plans
Plans:
- [ ] 07-01-PLAN.md — HTML/CSS layout: fullscreenBtn into cornerControls, landscape info row below ring, controls at 50% column height
- [ ] 07-02-PLAN.md — JS landscape element sync, uniform hover states, countdown animation verification
**UI hint**: yes

### Phase 8: Accessibility & CSV Import
**Goal**: Users can navigate the entire app with keyboard alone, screen readers can follow the session state, and session history can be restored from a previously exported CSV file
**Depends on**: Phase 7
**Requirements**: A11Y-01, A11Y-02, A11Y-03, HIST-12
**Success Criteria** (what must be TRUE):
  1. Every button, toggle, preset selector, and slider is reachable and activatable via Tab / Shift+Tab / Enter / Space in a logical order with no keyboard traps
  2. Screen readers announce meaningful labels for all controls, the current phase name, and running/stopped state changes via aria-live or equivalent
  3. Every interactive element shows a visible focus ring when keyboard-focused, meeting at least 3:1 contrast against the adjacent background
  4. User can import a CSV file previously exported from the app; valid rows merge into history, duplicates are skipped, and invalid rows produce visible feedback
**Plans**: TBD
**UI hint**: yes

### Phase 9: Custom Preset Builder
**Goal**: Users can create, name, edit, and delete their own breathing presets, which persist across sessions and appear alongside built-in presets
**Depends on**: Phase 8
**Requirements**: PRESET-01, PRESET-02, PRESET-03, PRESET-04, PRESET-05
**Success Criteria** (what must be TRUE):
  1. User can create a custom preset with a name and at least 2 of the 4 standard phases (Inhale, Hold, Exhale, Hold2), each with its own duration
  2. Custom presets appear in the preset selector alongside Relax, Box, and 4-7-8 and can be selected to start a session
  3. User can edit a custom preset's name, active phases, and durations; changes persist and do not interrupt a running session
  4. User can delete a custom preset with a confirmation step; if the deleted preset was active, the app falls back to the first built-in preset
  5. Custom presets survive page reload and browser restart (stored under a dedicated localStorage key)
**Plans**: TBD
**UI hint**: yes

### Phase 10: Streak Tracking
**Goal**: Users can see their daily practice consistency — current streak, longest ever, today's status, and total completed sessions — at a glance from the main screen
**Depends on**: Phase 9
**Requirements**: STREAK-01, STREAK-02, STREAK-03, STREAK-04, STREAK-05, STREAK-06
**Success Criteria** (what must be TRUE):
  1. Streak button visible on the main screen shows the current streak count (e.g., flame icon + "5") at page load and updates immediately after each completed session; shows "0" or is visually muted when streak is 0
  2. Opening the streak panel reveals current streak, longest-ever streak, today's completion status ("Done today" with count, or "Not yet" prompt), and total completed session count excluding incomplete sessions
  3. Streak increments for each consecutive calendar day (local timezone) with at least one non-incomplete session and resets to 0 after any missed day
  4. Streak panel opens and dismisses via the same button-to-overlay interaction pattern as the existing info panel
**Plans**: TBD
**UI hint**: yes

---

## Progress

| Phase | Milestone | Plans Complete | Status | Completed |
|-------|-----------|----------------|--------|-----------|
| 1. Session History & Vibration | v0.4 | 1/1 | Complete | 2026-06-04 |
| 2. Theming & UX Polish | v0.4 | 4/4 | Complete | 2026-06-05 |
| 3. Critical Bugs & UX Polish | v0.5 | 8/8 | Complete | 2026-06-29 |
| 4. Layout & Light Theme | v0.6 | 2/2 | Complete | 2026-06-30 |
| 5. History Data Management | v0.6 | 1/1 | Complete | 2026-06-30 |
| 6. Bug Fixes & Visual Polish | v1.0 | 2/2 | Complete | 2026-07-04 |
| 7. Landscape Layout & UX Polish | v1.1 | 2/2 | Complete | 2026-07-14 |
| 8. Accessibility & CSV Import | v1.1 | 0/TBD | Not started | - |
| 9. Custom Preset Builder | v1.1 | 0/TBD | Not started | - |
| 10. Streak Tracking | v1.1 | 0/TBD | Not started | - |

---

**Last updated:** 2026-07-14 — Phase 7 complete
