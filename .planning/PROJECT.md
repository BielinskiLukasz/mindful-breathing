# Mindful Breathing v0.4

## What This Is

A minimalist guided breathing app that helps users practice controlled breathing exercises with visual feedback, sound/haptic cues, and session history tracking. The app runs offline with no dependencies, delivering distraction-free breathing sessions on any modern browser.

## Core Value

Reliable, uninterrupted breathing guidance with verifiable history — users need to trust that their sessions are tracked and that the app won't let them down when they need it most.

## Requirements

### Validated

- ✓ User can select breathing presets (Relax, Box, 4-7-8) — existing
- ✓ User can customize phase durations for each preset — existing
- ✓ Smooth SVG progress ring animation during phases — existing
- ✓ Screen stays on during active session (Wake Lock API) — existing
- ✓ Session data persists to localStorage — existing
- ✓ Works fully offline with no external dependencies — existing
- ✓ Keyboard shortcuts (Space to start/stop, R to reset, F for fullscreen) — existing
- ✓ Visual countdown and phase indicator — existing

### Active

- [ ] Vibration feedback works reliably during phase transitions
- [ ] User can configure max session logs stored (unlimited or capped number)
- [ ] User can configure session logs visible per page (pagination)
- [ ] User can navigate between pages of session history (Page 1, 2, 3...)
- [ ] User can toggle between light and dark theme
- [ ] Font sizing is consistent and readable across all UI elements
- [ ] Landscape (horizontal) orientation is optimized for usability
- [ ] Session history cards are visually scannable

### Out of Scope

- Real-time sync to cloud — localStorage only
- Mobile app — web-first
- Advanced analytics — simple session count and duration
- Multiple user accounts — single-user per browser

## Context

This is a v0.3+ release of an existing mindful-breathing tool. The app is fully functional but has critical UX bug (vibration failure) and needs feature enhancements for session tracking and personalization.

The codebase is a single `index.html` file (~1,350 lines) with clear internal sections: CONFIG, STATE, DOM, HELPERS, RENDER, MAIN LOOP, CONTROLS, etc. No build step, no dependencies, no transpilation.

## Constraints

- **Stack**: Vanilla JS (no frameworks) — keep it that way
- **Deployment**: Static file on GitHub Pages (https://bielinskilukasz.github.io/mindful-breathing/)
- **Browser APIs**: Web Audio API, Vibration API, Screen Wake Lock API, Fullscreen API
- **Storage**: localStorage only (no IndexedDB or cloud)
- **UX requirement**: Works on desktop, tablet, mobile in both portrait and landscape

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Single-file architecture | Keep complexity low, no build tools | ✓ Good — proven lightweight and fast |
| localStorage for persistence | Offline-first, no backend needed | ✓ Good — reliable for single-user |
| Vanilla JS only | Minimize dependencies, maximize compatibility | ✓ Good — runs anywhere with modern browser |
| Light/dark theme toggle | Users prefer choice, dark-only current state limits accessibility | — Pending |
| Paginated history view | Unlimited growth of session data, but scalable UI presentation | — Pending |

## Phases

**Phase 1: Session History & Vibration** (5 requirements)
- Fix vibration reliability (BUG-01)
- History pagination & configuration (HIST-01–HIST-04)

**Phase 2: Theming & UX Polish** (5 requirements)
- Light/dark theme toggle (THEME-01–THEME-02)
- Responsive design & visual polish (UX-01–UX-03)

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd-transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd-complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-06-03 after initialization*
