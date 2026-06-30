# Mindful Breathing — Roadmap

**Current Status:** v0.5 shipped, v0.6 in planning

---

## Milestones

- ✅ **v0.4** — Session History, Vibration & Theming (shipped 2026-06-05)
- ✅ **v0.5** — Critical Bugs & UX Polish (shipped 2026-06-29)
- 📋 **v0.6** — Layout & History (planned)

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

### 📋 v0.6 — Layout & History

- [ ] **Phase 4: Layout & Light Theme** - 2-column landscape CSS Grid and WCAG AA contrast fixes
- [ ] **Phase 5: History Data Management** - JSON/CSV export, JSON import, and clear history with confirmation

---

## Phase Details

### Phase 4: Layout & Light Theme
**Goal**: Users get a readable, accessible layout in both orientations — history panel always visible alongside the ring in landscape, and light theme text passes WCAG AA contrast everywhere
**Depends on**: Phase 3
**Requirements**: UX-04, THEME-03
**Success Criteria** (what must be TRUE):
  1. User can see the history panel alongside the breathing ring without scrolling in landscape on a tablet-width screen (600px+)
  2. All light theme text and interactive element labels pass WCAG AA contrast ratios (4.5:1 normal text, 3:1 large text) verified against all color pairs
  3. Portrait layout on mobile is unchanged — existing phone UX is not regressed
  4. Landscape layout renders correctly across phone landscape (667px), tablet (768–1024px), and desktop (1280px+)
**Plans**: TBD
**UI hint**: yes

### Phase 5: History Data Management
**Goal**: Users have full control over their session data — they can back it up, restore it, and permanently delete it with a clear confirmation step
**Depends on**: Phase 4
**Requirements**: HIST-08, HIST-09, HIST-10, HIST-11
**Success Criteria** (what must be TRUE):
  1. User can download session history as a JSON file that contains all recorded sessions
  2. User can download session history as a CSV file that opens correctly in a spreadsheet
  3. User can import a previously exported JSON file and see existing sessions merged with imported ones (duplicate dates skipped, no data corruption)
  4. User can clear all session history only after a confirmation dialog that uses destructive-action language ("Delete all sessions") — accidental dismissal does not delete data
**Plans**: TBD

---

## Progress

| Phase | Milestone | Plans Complete | Status | Completed |
|-------|-----------|----------------|--------|-----------|
| 1. Session History & Vibration | v0.4 | 1/1 | Complete | 2026-06-04 |
| 2. Theming & UX Polish | v0.4 | 4/4 | Complete | 2026-06-05 |
| 3. Critical Bugs & UX Polish | v0.5 | 8/8 | Complete | 2026-06-29 |
| 4. Layout & Light Theme | v0.6 | 0/? | Not started | - |
| 5. History Data Management | v0.6 | 0/? | Not started | - |

---

**Last updated:** 2026-06-30 — v0.6 roadmap created
