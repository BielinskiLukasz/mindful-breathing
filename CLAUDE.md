# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the App

No build step — open `index.html` directly in a browser, or serve it:

```bash
python -m http.server 8080
# then open http://localhost:8080
```

There is no package manager, no dependencies, no compilation. Opening via `file://` disables Web Audio, Wake Lock, and Vibration APIs but the basic timer still works.

When adding new cached assets (images, scripts), bump the `CACHE` key in `sw.js` from `mb-v1` to `mb-v2` etc. to force cache invalidation on next load.

## Architecture

The entire application is a single file: `index.html` (~3,400 lines). All HTML, CSS, and JS lives there — no modules, no imports, all state is global. The `<script>` block is divided into clearly labeled sections:

| Section | What it does |
|---|---|
| **CONFIG** | Frozen constants: `PRESETS`, `SOUND`, `VIBRATION`, `RING`, `COUNTDOWN`, `SESSION`, `UI`, `QUOTES`, `DURATION_RANGE` |
| **STATE** | Mutable globals: phase index, cycle count, elapsed time, `audioCtx`, wake lock handle, `longestStreak` |
| **DOM** | Element references collected once at startup (e.g. `appEl`, `phaseEl`, `ringProgressEl`) |
| **RING GEOMETRY** | Pre-calculates `CIRC` (circumference) from `RING` config; sets `strokeDasharray` once |
| **HELPERS** | `hexToRgba()`, `getPhase()`, `getGoal()`, `buildActivePhases()`, theme application |
| **DURATION INPUTS** | Per-phase duration inputs; `updateTimeEst()` recalculates estimated session length |
| **PERSISTENCE** | `saveSettings()` / `loadSettings()` via `localStorage`; custom preset CRUD |
| **SESSION HISTORY** | Save, load, and render past sessions; configurable cap (default 1,000) |
| **CUSTOM PRESET RENDERING** | Renders user-defined presets in the settings panel |
| **PRESET BUILDER** | `<dialog>`-based form for creating and editing custom presets |
| **WAKE LOCK** | Screen Wake Lock API wrapper |
| **SOUND/VIBRATION** | Web Audio API beeps + Vibration API haptics |
| **RENDER** | SVG ring progress, countdown text, phase label |
| **PHASE ADVANCE** | Cycles Inhale → Hold → Exhale → Hold2, fires beep/vibe cues |
| **MAIN LOOP** | `requestAnimationFrame` loop (`loop()`) driving animation and phase transitions |
| **CONTROLS** | `start()` / `stop()` / `reset()`, 3-second countdown overlay |
| **FULLSCREEN** | Fullscreen API toggle |
| **STREAK FUNCTIONS** | Computes current and longest streak from `HISTORY_KEY` entries; updates badge |
| **KEYBOARD SHORTCUTS** | Space (start/stop), R (reset), F (fullscreen) |

### State machine

Phases (`activePhases` array, built from the active preset) advance in sequence. The `loop()` function computes `progress01 = elapsed / phaseDuration` each frame and calls `render()` to update the SVG ring (stroke-dashoffset) and `advanceToNextPhase()` when `progress01 >= 1`.

### Theming

Four CSS custom properties drive all colors: `--bg`, `--accent`, `--accentSoft`, `--text`. Theme switching mutates these on `:root` and saves to localStorage. JS-driven properties (ring track, overlay, panel colors) are also set inline via `style.setProperty`.

Each phase also carries its own `theme: { bg, bgLight, accent, accentLight }` for dynamic per-phase color transitions.

### Persistence

Three localStorage keys (defined in CONFIG):
- `STORAGE_KEY` (`mb_v1`) — settings: `sound`, `vibe`, `isDarkMode`, `savedDurations`, `goal`, `maxHistoryEntries`, `preset`, `longestStreak`
- `HISTORY_KEY` (`mb_history`) — array of `{ date, durationMs, cycles, preset }` objects; capped at `maxHistoryEntries` (default 1,000)
- `CUSTOM_PRESETS_KEY` (`mb_custom_presets`) — user-created custom presets array

Streaks are computed from `HISTORY_KEY` on demand; `longestStreak` is cached in `STORAGE_KEY`.

## Key Constraints

- **Vanilla JS only** — no frameworks, no npm, no transpilers. Keep it that way.
- **Single file** — all HTML, CSS, and JS stays in `index.html`.
- **No external network requests** — app works fully offline.
- **Graceful degradation** — all optional browser APIs (Web Audio, Vibration, Wake Lock, Fullscreen) fail silently with `try/catch` or capability checks. No `throw` anywhere.
- **Mobile + desktop** — responsive via media queries at 480px, 360px, and landscape `max-height: 500px`.

## Naming Conventions

- Constants: `UPPERCASE` with `Object.freeze()` — `SOUND`, `RING`, `PRESETS`, `UI`
- DOM references: camelCase, often suffixed `El` — `appEl`, `phaseEl`, `ringProgressEl`
- State globals: camelCase — `isRunning`, `cycleCount`, `elapsedMs`, `activePresetKey`
- Functions: camelCase — `getPhase()`, `playBeep()`, `advanceToNextPhase()`, `renderHistory()`
- CSS classes: camelCase for components, kebab-case for modifier states — `.ringWrap`, `.presetBtn.active`

## Live Demo

https://bielinskilukasz.github.io/mindful-breathing/

<!-- GSD:project-start source:PROJECT.md -->

## Project

**Mindful Breathing v1.1.0**

A minimalist guided breathing app that helps users practice controlled breathing exercises with visual feedback, sound/haptic cues, and session history tracking. The app runs offline with no dependencies, delivering distraction-free breathing sessions on any modern browser.

**Core Value:** Reliable, uninterrupted breathing guidance with verifiable history — users need to trust that their sessions are tracked and that the app won't let them down when they need it most.

### Constraints

- **Stack**: Vanilla JS (no frameworks) — keep it that way
- **Deployment**: Static file on GitHub Pages (https://bielinskilukasz.github.io/mindful-breathing/)
- **Browser APIs**: Web Audio API, Vibration API, Screen Wake Lock API, Fullscreen API
- **Storage**: localStorage only (no IndexedDB or cloud)
- **UX requirement**: Works on desktop, tablet, mobile in both portrait and landscape

<!-- GSD:project-end -->

<!-- GSD:stack-start source:codebase/STACK.md -->

## Technology Stack

- **Language**: Vanilla HTML5/CSS3/ES2023 — no transpilers, no build tools
- **Runtime**: Any modern browser (Chrome, Edge, Firefox, Safari — desktop and mobile)
- **PWA**: `manifest.json` + `sw.js` (cache-first, cache key `mb-v1`) + `icon.svg`
- **APIs**: Web Audio API, Vibration API, Screen Wake Lock API, Fullscreen API, localStorage, Service Worker, requestAnimationFrame
- **Secure context required**: Web Audio, Wake Lock, Vibration, and Service Worker all need HTTPS or localhost

<!-- GSD:stack-end -->

<!-- GSD:conventions-start source:CONVENTIONS.md -->

## Conventions

- **Indentation**: 2 spaces; semicolons always; `const` by default, `let` only when reassignment needed
- **Config objects**: `Object.freeze()` — treat as immutable; do not mutate at runtime
- **Error handling**: Silent `try/catch` with empty or `_`-named catch param; capability checks like `if (!("wakeLock" in navigator)) return`
- **Section dividers**: `/* ====== SECTION NAME ====== */` — preserve these when adding code
- **No logging**: No `console.log` in production; use browser DevTools for debugging
- **UI strings**: All user-facing text lives in the frozen `UI` object in CONFIG — do not hardcode strings in handlers

<!-- GSD:conventions-end -->

<!-- GSD:architecture-start source:ARCHITECTURE.md -->

### Overall Pattern

**Vanilla JS state machine** driving a `requestAnimationFrame` loop. Immutable config at the top, mutable state globals in the middle, DOM cache collected once at startup, then event handlers and the animation loop wire them together.

### Key Data Flow

```
User action / keypress
  → start() / stop() / reset()
    → isRunning = true / false
      → loop() (rAF)
        → render() — updates ring SVG + phase label + brightness
        → advanceToNextPhase() — when progress01 ≥ 1
          → playBeep() + vibrate() + saveHistory() (on session end)
```

### Phase Config (`PRESETS`)

Three built-in breathing patterns; each phase entry has `{ name, durationSec, breathR, theme: { bg, bgLight, accent, accentLight }, cue: { freqHz }, hint }`:

| Preset | Pattern |
|---|---|
| Relax (`relax`) | 4s inhale / 2s hold / 8s exhale / 2s hold |
| Box (`box`) | 4s inhale / 4s hold / 4s exhale / 4s hold |
| 4-7-8 (`478`) | 4s inhale / 7s hold / 8s exhale |

`savedDurations` in localStorage can override per-preset durations from the UI sliders.

### SVG Ring

`strokeDashoffset = circumference × (1 − progress01)` — 0% fill at rest, 100% at phase end. Ring geometry (radius, stroke width, circumference) is pre-calculated in the `RING` config constant.

### Browser API Fallbacks

| API | Fallback |
|---|---|
| Web Audio API | Silent (no beeps) |
| Vibration API | Ignored |
| Wake Lock API | Screen may sleep |
| Fullscreen API | Button does nothing |
| Service Worker | Loads from network |
| localStorage | Defaults only, nothing persisted |

<!-- GSD:architecture-end -->

<!-- GSD:skills-start source:skills/ -->

## Project Skills

No project skills found. Add skills to any of: `.claude/skills/`, `.agents/skills/`, `.cursor/skills/`, `.github/skills/`, or `.codex/skills/` with a `SKILL.md` index file.
<!-- GSD:skills-end -->

<!-- GSD:workflow-start source:GSD defaults -->

## GSD Workflow Enforcement

Before using Edit, Write, or other file-changing tools, start work through a GSD command so planning artifacts and execution context stay in sync.

Use these entry points:

- `/gsd-quick` for small fixes, doc updates, and ad-hoc tasks
- `/gsd-debug` for investigation and bug fixing
- `/gsd-execute-phase` for planned phase work

Do not make direct repo edits outside a GSD workflow unless the user explicitly asks to bypass it.
<!-- GSD:workflow-end -->

<!-- GSD:profile-start -->

## Developer Profile

> Profile not yet configured. Run `/gsd-profile-user` to generate your developer profile.
> This section is managed by `generate-claude-profile` -- do not edit manually.
<!-- GSD:profile-end -->
