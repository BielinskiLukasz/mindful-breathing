# Mindful Breathing v1.0 — Shipped

## Current State

**Shipped:** v1.0 — Bug Fixes & Visual Polish (2026-07-04)  
**Next milestone:** v1.1 (TBD — planning not yet started)

## What This Is

A minimalist guided breathing app that helps users practice controlled breathing exercises with visual feedback, audio cues, and session history tracking. The app features a personalized light/dark theme, responsive design optimized for mobile-to-4K displays, polished micro-interactions (ring flash, button feedback, gesture hint, error flash), and session history with export/import and incomplete-session tracking. The app runs offline with no dependencies, delivering distraction-free breathing sessions on any modern browser.

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
- ✓ User can configure max session logs stored (unlimited or capped number) — v0.4
- ✓ User can configure session logs visible per page (pagination) — v0.4
- ✓ User can navigate between pages of session history (Page 1, 2, 3...) — v0.4
- ✓ User can toggle between light and dark theme — v0.4
- ✓ Font sizing is consistent and readable across all UI elements — v0.4
- ✓ Landscape (horizontal) orientation is optimized for usability — v0.4
- ✓ Session history cards are visually scannable — v0.4
- ✓ Countdown timer does not accumulate on rapid Start/Stop — v0.5
- ✓ Phase duration inputs clamped to valid range (1–300s) with shared constant — v0.5
- ✓ localStorage quota errors surfaced to user with visible warning — v0.5
- ✓ Button press provides immediate tactile visual feedback — v0.5
- ✓ Phase transition ring flashes briefly to signal phase advance — v0.5
- ✓ First-visit gesture hint shown once and dismissed on interaction — v0.5
- ✓ Duration input error flash on invalid/out-of-range values — v0.5
- ✓ 3-second countdown has audible beeps (350 Hz) and can be skipped (Skip button or ESC) — v0.5
- ✓ History panel visible alongside ring in landscape orientation (CSS Grid 2-column layout) — v0.6
- ✓ Light theme contrast meets WCAG AA (4.5:1 normal, 3:1 large) across all UI elements — v0.6
- ✓ Per-phase atmospheric colors in light mode (sage/lavender/sky/cream per phase) — v0.6
- ✓ Session history export as JSON and CSV file downloads — v0.6
- ✓ Session history import from JSON with merge/dedup — v0.6
- ✓ Clear session history with native confirmation dialog — v0.6
- ✓ **LAYOUT-01**: Compact landscape layout uses height-based detection for tall phones — v1.0
- ✓ **VISUAL-01**: Background color transitions smoothly (220ms) when phase changes — v1.0
- ✓ **TRACK-01**: User can view incomplete sessions in history with cycle count and elapsed time — v1.0
- ✓ **INFO-01**: App version number (v1.0) displayed in info panel footer — v1.0

### Active

*(Empty — planning for v1.1 not yet started. Run `/gsd-new-milestone` to define v1.1 requirements.)*

### Out of Scope

- Real-time sync to cloud — localStorage only
- Mobile native app — web-first
- Advanced analytics — simple session count and duration
- Multiple user accounts — single-user per browser
- Vibration API on Android — hidden as known limitation; Samsung/Android OS restriction; code retained for future re-enable
- CSV import — RFC 4180 edge cases; JSON import covers use case safely (deferred to v1.1+)
- Full accessibility (keyboard nav, ARIA, focus indicators) — deferred to v1.1+

## Context

**v1.0 shipped 2026-07-04.**

Codebase: single `index.html` file (~2,500 lines) with clear internal sections. No build step, no dependencies, no transpilation. Deployed via GitHub Pages.

**v1.0 Changes:**
- `transition: background 220ms ease` on body — CSS custom property `--bg` changes now animate (VISUAL-01)
- `height: auto; min-height: 100vh` in compact landscape body — controls visible on tall phones without horizontal scroll (LAYOUT-01)
- `APP_VERSION = "1.0"` constant injected into info panel footer as `v1.0` (INFO-01)
- Incomplete session tracking: Pause saves `{incomplete: true}` when ≥1 full cycle completed; rendered at opacity 0.6 with italic "Incomplete" suffix (TRACK-01)
- Stop button renamed to Pause — reflects actual behavior

**Known Limitations:**
- Vibration API on Android: Samsung/Android OS blocks the API; toggle hidden (`display:none`); code retained
- CSV import: deferred to v1.1 (RFC 4180 edge cases)
- Accessibility: keyboard + screen reader support deferred to v1.1+

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
| `DURATION_RANGE` constant (v0.5) | Single source of truth for input clamping, attributes, and storage validation | ✓ Good — eliminates scattered hardcoded bounds |
| CSS-only button :active (v0.5) | Native pseudo-class fires on both mouse and touch, GPU-accelerated | ✓ Good — zero JS overhead |
| `offsetWidth` reflow for ring flash (v0.5) | Restarts CSS animation on rapid consecutive phase changes | ✓ Good — animation always fires even when triggered back-to-back |
| 350 Hz countdown beep vs 520 Hz phase beep (v0.5) | Distinct audio cues for countdown vs phase transition | ✓ Good — users can distinguish by ear |
| ESC key first in switch (v0.5) | Browser-native dismiss convention; ESC before Space | ✓ Good — predictable keyboard behavior |
| Vibration UI hidden with display:none (v0.5) | Samsung/Android OS restriction blocks Vibration API; ship clean UI over broken toggle | ✓ Good — no user-visible broken feature; code retained for future re-enable |
| Error.name check for QuotaExceededError (v0.5) | instanceof fails across browser implementations | ✓ Good — cross-browser compatible error detection |
| CSS Grid `1fr 1fr` for landscape layout (v0.6) | Cleaner equal-column semantics than flex; children need only min-width:0 | ✓ Good — no flex:1 hacks needed |
| WCAG AA contrast: #a0662e / #6b6058 on cream (v0.6) | Darkened warm brown values stay in brand family while passing 4.5:1 | ✓ Good — visually warm, accessible |
| Per-phase bgLight/accentLight in PRESETS (v0.6) | Collocates light/dark pairs per phase object; isDarkMode branches at runtime | ✓ Good — single source of truth per phase |
| Blob + URL.createObjectURL for export (v0.6) | No server needed; works offline; revoked after click | ✓ Good — zero infrastructure |
| CSV duration M:SS inline (not formatDuration()) (v0.6) | formatDuration() returns "N min N sec" which breaks spreadsheet parsing | ✓ Good — D-08 compliant |
| Import dedup by exact date string (v0.6) | Safer than replace; no accidental data loss on re-import | ✓ Good — conservative merge |
| Native `<dialog>` for clear confirmation (v0.6) | ESC, backdrop dismiss, focus trap all free from browser | ✓ Good — zero JS for dismissal |
| CSV import deferred to v1.1 (v0.6) | RFC 4180 edge cases too complex without a parser library | ✓ Good — JSON covers the use case safely |
| `background` shorthand in body transition (v1.0) | CSS custom property `--bg` doesn't animate via `background-color` sub-property | ✓ Good — reliable across browsers |
| `height: auto` + `min-height: 100vh` in compact landscape (v1.0) | Overrides base `height: 100vh` so content scrolls past viewport on tall phones | ✓ Good — UAT verified on iPhone 14 Pro Max preset |
| APP_VERSION JS-injected at init (v1.0) | HTML element stays empty; CONFIG constant is single source of truth | ✓ Good — no hardcoded value in markup |
| `cycleCount >= 2` for incomplete save threshold (v1.0) | cycleCount starts at 1; increments after each full cycle; ≥2 = at least 1 done | ✓ Good — correct semantics for "incomplete" |
| Incomplete save in toggleBtn handler only (v1.0) | stop() and reset() stay unmodified; no duplicate entries on pause/resume cycles | ✓ Good — clean isolation |
| Stop renamed to Pause (v1.0) | Reflects actual behavior — session is paused, Resume/Start follows | ✓ Good — accurate UX language |

## Phases

**v0.4 (Shipped 2026-06-05)** — see `.planning/milestones/v0.4-ROADMAP.md`

**v0.5 (Shipped 2026-06-29)** — see `.planning/milestones/v0.5-ROADMAP.md`

**v0.6 (Shipped 2026-07-01)** — see `.planning/milestones/v0.6-ROADMAP.md`

**v1.0 (Shipped 2026-07-04)** — see `.planning/milestones/v1.0-ROADMAP.md`

**Phase 6: Bug Fixes & Visual Polish** ✓ Complete — `.planning/phases/06/`

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
*Last updated: 2026-07-04 after v1.0 milestone*
