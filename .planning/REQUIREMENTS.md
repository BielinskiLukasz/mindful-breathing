# Requirements: Mindful Breathing v0.4

**Defined:** 2026-06-03
**Core Value:** Reliable, uninterrupted breathing guidance with verifiable history

## v1 Requirements

### Bug Fixes

- [x] **BUG-01**: Vibration feedback triggers reliably during phase transitions

### Session History

- [x] **HIST-01**: User can configure maximum number of session logs to store (number input or "unlimited")
- [x] **HIST-02**: User can configure number of session logs visible per page (pagination items per page)
- [x] **HIST-03**: User can navigate between pages of session history (Page 1, 2, 3...)
- [x] **HIST-04**: Session history cards are visually scannable and show key metrics (duration, preset name, date)

### Theming

- [x] **THEME-01**: User can toggle between light and dark theme
- [x] **THEME-02**: Theme preference persists across browser sessions (localStorage)

### User Experience

- [x] **UX-01**: Font sizing is consistent and readable across all UI elements
- [x] **UX-02**: Landscape (horizontal) orientation displays optimally on tablets and wide screens
- [x] **UX-03**: UI audit completed — all visual elements reviewed for usability and hierarchy

## v2 Requirements

### Advanced History

- **HIST-05**: User can filter session history by preset (Relax, Box, 4-7-8)
- **HIST-06**: User can export session history as CSV or JSON
- **HIST-07**: User can delete individual sessions from history

### Analytics

- **STATS-01**: User can view aggregate stats (total sessions, total minutes, streak)
- **STATS-02**: User can view weekly or monthly breakdown of sessions

### Accessibility

- **A11Y-01**: WCAG 2.1 AA compliance audit
- **A11Y-02**: Screen reader compatibility for all controls

## Out of Scope

| Feature | Reason |
|---------|--------|
| Real-time cloud sync | localStorage sufficient for single-user; cloud adds complexity |
| Data import/export UI | Can defer to v2 if needed; v1 keeps history local |
| Multiple breathing styles beyond presets | Presets cover most use cases; customization exists |
| Mobile native app | Web-first; mobile uses responsive design |
| Social features (sharing, comparing) | Out of scope for meditation app |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| BUG-01 | Phase 1 | Complete |
| HIST-01 | Phase 1 | Complete |
| HIST-02 | Phase 1 | Complete |
| HIST-03 | Phase 1 | Complete |
| HIST-04 | Phase 1 | Complete |
| THEME-01 | Phase 2 | Complete |
| THEME-02 | Phase 2 | Complete |
| UX-01 | Phase 2 | Complete |
| UX-02 | Phase 2 | Complete |
| UX-03 | Phase 2 | Complete |

**Coverage:**

- v1 requirements: 10 total
- Mapped to phases: 10
- Unmapped: 0 ✓

---
*Requirements defined: 2026-06-03*
*Last updated: 2026-06-03 after initial definition*
