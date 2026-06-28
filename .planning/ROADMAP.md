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
- Goal: Fix Phase 2 blockers (countdown timer cleanup, duration input validation, wake lock robustness); add 4 CSS-based micro-interactions; validate vibration on Android; complete Chrome/Edge cross-browser testing
- Requirements: BUG-01, BUG-02, BUG-03, MICRO-01, MICRO-02, MICRO-03, MICRO-04, TEST-01, TEST-02, TEST-03
- Scope: 6 plans across 6 waves (bug fixes → micro-interactions → testing)
- Dependencies: None

**Plans:**
- [ ] 03-01-PLAN.md — Bug Fixes (countdown timer cleanup, duration validation, wake lock/localStorage robustness)
- [ ] 03-02-PLAN.md — Button Press Feedback (CSS :active enhancement)
- [ ] 03-03-PLAN.md — Phase Transition Ring Flash & Gesture Hint (CSS animations + DOM)
- [ ] 03-04-PLAN.md — Duration Input Error Feedback (red flash animation)
- [x] 03-05-PLAN.md — Android Vibration Testing (real device QA)
- [x] 03-06-PLAN.md — Cross-Browser & Contrast Validation (audio, wake lock, FPS, theme contrast)
- [x] 03-07-PLAN.md — Gap closure: Countdown audio beeps + skip capability (TEST-02, TEST-03)
- [ ] 03-08-PLAN.md — Gap closure: Vibration normalization + Android DevTools diagnostic (TEST-01)

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
| 3. Critical Bugs & UX Polish | v0.5 | 7/8 | Gap closure (1 plan remaining) | — |
| 4. 2-Column Landscape Redesign & Light Theme | v0.5 | 0/4 | Planned | — |
| 5. Advanced History Features | v0.5 | 0/2 | Planned | — |

---

**Last updated:** 2026-06-05 after Phase 3 planning
