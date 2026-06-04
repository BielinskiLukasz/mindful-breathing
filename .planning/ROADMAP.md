# Mindful Breathing v0.4 — Roadmap

**Milestone:** v0.4  
**Created:** 2026-06-03  
**Granularity:** Coarse  
**Coverage:** 11/11 v1 requirements mapped ✓

---

## Phases

- [x] **Phase 1: Session History & Vibration** - Fix vibration reliability and enable pagination, filtering, and configurable storage (completed 2026-06-04)
- [ ] **Phase 2: Theming & UX Polish** - Add light/dark theme toggle and optimize visual design across devices

---

## Phase Details

### Phase 1: Session History & Vibration

**Goal:** Users can configure session history storage, navigate logs, and receive haptic feedback during active breathing.  
**Mode:** mvp  
**Depends on:** None (foundation)  
**Requirements:** BUG-01, HIST-01, HIST-02, HIST-03, HIST-04  

**Success Criteria** (what must be TRUE):

1. Vibration haptics trigger reliably at each phase transition during active breathing session
2. User can configure maximum session logs to store (numeric input or "unlimited" option, persisted to localStorage)
3. User can set pagination size (number of logs visible per page) and see changes apply immediately
4. User can navigate between pages of session history (Page 1, 2, 3...) with next/previous controls
5. Session history cards are visually scannable, displaying duration, preset name, and date in clear format

**Plans:** 1/1 plans complete

- [x] 01-01-PLAN.md — Vibration reliability verification, history cap configuration, pagination controls, enhanced card rendering, responsive CSS

**UI hint:** yes

---

### Phase 2: Theming & UX Polish

**Goal:** Users have personalized theme preferences and optimized visual experience across all screen sizes and orientations.  
**Mode:** mvp  
**Depends on:** Phase 1  
**Requirements:** THEME-01, THEME-02, UX-01, UX-02, UX-03  

**Success Criteria** (what must be TRUE):

1. User can toggle light/dark theme and preference persists across browser sessions via localStorage
2. Font sizes are consistent and readable from mobile (320px) to desktop (4K) widths
3. Landscape orientation displays breathing interface optimally without horizontal scroll or cramped text
4. All interactive elements have clear visual hierarchy and accessible color contrast (keyboard focus states deferred to Phase 3)

**Plans:** 4/4 plans created

- [ ] 02-01-PLAN.md — Light theme CSS custom properties, toggle button in settings, theme handler, localStorage persistence
- [ ] 02-02-PLAN.md — Responsive typography with CSS clamp(), all text sizes scale smoothly from 320px to 4K
- [ ] 02-03-PLAN.md — Landscape orientation side-by-side layout (ring 50% left, controls/history 50% right), max-width cap at 1400px
- [ ] 02-04-PLAN.md — Cross-device manual testing (320px to 4K, portrait/landscape), Phase 1 regression check

**UI hint:** yes

---

## Progress

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Session History & Vibration | 1/1 | Complete    | 2026-06-04 |
| 2. Theming & Polish | 0/4 | Planned | — |

---

## Coverage Summary

**Total v1 requirements:** 10  
**Mapped to phases:** 10  
**Unmapped:** 0 ✓

| Category | Requirements | Phase |
|----------|--------------|-------|
| Bug Fixes | BUG-01 | 1 |
| Session History | HIST-01, HIST-02, HIST-03, HIST-04 | 1 |
| Theming | THEME-01, THEME-02 | 2 |
| User Experience | UX-01, UX-02, UX-03 | 2 |

---

*Roadmap updated: 2026-06-04 Phase 2 planning complete*
