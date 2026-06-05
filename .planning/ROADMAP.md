# Mindful Breathing — Roadmap

**Current Status:** v0.4 shipped, v0.5 in planning

---

## Milestones

- ✅ **v0.4** — Session History, Vibration & Theming (shipped 2026-06-05)
- 🚧 **v0.5** — Accessibility & Analytics (planned)

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

---

## Planned Phases

### 🚧 v0.5 — Bug Fixes, Layout Redesign & Features

**Phase 3: Critical Bugs & UX Polish**
- Goal: Fix Phase 2 blockers (icon sizing, vibration, light theme); add micro-interactions
- Requirements: Cross-browser testing, vibration validation on Android, light/dark contrast verification
- Scope: 3 bug fixes + 1 UX polish = ~2–3 plans
- Dependencies: None

**Phase 4: 2-Column Landscape Layout Redesign**
- Goal: Complete horizontal view overhaul (history visibility, button placement, grid refinement)
- Requirements: Landscape testing (480px to 2560px width), multi-device validation
- Scope: 1 major layout item = ~2–3 plans
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
| 3. Critical Bugs & UX Polish | v0.5 | 0/3 | Planned | — |
| 4. 2-Column Landscape Redesign | v0.5 | 0/3 | Planned | — |
| 5. Advanced History Features | v0.5 | 0/2 | Planned | — |

---

**Last updated:** 2026-06-05 after v0.4 milestone completion
