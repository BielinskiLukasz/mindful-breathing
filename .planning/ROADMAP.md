# Mindful Breathing — Roadmap

**Current Status:** v1.0 shipped 2026-07-04 — planning v1.1

---

## Milestones

- ✅ **v0.4** — Session History, Vibration & Theming (shipped 2026-06-05)
- ✅ **v0.5** — Critical Bugs & UX Polish (shipped 2026-06-29)
- ✅ **v0.6** — Layout & History (shipped 2026-07-01)
- ✅ **v1.0** — Bug Fixes & Visual Polish (shipped 2026-07-04)
- 📋 **v1.1+** — (planning not yet started — run `/gsd-new-milestone`)

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

## Next Milestone: v1.1

Planning not yet started. Run `/gsd-new-milestone` to define requirements and roadmap.

**Candidates from backlog:**
- CSV import (HIST-12) — RFC 4180 edge cases; deferred from v1.0
- Full accessibility: keyboard nav, ARIA labels, focus indicators (A11Y-01–03)
- Individual session delete (HIST-07)
- History filter by preset (HIST-05)

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

---

**Last updated:** 2026-07-04 — v1.0 shipped
