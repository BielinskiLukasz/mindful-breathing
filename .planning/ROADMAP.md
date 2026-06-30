# Mindful Breathing — Roadmap

**Current Status:** v0.5 shipped, v0.6 in planning

---

## Milestones

- ✅ **v0.4** — Session History, Vibration & Theming (shipped 2026-06-05)
- ✅ **v0.5** — Critical Bugs & UX Polish (shipped 2026-06-29)
- 📋 **v0.6** — Landscape Redesign, Light Theme & Advanced History (planned)

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

---

## Planned Phases

### 📋 v0.6 — Landscape Redesign, Light Theme & Advanced History

**Phase 4: 2-Column Landscape Layout Redesign & Light Theme**
- Goal: Complete horizontal view overhaul (history visibility, button placement, grid refinement); improve light theme contrast and color palette for readability and WCAG compliance
- Requirements: Landscape testing (480px to 2560px width), multi-device validation, light theme contrast review per LIGHT-THEME-READABILITY-REPORT.md
- Scope: 1 major layout item + 1 theme improvement item = ~3–4 plans
- Dependencies: Phase 3

**Phase 5: Advanced History Features**
- Goal: Add import/export and safe data deletion with confirmation
- Requirements: JSON/CSV format validation, localStorage quota planning
- Scope: 2 features = ~1–2 plans
- Dependencies: Phase 3

---

## Progress

| Phase | Milestone | Plans Complete | Status | Completed |
|-------|-----------|----------------|--------|-----------|
| 1. Session History & Vibration | v0.4 | 1/1 | Complete | 2026-06-04 |
| 2. Theming & UX Polish | v0.4 | 4/4 | Complete | 2026-06-05 |
| 3. Critical Bugs & UX Polish | v0.5 | 8/8 | Complete | 2026-06-29 |
| 4. 2-Column Landscape Redesign & Light Theme | v0.6 | 0/4 | Planned | — |
| 5. Advanced History Features | v0.6 | 0/2 | Planned | — |

---

**Last updated:** 2026-06-29 after v0.5 milestone completion
