# Mindful Breathing — Roadmap

**Current Status:** v1.0 in planning

---

## Milestones

- ✅ **v0.4** — Session History, Vibration & Theming (shipped 2026-06-05)
- ✅ **v0.5** — Critical Bugs & UX Polish (shipped 2026-06-29)
- ✅ **v0.6** — Layout & History (shipped 2026-07-01)
- 📋 **v1.0** — Bug Fixes & Visual Polish (in progress)
- 📋 **v1.1+** — (planned)

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

---

## Current Milestone: v1.0

### 📋 v1.0 — Bug Fixes & Visual Polish (IN PROGRESS)

**Status:** Planning  
**Phases:** 1 (Phase 6)  
**Requirements:** 4 v1  
**Target Ship Date:** TBD

#### Phases

- [ ] **Phase 6: Bug Fixes & Visual Polish** — Fix landscape compact layout detection, smooth background transitions, track unfinished sessions, display app version

#### Phase Details

### Phase 6: Bug Fixes & Visual Polish

**Goal:** Fix critical landscape layout bugs, enhance visual transitions, track incomplete sessions, and display app version information

**Depends on:** Phase 5 (v0.6)

**Requirements:** LAYOUT-01, VISUAL-01, TRACK-01, INFO-01

**Success Criteria** (what must be TRUE):
1. Landscape mode on tall phones (height ≤ 500px) displays correct compact layout without content overflow
2. Background color transitions smoothly (220ms) when phase changes in dark mode instead of jumping abruptly
3. Unfinished sessions appear in history list with distinct "Incomplete" styling showing cycle count and elapsed time
4. App version (v1.0) is visible in the info panel footer
5. All existing features (light theme, export/import, controls) continue to work without regression

**Plans:** TBD

---

## Progress

| Phase | Milestone | Plans Complete | Status | Completed |
|-------|-----------|----------------|--------|-----------|
| 1. Session History & Vibration | v0.4 | 1/1 | Complete | 2026-06-04 |
| 2. Theming & UX Polish | v0.4 | 4/4 | Complete | 2026-06-05 |
| 3. Critical Bugs & UX Polish | v0.5 | 8/8 | Complete | 2026-06-29 |
| 4. Layout & Light Theme | v0.6 | 2/2 | Complete | 2026-06-30 |
| 5. History Data Management | v0.6 | 1/1 | Complete | 2026-06-30 |
| 6. Bug Fixes & Visual Polish | v1.0 | 0/? | Not started | — |

---

**Last updated:** 2026-07-03 — v1.0 roadmap created
