# Mindful Breathing v0.4 — Roadmap

**Milestone:** v0.4  
**Created:** 2026-06-03  
**Granularity:** Coarse  
**Coverage:** 11/11 v1 requirements mapped ✓

---

## Phases

- [ ] **Phase 1: Critical Fixes** - Resolve button blocking and vibration reliability issues
- [ ] **Phase 2: Session History Management** - Enable pagination, filtering, and configurable storage
- [ ] **Phase 3: Theming & UX Polish** - Add light/dark theme toggle and optimize visual design across devices

---

## Phase Details

### Phase 1: Critical Fixes

**Goal:** Users can interact with the app reliably after sessions complete and receive haptic feedback during active breathing.  
**Mode:** mvp  
**Depends on:** None (foundation)  
**Requirements:** BUG-01, BUG-02  

**Success Criteria** (what must be TRUE):
1. User can click "Start again" button immediately after session completes without DOM blocking
2. Vibration haptics trigger reliably at each phase transition during active breathing session
3. No z-index conflicts prevent interaction with post-session UI elements

**Plans:** TBD

---

### Phase 2: Session History Management

**Goal:** Users can configure session history storage, pagination, and navigate their breathing session logs.  
**Mode:** mvp  
**Depends on:** Phase 1  
**Requirements:** HIST-01, HIST-02, HIST-03, HIST-04  

**Success Criteria** (what must be TRUE):
1. User can configure maximum session logs to store (numeric input or "unlimited" option, persisted to localStorage)
2. User can set pagination size (number of logs visible per page) and see changes apply immediately
3. User can navigate between pages of session history (Page 1, 2, 3...) with next/previous controls
4. Session history cards are visually scannable, displaying duration, preset name, and date in clear format

**Plans:** TBD

**UI hint:** yes

---

### Phase 3: Theming & UX Polish

**Goal:** Users have personalized theme preferences and optimized visual experience across all screen sizes and orientations.  
**Mode:** mvp  
**Depends on:** Phase 2  
**Requirements:** THEME-01, THEME-02, UX-01, UX-02, UX-03  

**Success Criteria** (what must be TRUE):
1. User can toggle light/dark theme and preference persists across browser sessions via localStorage
2. Font sizes are consistent and readable from mobile (320px) to desktop (4K) widths
3. Landscape orientation displays breathing interface optimally without horizontal scroll or cramped text
4. All interactive elements have clear visual hierarchy and focus states for keyboard navigation

**Plans:** TBD

**UI hint:** yes

---

## Progress

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Critical Fixes | 0/TBD | Not started | - |
| 2. Session History | 0/TBD | Not started | - |
| 3. Theming & Polish | 0/TBD | Not started | - |

---

## Coverage Summary

**Total v1 requirements:** 11  
**Mapped to phases:** 11  
**Unmapped:** 0 ✓

| Category | Requirements | Phase |
|----------|--------------|-------|
| Bug Fixes | BUG-01, BUG-02 | 1 |
| Session History | HIST-01, HIST-02, HIST-03, HIST-04 | 2 |
| Theming | THEME-01, THEME-02 | 3 |
| User Experience | UX-01, UX-02, UX-03 | 3 |

---

*Roadmap created: 2026-06-03*
