# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the App

No build step — open `index.html` directly in a browser, or serve it:

```bash
python -m http.server 8080

# then open http://localhost:8080

```

There is no package manager, no dependencies, no compilation.

## Architecture

The entire application lives in a single file: `index.html` (~1,350 lines). It is organized into clearly labeled sections within one `<script>` block:

- **CONFIG** — constants: UI strings, breathing presets (Relax / Box / 4-7-8), sound settings, SVG ring geometry
- **STATE** — mutable globals: current phase, cycle count, elapsed time, AudioContext, Wake Lock handle
- **DOM** — cached element references
- **HELPERS** — color conversion, theme application, phase lookup
- **DURATION INPUTS** — per-phase timer customization
- **PERSISTENCE** — localStorage read/write for settings and session history
- **SESSION HISTORY** — save, load, and render past sessions
- **WAKE LOCK** — Screen Wake Lock API to prevent sleep during a session
- **SOUND/VIBRATION** — Web Audio API beeps and Vibration API haptics
- **RENDER** — SVG progress ring, countdown text, phase label, animations
- **PHASE ADVANCE** — cycle through Inhale → Hold → Exhale → Hold2 phases
- **MAIN LOOP** — `requestAnimationFrame` timer driving smooth animation
- **CONTROLS** — start/stop/reset handlers, 3-second countdown overlay
- **FULLSCREEN** — Fullscreen API toggle
- **KEYBOARD SHORTCUTS** — Space (start/stop), R (reset), F (fullscreen)

## Key Constraints

- **Vanilla JS only** — no frameworks, no npm, no transpilers. Keep it that way.
- **Single file** — all HTML, CSS, and JS stays in `index.html`.
- **No external network requests** — the app works fully offline.
- Browser APIs in use: Web Audio API, Vibration API, Screen Wake Lock API, Fullscreen API — these require a secure context (HTTPS or localhost) and may need graceful degradation for unsupported browsers.
- Settings and session history are persisted to `localStorage` under the keys defined in the CONFIG section.

## Live Demo

https://bielinskilukasz.github.io/mindful-breathing/

<!-- GSD:project-start source:PROJECT.md -->

## Project

**Mindful Breathing v0.4**

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

## Languages

- HTML5 - Markup, structure, and meta tags for PWA
- CSS3 - Styling, animations (keyframes), media queries for responsive design
- ECMAScript 2023 (JavaScript) - All application logic, Web APIs, browser interactions
- None - No transpilers, build tools, or secondary languages used

## Runtime

- Web browser (ES2023+ support required)
- Modern browser with Web Audio API, Vibration API, Screen Wake Lock API, Fullscreen API support
- Vanilla JavaScript only
- No Node.js, no server-side runtime
- Single HTML file executes directly in browser

## Package Manager

- Not used - Zero external dependencies
- No `package.json`, no npm, no yarn, no pnpm
- All functionality uses browser-native APIs

## Frameworks

- None - Pure vanilla HTML/CSS/JavaScript
- No build tools - Open `index.html` directly or serve with `python -m http.server`
- No transpilation, no bundling, no minification pipeline
- No linting or formatting tools configured

## Key Browser APIs (No External Libraries)

- **Web Audio API** (`AudioContext`, `OscillatorNode`, `GainNode`) - Sound cues with sine wave tone generation
- **Vibration API** (`navigator.vibrate()`) - Haptic feedback on phase transitions
- **Screen Wake Lock API** (`navigator.wakeLock.request()`) - Prevents display sleep during sessions
- **Fullscreen API** (`Element.requestFullscreen()`) - Immersive presentation mode
- **localStorage** - Persistent settings and session history storage
- **Service Worker API** - Offline capability and PWA installation
- **requestAnimationFrame** - Smooth animation and frame-synced timing

## Configuration

- File: `manifest.json`
- Contains: app name, short name, description, icons, display mode, theme color
- Enables: "Add to Home Screen" on Android/iOS, standalone display mode, custom icon and splash screen
- Primary: `icon.svg` (scalable SVG icon, supports maskable purpose for adaptive icons)
- Favicon: Inline data URI in `<head>` (no external file needed)
- File: `sw.js`
- Cache version: `mb-v1`
- Assets cached: `./`, `./index.html`, `./icon.svg`, `./manifest.json`
- Fallback: Network on cache miss

## HTML Meta Tags for Mobile

- Responsive viewport scaling
- Full-screen standalone mode on iOS
- Custom status bar appearance
- Custom app name on iOS home screen
- Chrome/Android theme color

## CSS Features

- CSS Custom Properties (CSS variables) - `--bg`, `--accent`, `--accentSoft`, `--text`
- CSS Gradients - Implicit in theme color system
- CSS Animations - `@keyframes` for phase fade-in, idle pulse, countdown pop
- CSS Media Queries - Breakpoints at 480px, 360px, and landscape mode (max-height: 500px)
- CSS Transitions - 220ms theme color transitions, button states
- CSS Grid - `display: grid; place-items: center` for centering
- CSS Flexbox - Layout for controls, status rows, preset buttons
- CSS Filters - `brightness()` filter tied to breathing phase progress
- All CSS is hand-written in a single `<style>` block in `index.html`
- No Bootstrap, Tailwind, or Material Design
- Responsive design via media queries, not a utility library

## JavaScript Features

- Arrow functions
- Template literals
- Destructuring (objects and arrays)
- `const`/`let` declarations
- Array methods: `.map()`, `.forEach()`, `.reduce()`, `.filter()`, `.closest()`
- `Object.freeze()` for immutable config constants
- Object spread syntax: `{...obj}`
- `Math.max()`, `Math.min()`, `Math.round()`, `Math.floor()`, `Math.ceil()`
- `Number.isFinite()`, `String().padStart()`
- Async/await for Wake Lock and AudioContext initialization
- Event listeners with delegation
- Code targets modern evergreen browsers directly
- No Babel, no TypeScript
- No polyfills needed for target browsers

## Platform Requirements

- Any text editor
- Any modern browser (Chrome, Edge, Firefox, Safari)
- Optional: Local HTTP server (Python, Node.js http-server, or equivalent) for HTTPS-dependent APIs
- GitHub Pages (HTTPS required for Web Audio, Wake Lock, Vibration APIs)
- URL: https://bielinskilukasz.github.io/mindful-breathing/
- Chrome: ✅ Desktop & Mobile
- Edge: ✅ Desktop & Mobile
- Firefox: ✅ Desktop & Mobile
- Safari: ✅ Desktop & Mobile
- Web Audio API, Vibration API, Screen Wake Lock API, Fullscreen API all require HTTPS or localhost
- Service Worker registration also requires secure context
- Opening `file://` locally disables these APIs but app still functions (basic breathing timer only)

## File Structure (Single File Architecture)

- `index.html` (~1,350 lines) - Contains entire application: HTML structure + CSS styling + JavaScript logic
- `manifest.json` - PWA metadata
- `sw.js` - Service Worker for caching
- `icon.svg` - PWA icon
- `README.md` - Documentation
- `index.html`: ~1,350 lines (HTML ~200, CSS ~670, JavaScript ~1,040)
- `sw.js`: 11 lines
- `manifest.json`: 18 lines
- **Total application code: ~1,370 lines (JavaScript dominant)**

## Key Architectural Constraint

- All HTML, CSS, JavaScript in one file
- No module system, no imports/exports
- All variables global scope (intentional for single-file simplicity)
- No separate components or build artifacts
- Freezes `Object` constants at top to simulate immutability

<!-- GSD:stack-end -->

<!-- GSD:conventions-start source:CONVENTIONS.md -->

## Conventions

## Naming Patterns

- Single-file app convention: `index.html` (main application)
- Service worker: `sw.js` (kebab-case)
- Asset files: `icon.svg`, `manifest.json` (lowercase)
- camelCase throughout: `playBeep()`, `advanceToNextPhase()`, `updateStatusUI()`, `formatDuration()`
- Helper/utility functions grouped in HELPERS section: `hexToRgba()`, `getPhase()`, `getGoal()`
- Getter functions: `getPhase()`, `getGoal()`, `getElapsedDisplay()`, `isFullscreen()`
- Event handler functions: `start()`, `stop()`, `reset()`, `loop()`
- Render/update functions: `render()`, `renderSessionBar()`, `renderHistory()`, `updateStatusUI()`
- No underscore prefix convention; functions are public by default
- Global state: camelCase with clear intent: `isRunning`, `soundEnabled`, `vibeEnabled`, `cycleCount`, `elapsedMs`
- DOM references: camelCase suffixed with `El` or unadorned: `appEl`, `phaseEl`, `timeEl`, `soundToggle`, `toggleBtn`
- Constants: UPPERCASE with underscores: `STORAGE_KEY`, `HISTORY_KEY`, `SOUND`, `VIBRATION`, `RING`, `COUNTDOWN`, `SESSION`
- Object.freeze() used for immutable constants (CONFIG, STATE, PRESETS, etc.)
- Temporary/loop variables: short names like `i`, `n`, `s`, `m`, `d`, `h`, `b`, `t0`
- Object literal keys: camelCase: `durationSec`, `freqHz`, `breathR`, `phaseStartTs`, `cue`, `theme`, `accent`, `bg`
- UI text constants in `UI` frozen object: `cyclePrefix`, `secondsLabel`, `statusRunning`, `btnStart`
- Frozen preset/config objects: `PRESETS`, `SOUND`, `VIBRATION`, `RING`, `COUNTDOWN`, `SESSION`, `QUOTES`, `MODE_LABELS`
- CSS class names: camelCase for component-style, kebab-case for modifier states: `.container`, `.phase`, `.ringWrap`, `.presetBtn.active`, `.running .runningDot`

## Code Style

- No explicit formatter used (vanilla JS in HTML)
- Indentation: 2 spaces (visible in CSS and inline `<script>`)
- Line length: pragmatic, averages 80–100 chars
- Semicolons: used throughout (not optional)
- Trailing commas: not used in multi-line arrays/objects
- No ESLint or automated linting configured
- No build step; code runs directly in browser
- Manual code review conventions observed in practices below
- No var; exclusively let/const throughout
- Use `const` by default, `let` only when reassignment needed
- Prefer Object.freeze() for immutable config objects
- Ternary operators used freely for simple conditions: `x ? y : z`
- Early returns in handlers to avoid nesting

## Import Organization

- Not applicable (no module system, single file)
- Entire app under one `<script>` block
- Global variables: `UI`, `PRESETS`, `SOUND`, `VIBRATION`, `RING`, `COUNTDOWN`, `SESSION`, `STORAGE_KEY`, `HISTORY_KEY`, `QUOTES`
- Global state: `currentPhaseIndex`, `cycleCount`, `elapsedMs`, `phaseStartTs`, `rafId`, `isRunning`, `audioCtx`, `wakeLockSentinel`, `sessionResumeTs`, `totalSessionMs`, `activePresetKey`, `savedDurations`, `isCountingDown`, `countdownTimers`, `soundEnabled`, `vibeEnabled`
- DOM cache: `appEl`, `phaseEl`, `timeEl`, `cycleEl`, `toggleBtn`, `resetBtn`, `statusText`, `ringProgressEl`, etc. (collected once at startup)

## Error Handling

- Silent failures with try/catch, empty catch blocks: `try { ... } catch (_) {}`
- Used for localStorage access: `saveSettings()` (line 1316), `loadSettings()` (lines 1329–1352), `saveHistory()` (line 1359), `renderHistory()` (line 1370)
- Used for Web APIs that may not exist: navigator.wakeLock (line 1399), navigator.vibrate (line 1447), AudioContext resume (line 1770)
- Browser compatibility: fallback checks like `if (!("wakeLock" in navigator)) return;` (line 1399)
- No throw statements; app gracefully degrades when APIs unavailable
- Fullscreen methods use conditional chaining: `(el.requestFullscreen || el.webkitRequestFullscreen).call(el).catch(() => {})` (line 1876)

## Logging

- No console.log or debug logging in production code
- Application relies on observable DOM state changes
- Errors silently fail (catch blocks with empty statements)
- No debug mode; browser DevTools required for inspection

## Comments

- Section dividers: `/* ====== SECTION NAME ====== */` (clear demarcation throughout)
- Inline explanations for non-obvious logic: "breathR: [startFraction, endFraction] — 0=contracted, 1=expanded" (line 1056)
- Phase description: "Phase sequence: 2s Pause -> 4s Inhale -> 2s Pause -> 8s Exhale" (line 1054)
- API behavior notes: "created on first Start click" for audioCtx (line 1144), "kept on pause" for elapsedMs (line 1137)
- Browser quirk notes: "// performance.now() - elapsedMs" (line 1138), "// no 0" countdown comment (line 1605)
- Not used; vanilla JS without type hints
- Functions have inline comments where parameter intent is non-obvious

## Function Design

- Typical range: 5–30 lines
- Largest functions: `renderHistory()` (25 lines, data formatting), `showCountdown()` (30 lines, orchestration)
- Helper functions: 2–5 lines (e.g., `getPhase()` at line 1230, `getGoal()` at line 1234)
- Event handler delegation used to keep individual handlers concise
- Most functions 0–2 parameters
- Config passed via global constants (SOUND, VIBRATION, etc.) rather than parameters
- Callbacks used sparingly: `showCountdown(callback)` (line 1621)
- No destructuring; parameters are primitives or simple object lookups
- Most functions return void (side-effect driven: state updates, DOM manipulation)
- Query/getter functions return values: `getPhase()`, `getGoal()`, `getElapsedDisplay()`, `isFullscreen()`
- Render functions have no return (update DOM/state directly)
- Array methods (map, forEach) used for side-effects, not chained

## Module Design

- Single file; no modules or exports
- All functions and state are implicitly global within the script scope
- Immediately-loaded initialization (lines 1905–1912): `loadSettings()`, `updateModeIndicator()`, `buildDurationInputs()`, `renderHistory()`, `render()`, `updateStatusUI()`, `breathCircleEl.classList.add("idle")`
- Event listeners attached at startup (not dynamically added during execution)
- State mutations happen in response to user actions or animation frames
- Not applicable (no file structure)

<!-- GSD:conventions-end -->

<!-- GSD:architecture-start source:ARCHITECTURE.md -->

## Architecture

## Overall Pattern

- **Vanilla JS state machine** — tracks current breathing phase (Inhale/Hold/Exhale/Hold2), cycle count, elapsed time
- **requestAnimationFrame loop** (`loop()` function) drives smooth animation and state transitions
- **Event-driven controls** — user clicks and keyboard input trigger state changes
- **Immutable config** — breathing presets, UI strings, and geometry are frozen at startup
- **Graceful API degradation** — optional browser APIs (Web Audio, Vibration, Wake Lock, Fullscreen) fail silently if unavailable

## Architecture Layers

### 1. Configuration Layer (lines 1044–1128)

- `PRESETS` — 3 breathing patterns: Relax (4s inhale / 6s exhale), Box (4s hold each), 4-7-8 (4s inhale / 7s hold / 8s exhale)
- `SOUND` — Web Audio beep frequencies and envelope
- `VIBRATION` — haptic patterns for phase transitions
- `RING` — SVG ring geometry (stroke width, circumference calculation)
- `COUNTDOWN` — 3-second overlay animation timing
- `SESSION` — history storage, max entries, date formatting
- `UI` — all user-facing strings (labels, buttons, status text)

### 2. State Layer (lines 1131–1164)

- **Phase state:** `currentPhaseIndex`, `phaseStartTs`, `activePhases` array
- **Timing:** `elapsedMs`, `sessionResumeTs`, `totalSessionMs`, `cycleCount`
- **Audio:** `audioCtx` (created lazily on first Start), `oscillator`, `gain` nodes
- **UI state:** `isRunning`, `isCountingDown`, `countdownTimers`, `activePresetKey`
- **Browser state:** `wakeLockSentinel`, `rafId` (animation frame ID)
- **Settings:** `soundEnabled`, `vibeEnabled`, `savedDurations`, `isDarkMode`

### 3. DOM Layer (lines 1168–1209)

- Ring SVG elements: `ringSvg`, `ringBg`, `ringProgress`, `timeEl`
- Control elements: `toggleBtn`, `resetBtn`, `presetBtns`, `durationInputs`
- UI elements: `phaseEl`, `cycleEl`, `statusText`, `modeIndicatorEl`
- Panel elements: `infoPanelEl`, `settingsPanelEl`, `historyListEl`
- Toggles: `soundToggle`, `vibeToggle`, `darkToggle`

### 4. Phase Advancement (lines 1540–1573)

```

```

### 5. Animation Loop (lines 1576–1613)

```

```

### 6. Rendering (lines 1479–1537)

```

```

- Circumference = 2π × radius (calculated in RING config)
- Stroke-dashoffset = circumference × (1 - progress01) — creates animation from 0% to 100% fill

### 7. Persistence Layer (lines 1313–1393)

- `soundEnabled`, `vibeEnabled`, `isDarkMode`, `savedDurations` (per-preset custom durations)
- Read on app load: `loadSettings()`
- Written on every toggle or slider change: `saveSettings()`
- Array of completed sessions: `[{ date: "2026-06-03", durationMs: 600000, cycles: 10 }, ...]`
- Appended on session finish: `saveHistory()`
- Rendered in history panel: `renderHistory()` (max last 14 entries)

## Data Flow

```

```

## Key Abstractions

```javascript

```

## Browser APIs Used

| API | Purpose | Fallback |
|-----|---------|----------|
| **Web Audio API** | Generate beep tones on phase transitions | Silent (no sound) |
| **Vibration API** | Haptic feedback on phase transitions | Ignored on unsupported devices |
| **Wake Lock API** | Keep screen on during session | Session proceeds, screen may sleep |
| **Fullscreen API** | Immersive mode toggle | Window fullscreen unavailable |
| **Service Worker** | Offline caching and install prompts | App still loads from cache or network |
| **localStorage** | Persist settings and history | Defaults used, nothing saved |

## Entry Points

## Scaling Constraints

- **File size:** Single file now ~1,350 lines; approaches inflection (~2,500 lines) where monolithic becomes unwieldy
- **localStorage quota:** ~5–10 MB depending on browser; currently uses <1 KB (history capped at 14 entries)
- **Animation frame rate:** Fixed 60 FPS by requestAnimationFrame; no throttling needed
- **Global state:** Mutable globals at module scope; isolated from framework or modularization overhead

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
