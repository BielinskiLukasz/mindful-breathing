# Mindful Breathing v0.4

## What This Is

A minimalist guided breathing app that helps users practice controlled breathing exercises with visual feedback, sound/haptic cues, and session history tracking. The app features a personalized light/dark theme, responsive design optimized for mobile-to-4K displays, and reliable haptic feedback. The app runs offline with no dependencies, delivering distraction-free breathing sessions on any modern browser.

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
- ✓ Vibration feedback works reliably during phase transitions — v0.4
- ✓ User can configure max session logs stored (unlimited or capped number) — v0.4
- ✓ User can configure session logs visible per page (pagination) — v0.4
- ✓ User can navigate between pages of session history (Page 1, 2, 3...) — v0.4
- ✓ User can toggle between light and dark theme — v0.4
- ✓ Font sizing is consistent and readable across all UI elements — v0.4
- ✓ Landscape (horizontal) orientation is optimized for usability — v0.4
- ✓ Session history cards are visually scannable — v0.4

### Active

(None — all v0.4 requirements shipped. v0.5 scope pending.)

### Out of Scope

- Real-time sync to cloud — localStorage only
- Mobile app — web-first
- Advanced analytics — simple session count and duration
- Multiple user accounts — single-user per browser

## Context

**v0.4 Status:** Shipped 2026-06-05 with all v1 requirements complete.

The app is production-ready with full session history tracking, reliable haptic feedback, and personalized theme preferences. The codebase is a single `index.html` file (2,014 lines) with clear internal sections: CONFIG, STATE, DOM, HELPERS, PERSISTENCE, SESSION HISTORY, RENDER, MAIN LOOP, CONTROLS, etc. No build step, no dependencies, no transpilation.

**Recent Updates:**
- Session history now paginated (5 per page) with configurable storage cap (default 1,000)
- Vibration reliability verified correct per W3C spec
- Light/dark theme toggle with 220ms smooth transitions (cream/gold light palette, dark/gray dark palette)
- Responsive typography with CSS clamp() — fluid scaling from 320px to 4K without breakpoint jumps
- Landscape 50/50 layout for tablets/desktops with scrollable history and fixed controls
- Cross-device testing: 320px mobile to 4K desktop, portrait/landscape, both themes

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
| Light/dark theme toggle | Users prefer choice, dark-only previous state limited accessibility | ✓ Good — warm cream/gold light palette, smooth 220ms transitions |
| Paginated history view | Unlimited growth of session data, but scalable UI presentation | ✓ Good — 5 items per page, configurable cap (default 1,000) |
| CSS clamp() for typography | Fluid scaling without media query breakpoints | ✓ Good — no jumps from 320px to 4K, consistent readability |
| Landscape 50/50 layout | Tablets and desktop need better horizontal space utilization | ✓ Good — ring left (scalable), controls/history right (scrollable) |
| Pagination state in sessionStorage | Avoids localStorage pollution while supporting navigation reset on reload | ✓ Good — predictable, simple |
| Theme toggle in cornerControls | Instant-action access alongside sound/vibration toggles | ✓ Good — discoverability + muscle memory consistency |

## Phases

**v0.4 (Shipped 2026-06-05)**

**Phase 1: Session History & Vibration** ✓ Complete
- Fix vibration reliability (BUG-01) — verified correct per W3C spec
- History pagination & configuration (HIST-01–HIST-04) — 5 items per page, configurable cap 1–10,000

**Phase 2: Theming & UX Polish** ✓ Complete
- Light/dark theme toggle (THEME-01–THEME-02) — warm cream/gold light palette
- Responsive design & visual polish (UX-01–UX-03) — clamp() typography, landscape 50/50 layout

**v0.5 (Planned)**

**Phase 3: Accessibility Polish** (keyboard focus, micro-interactions)
**Phase 4: Analytics Dashboard** (total sessions, weekly/monthly breakdown, streaks)
**Phase 5: Advanced History** (filter by preset, export CSV/JSON, delete individual sessions)

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
*Last updated: 2026-06-05 after v0.4 milestone completion*
