# Requirements: Mindful Breathing v1.0

**Milestone:** v1.0 — Bug Fixes & Visual Polish  
**Status:** Roadmap Created  
**Last Updated:** 2026-07-03

---

## v1 Requirements — IN PROGRESS

### Landscape Layout & Responsiveness

- [ ] **LAYOUT-01**: Landscape compact layout activates on tall phones based on viewport height (≤500px), not width, ensuring correct display in portrait-rotated-to-landscape orientation on iPhone 14 Pro Max and similar tall devices
  - **Acceptance**: Media query uses `(orientation: landscape) and (max-height: 500px)` for compact layout instead of width-based check
  - **Dependencies**: None
  - **Complexity**: Low

### Visual Polish & Transitions

- [ ] **VISUAL-01**: Body background color transitions smoothly (220ms) in dark mode when phase changes, instead of jumping abruptly
  - **Acceptance**: Background tint change is animated/transitioned during phase updates in dark mode; light mode remains unchanged (not needed)
  - **Dependencies**: None
  - **Complexity**: Low

### Session History Data Enhancement

- [ ] **TRACK-01**: User can view unfinished/incomplete sessions in history (sessions that were started but not completed to full cycle), showing cycle count and elapsed time
  - **Acceptance**: Session history tracks interrupted sessions with `incomplete: true` flag; displays with distinct styling (e.g. "Incomplete - 3 cycles, 5:30")
  - **Dependencies**: None
  - **Complexity**: Medium (requires schema change to session storage)

### Info Panel Enhancement

- [ ] **INFO-01**: App version number is displayed in the info panel (e.g. "v1.0"), updated automatically when app updates
  - **Acceptance**: Version hardcoded in CONFIG section (e.g. `APP_VERSION: "1.0"`); displayed in info panel footer; matches git tags
  - **Dependencies**: None
  - **Complexity**: Low

---

## v2 Requirements — DEFERRED

### History Filtering & Management

- **HIST-05**: User can filter session history by preset (Relax, Box, 4-7-8) — from backlog
- **HIST-07**: User can delete individual sessions from history — from backlog
- **HIST-12**: User can import session history from a CSV file — deferred to v0.7+ (RFC 4180 edge cases)

### Accessibility

- **A11Y-01**: Full keyboard navigation (Tab, Shift+Tab, Enter/Space for buttons)
- **A11Y-02**: Screen reader support with ARIA labels and semantic HTML
- **A11Y-03**: Visible focus indicators on all interactive elements

---

## Out of Scope (v1.0)

| Feature | Reason | Status |
|---------|--------|--------|
| CSV import | RFC 4180 edge cases without a parser library | Deferred to v1.1+ |
| Accessibility (full) | Separate project scope; defer to v1.1 | Deferred |
| Cloud sync | localStorage only — confirmed constraint | Remains out of scope |
| Soft-delete / history undo | Over-engineered for single-user use | Remains out of scope |

---

## Traceability

| Requirement | Phase | Status | Notes |
|-------------|-------|--------|-------|
| LAYOUT-01 | 6 | Roadmap created | Media query fix for height-based compact layout |
| VISUAL-01 | 6 | Roadmap created | Background transition in dark mode |
| TRACK-01 | 6 | Roadmap created | Unfinished session tracking |
| INFO-01 | 6 | Roadmap created | Version display in info panel |

**Coverage:**
- v1 requirements: 4 total
- Mapped to Phase 6: 4/4 (100%)
- Status: Roadmap created; ready for phase planning

---

*Last updated: 2026-07-03 — v1.0 roadmap created; all requirements mapped*
