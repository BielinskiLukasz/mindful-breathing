# Mindful Breathing v1.0

## Current Milestone: v1.0 — Bug Fixes & Visual Polish

**Status:** Planning
**Goal:** Fix critical landscape media query bug, enhance visual polish, track unfinished sessions, and display app version

**Target features:**
- Fix landscape layout media query on tall phones (height-based compact layout detection)
- Display app version in info panel
- Background tint transitions in dark mode for smooth phase animations
- Track and display unfinished/incomplete sessions in history

## Previous Milestone: v0.6 Shipped

**Shipped:** 2026-07-01

**v0.6 delivered:**
- CSS Grid 2-column landscape layout — history panel visible alongside the breathing ring
- WCAG AA light theme contrast fixes and per-phase atmospheric bgLight/accentLight colors
- Full session history data management: JSON/CSV export, JSON import with merge/dedup, clear with native dialog

## What This Is

A minimalist guided breathing app that helps users practice controlled breathing exercises with visual feedback, audio cues, and session history tracking. The app features a personalized light/dark theme, responsive design optimized for mobile-to-4K displays, and polished micro-interactions (ring flash, button feedback, gesture hint, error flash). The app runs offline with no dependencies, delivering distraction-free breathing sessions on any modern browser.

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

### Active

- [ ] **LAYOUT-01**: Landscape media query uses height-based detection for tall phone compact layout
- [ ] **VISUAL-01**: Background color transitions smoothly in dark mode during phase changes
- [ ] **TRACK-01**: User can view unfinished sessions in history with cycle count and elapsed time
- [ ] **INFO-01**: App version number displayed in info panel

### Out of Scope

- Real-time sync to cloud — localStorage only
- Mobile native app — web-first
- Advanced analytics — simple session count and duration
- Multiple user accounts — single-user per browser
- Vibration API on Android — hidden as known limitation; Samsung/Android OS restriction; no code path to fix without device access

## Context

**v0.6 shipped 2026-07-01.**

Codebase: single `index.html` file (2,487 lines) with clear internal sections. No build step, no dependencies, no transpilation. Deployed via GitHub Pages.

**Recent Updates (v0.6):**
- CSS Grid `1fr 1fr` landscape layout — ring left, history/controls right on 600px+ landscape screens
- WCAG AA light theme: accent #a0662e (5.2:1), textSoft #6b6058 (5.3:1) on cream #f5f1ed
- Per-phase bgLight/accentLight in all PRESETS (11 phase entries) — atmospheric tints in light mode sessions
- `applyThemeForCurrentPhase()` with isDarkMode branching; `toggleTheme()` updates mid-session immediately
- `#historyActions` row: Export JSON, Export CSV, Import buttons in history panel
- `exportJson()`, `exportCsv()` via Blob + URL.createObjectURL (M:SS duration in CSV)
- `importJson()` via FileReader: schema validation, dedup by date, merge, sort, cap, feedback flash
- Native `<dialog>` for clear-history: "Delete all sessions" + Cancel + backdrop dismiss
- Post-release debug fix: startup crash, icon regression, history init issue (3 bugs in one session)

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
| CSV import deferred to v0.7 (v0.6) | RFC 4180 edge cases too complex without a parser library | ✓ Good — JSON covers the use case safely |

## Phases

**v0.4 (Shipped 2026-06-05)**

**Phase 1: Session History & Vibration** ✓ Complete
- Fix vibration reliability (BUG-01) — verified correct per W3C spec
- History pagination & configuration (HIST-01–HIST-04) — 5 items per page, configurable cap 1–10,000

**Phase 2: Theming & UX Polish** ✓ Complete
- Light/dark theme toggle (THEME-01–THEME-02) — warm cream/gold light palette
- Responsive design & visual polish (UX-01–UX-03) — clamp() typography, landscape 50/50 layout

**v0.5 (Shipped 2026-06-29)**

**Phase 3: Critical Bugs & UX Polish** ✓ Complete
- Bug fixes (BUG-01–BUG-03) — countdown timer cleanup, duration input validation, localStorage quota handling
- Micro-interactions (MICRO-01–MICRO-04) — button press feedback, ring flash, gesture hint, error flash
- Gap closure (TEST-02, TEST-03) — countdown audio beeps, Skip button, ESC key handler
- Gap closure (TEST-01) — vibration deferred as known_limitation; UI hidden

**v0.6 (Shipped 2026-07-01)**

**Phase 4: Layout & Light Theme** ✓ Complete
- CSS Grid 2-column landscape layout (UX-04) — ring left, history right on 600px+ landscape
- WCAG AA light theme contrast fixes (THEME-03) — #a0662e / #6b6058 on cream
- Per-phase atmospheric bgLight/accentLight for all PRESETS entries

**Phase 5: History Data Management** ✓ Complete
- JSON/CSV export (HIST-08, HIST-09) — Blob downloads, no server
- JSON import with merge/dedup (HIST-10) — FileReader, schema validation, exact-date dedup
- Clear history with native dialog confirmation (HIST-11) — ESC + backdrop dismiss

**v1.0 (In Planning)**

**Phase 6: Bug Fixes & Visual Polish** (Planned)
- Landscape media query fix (LAYOUT-01) — height-based detection for tall phones
- Dark mode background transitions (VISUAL-01) — smooth phase tint changes
- Unfinished session tracking (TRACK-01) — display incomplete sessions in history
- Version display (INFO-01) — show app version in info panel

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
*Last updated: 2026-07-03 — v1.0 milestone initiated*
