# Requirements: Mindful Breathing v0.6

**Defined:** 2026-06-30
**Core Value:** Reliable, uninterrupted breathing guidance with verifiable history — users need to trust that their sessions are tracked and that the app won't let them down when they need it most.

## v1 Requirements

### Layout & Responsive Design

- [x] **UX-04**: User can view the history panel alongside the breathing ring in landscape orientation on tablet-width screens and above (2-column CSS Grid layout)

### Theming & Accessibility

- [x] **THEME-03**: Light theme text and interactive elements meet WCAG AA contrast ratios (4.5:1 for normal text, 3:1 for large text) across all UI elements

### History Data Management

- [x] **HIST-08**: User can export session history as a JSON file download
- [x] **HIST-09**: User can export session history as a CSV file download
- [x] **HIST-10**: User can import session history from a previously exported JSON file, merged with existing history (skip duplicates by date)
- [x] **HIST-11**: User can clear all session history after a confirmation dialog with destructive-action language

## v2 Requirements

### History Data Management

- **HIST-05**: User can filter session history by preset (Relax, Box, 4-7-8) — deferred from v0.4
- **HIST-07**: User can delete individual sessions from history — deferred from v0.4
- **HIST-12**: User can import session history from a CSV file — deferred to v0.7 (RFC 4180 edge cases too risky without a parser library; JSON import covers the use case safely)

## Out of Scope

| Feature | Reason |
|---------|--------|
| CSV import (v0.6) | RFC 4180 edge cases (quoted newlines, mismatched columns) are complex without a parser library — deferred to v0.7 |
| Soft-delete / history undo | Over-engineered for current single-user, local-only use case |
| Adjustable landscape column ratio | 50/50 split sufficient for v0.6; tuning deferred |
| Cloud sync | localStorage only — confirmed constraint |
| Individual session editing | Out of current scope; v2+ candidate |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| UX-04 | Phase 4 | Complete |
| THEME-03 | Phase 4 | Complete |
| HIST-08 | Phase 5 | Complete |
| HIST-09 | Phase 5 | Complete |
| HIST-10 | Phase 5 | Complete |
| HIST-11 | Phase 5 | Complete |

**Coverage:**

- v1 requirements: 6 total
- Mapped to phases: 6
- Unmapped: 0 ✓

---
*Requirements defined: 2026-06-30*
*Last updated: 2026-06-30 — traceability populated after roadmap creation*
