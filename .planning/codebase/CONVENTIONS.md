# Coding Conventions

**Analysis Date:** 2026-06-03

## Naming Patterns

**Files:**
- Single-file app convention: `index.html` (main application)
- Service worker: `sw.js` (kebab-case)
- Asset files: `icon.svg`, `manifest.json` (lowercase)

**Functions:**
- camelCase throughout: `playBeep()`, `advanceToNextPhase()`, `updateStatusUI()`, `formatDuration()`
- Helper/utility functions grouped in HELPERS section: `hexToRgba()`, `getPhase()`, `getGoal()`
- Getter functions: `getPhase()`, `getGoal()`, `getElapsedDisplay()`, `isFullscreen()`
- Event handler functions: `start()`, `stop()`, `reset()`, `loop()`
- Render/update functions: `render()`, `renderSessionBar()`, `renderHistory()`, `updateStatusUI()`
- No underscore prefix convention; functions are public by default

**Variables:**
- Global state: camelCase with clear intent: `isRunning`, `soundEnabled`, `vibeEnabled`, `cycleCount`, `elapsedMs`
- DOM references: camelCase suffixed with `El` or unadorned: `appEl`, `phaseEl`, `timeEl`, `soundToggle`, `toggleBtn`
- Constants: UPPERCASE with underscores: `STORAGE_KEY`, `HISTORY_KEY`, `SOUND`, `VIBRATION`, `RING`, `COUNTDOWN`, `SESSION`
- Object.freeze() used for immutable constants (CONFIG, STATE, PRESETS, etc.)
- Temporary/loop variables: short names like `i`, `n`, `s`, `m`, `d`, `h`, `b`, `t0`

**Types/Objects:**
- Object literal keys: camelCase: `durationSec`, `freqHz`, `breathR`, `phaseStartTs`, `cue`, `theme`, `accent`, `bg`
- UI text constants in `UI` frozen object: `cyclePrefix`, `secondsLabel`, `statusRunning`, `btnStart`
- Frozen preset/config objects: `PRESETS`, `SOUND`, `VIBRATION`, `RING`, `COUNTDOWN`, `SESSION`, `QUOTES`, `MODE_LABELS`
- CSS class names: camelCase for component-style, kebab-case for modifier states: `.container`, `.phase`, `.ringWrap`, `.presetBtn.active`, `.running .runningDot`

## Code Style

**Formatting:**
- No explicit formatter used (vanilla JS in HTML)
- Indentation: 2 spaces (visible in CSS and inline `<script>`)
- Line length: pragmatic, averages 80–100 chars
- Semicolons: used throughout (not optional)
- Trailing commas: not used in multi-line arrays/objects

**Linting:**
- No ESLint or automated linting configured
- No build step; code runs directly in browser
- Manual code review conventions observed in practices below

**Observed Style Rules:**
- No var; exclusively let/const throughout
- Use `const` by default, `let` only when reassignment needed
- Prefer Object.freeze() for immutable config objects
- Ternary operators used freely for simple conditions: `x ? y : z`
- Early returns in handlers to avoid nesting

## Import Organization

**Path Aliases:**
- Not applicable (no module system, single file)

**Global Scope:**
- Entire app under one `<script>` block
- Global variables: `UI`, `PRESETS`, `SOUND`, `VIBRATION`, `RING`, `COUNTDOWN`, `SESSION`, `STORAGE_KEY`, `HISTORY_KEY`, `QUOTES`
- Global state: `currentPhaseIndex`, `cycleCount`, `elapsedMs`, `phaseStartTs`, `rafId`, `isRunning`, `audioCtx`, `wakeLockSentinel`, `sessionResumeTs`, `totalSessionMs`, `activePresetKey`, `savedDurations`, `isCountingDown`, `countdownTimers`, `soundEnabled`, `vibeEnabled`
- DOM cache: `appEl`, `phaseEl`, `timeEl`, `cycleEl`, `toggleBtn`, `resetBtn`, `statusText`, `ringProgressEl`, etc. (collected once at startup)

**Organization within script:**
Order of declaration in `index.html`:
1. CONFIG (UI, PRESETS, SOUND, VIBRATION, RING, COUNTDOWN, SESSION, STORAGE_KEY, HISTORY_KEY, QUOTES) — lines 1044–1128
2. STATE (mutable globals: phase tracking, timing, audio, wake lock, session tracking, settings) — lines 1131–1164
3. DOM (cached element references) — lines 1168–1209
4. HELPERS (utility functions) — lines 1220–1245
5. DURATION INPUTS (phase duration customization) — lines 1256–1310
6. PERSISTENCE (localStorage read/write) — lines 1313–1353
7. SESSION HISTORY (history UI and logic) — lines 1356–1393
8. WAKE LOCK (screen wake lock API) — lines 1396–1416
9. SOUND/VIBRATION (Web Audio and haptics) — lines 1419–1476
10. RENDER (SVG ring progress and UI updates) — lines 1479–1537
11. PHASE ADVANCE (cycle state machine) — lines 1540–1573
12. MAIN LOOP (requestAnimationFrame animation) — lines 1576–1613
13. CONTROLS (button handlers and state transitions) — lines 1616–1827
14. Panels (info, settings, fullscreen) — lines 1829–1883
15. KEYBOARD SHORTCUTS — lines 1886–1896
16. SERVICE WORKER registration and init — lines 1898–1912

## Error Handling

**Patterns:**
- Silent failures with try/catch, empty catch blocks: `try { ... } catch (_) {}`
- Used for localStorage access: `saveSettings()` (line 1316), `loadSettings()` (lines 1329–1352), `saveHistory()` (line 1359), `renderHistory()` (line 1370)
- Used for Web APIs that may not exist: navigator.wakeLock (line 1399), navigator.vibrate (line 1447), AudioContext resume (line 1770)
- Browser compatibility: fallback checks like `if (!("wakeLock" in navigator)) return;` (line 1399)
- No throw statements; app gracefully degrades when APIs unavailable
- Fullscreen methods use conditional chaining: `(el.requestFullscreen || el.webkitRequestFullscreen).call(el).catch(() => {})` (line 1876)

## Logging

**Framework:** None (no logging framework used)

**Patterns:**
- No console.log or debug logging in production code
- Application relies on observable DOM state changes
- Errors silently fail (catch blocks with empty statements)
- No debug mode; browser DevTools required for inspection

## Comments

**When to Comment:**
- Section dividers: `/* ====== SECTION NAME ====== */` (clear demarcation throughout)
- Inline explanations for non-obvious logic: "breathR: [startFraction, endFraction] — 0=contracted, 1=expanded" (line 1056)
- Phase description: "Phase sequence: 2s Pause -> 4s Inhale -> 2s Pause -> 8s Exhale" (line 1054)
- API behavior notes: "created on first Start click" for audioCtx (line 1144), "kept on pause" for elapsedMs (line 1137)
- Browser quirk notes: "// performance.now() - elapsedMs" (line 1138), "// no 0" countdown comment (line 1605)

**JSDoc/TSDoc:**
- Not used; vanilla JS without type hints
- Functions have inline comments where parameter intent is non-obvious

## Function Design

**Size:**
- Typical range: 5–30 lines
- Largest functions: `renderHistory()` (25 lines, data formatting), `showCountdown()` (30 lines, orchestration)
- Helper functions: 2–5 lines (e.g., `getPhase()` at line 1230, `getGoal()` at line 1234)
- Event handler delegation used to keep individual handlers concise

**Parameters:**
- Most functions 0–2 parameters
- Config passed via global constants (SOUND, VIBRATION, etc.) rather than parameters
- Callbacks used sparingly: `showCountdown(callback)` (line 1621)
- No destructuring; parameters are primitives or simple object lookups

**Return Values:**
- Most functions return void (side-effect driven: state updates, DOM manipulation)
- Query/getter functions return values: `getPhase()`, `getGoal()`, `getElapsedDisplay()`, `isFullscreen()`
- Render functions have no return (update DOM/state directly)
- Array methods (map, forEach) used for side-effects, not chained

## Module Design

**Exports:**
- Single file; no modules or exports
- All functions and state are implicitly global within the script scope

**Patterns:**
- Immediately-loaded initialization (lines 1905–1912): `loadSettings()`, `updateModeIndicator()`, `buildDurationInputs()`, `renderHistory()`, `render()`, `updateStatusUI()`, `breathCircleEl.classList.add("idle")`
- Event listeners attached at startup (not dynamically added during execution)
- State mutations happen in response to user actions or animation frames

**Barrel Files:**
- Not applicable (no file structure)

---

*Convention analysis: 2026-06-03*
