# System Architecture

**Analysis Date:** 2026-06-03

## Overall Pattern

The mindful-breathing app is a **single-file Progressive Web App** implemented as a state machine with an animation loop. The entire application logic lives in `index.html` (~1,350 lines) with no dependencies, build step, or external imports.

**Core Pattern:**
- **Vanilla JS state machine** — tracks current breathing phase (Inhale/Hold/Exhale/Hold2), cycle count, elapsed time
- **requestAnimationFrame loop** (`loop()` function) drives smooth animation and state transitions
- **Event-driven controls** — user clicks and keyboard input trigger state changes
- **Immutable config** — breathing presets, UI strings, and geometry are frozen at startup
- **Graceful API degradation** — optional browser APIs (Web Audio, Vibration, Wake Lock, Fullscreen) fail silently if unavailable

## Architecture Layers

### 1. Configuration Layer (lines 1044–1128)
Frozen immutable constants that define app behavior:
- `PRESETS` — 3 breathing patterns: Relax (4s inhale / 6s exhale), Box (4s hold each), 4-7-8 (4s inhale / 7s hold / 8s exhale)
- `SOUND` — Web Audio beep frequencies and envelope
- `VIBRATION` — haptic patterns for phase transitions
- `RING` — SVG ring geometry (stroke width, circumference calculation)
- `COUNTDOWN` — 3-second overlay animation timing
- `SESSION` — history storage, max entries, date formatting
- `UI` — all user-facing strings (labels, buttons, status text)

### 2. State Layer (lines 1131–1164)
Mutable global variables tracking:
- **Phase state:** `currentPhaseIndex`, `phaseStartTs`, `activePhases` array
- **Timing:** `elapsedMs`, `sessionResumeTs`, `totalSessionMs`, `cycleCount`
- **Audio:** `audioCtx` (created lazily on first Start), `oscillator`, `gain` nodes
- **UI state:** `isRunning`, `isCountingDown`, `countdownTimers`, `activePresetKey`
- **Browser state:** `wakeLockSentinel`, `rafId` (animation frame ID)
- **Settings:** `soundEnabled`, `vibeEnabled`, `savedDurations`, `isDarkMode`

### 3. DOM Layer (lines 1168–1209)
Cached element references collected once at startup:
- Ring SVG elements: `ringSvg`, `ringBg`, `ringProgress`, `timeEl`
- Control elements: `toggleBtn`, `resetBtn`, `presetBtns`, `durationInputs`
- UI elements: `phaseEl`, `cycleEl`, `statusText`, `modeIndicatorEl`
- Panel elements: `infoPanelEl`, `settingsPanelEl`, `historyListEl`
- Toggles: `soundToggle`, `vibeToggle`, `darkToggle`

**Why caching matters:** DOM lookups avoided in the animation loop (60fps); all queries happen once at initialization.

### 4. Phase Advancement (lines 1540–1573)
State machine controlling breathing cycle:
```
PHASE SEQUENCE (per preset):
  currentPhaseIndex → (durationSec elapsed) → nextPhaseIndex

Relax example:
  0: Pause (2s)   →  1: Inhale (4s)   →  2: Pause (2s)   →  3: Exhale (6s)  →  [cycle++, return to 0]

Box example:
  0: Inhale (4s)  →  1: Hold (4s)     →  2: Exhale (4s)  →  3: Hold (4s)    →  [cycle++, return to 0]
```

Key function: `advanceToNextPhase(nowTs)` checks if current phase duration elapsed, triggers effects (sound, vibration, color change), updates UI, and returns true when a full breathing cycle completes.

### 5. Animation Loop (lines 1576–1613)
Core rendering engine:
```
loop(nowTs):
  1. Calculate elapsed time in current phase: progress01 = (nowTs - phaseStartTs) / phaseDurationMs
  2. Cap progress at 1.0 (phase complete, but not advanced yet)
  3. Call advanceToNextPhase() — may transition to next phase or complete cycle
  4. Calculate seconds-left display: secondsLeftInt = ceil(remainingMs / 1000)
  5. Call render(progress01, secondsLeftInt) — updates ring SVG, time text, colors
  6. If running, schedule next frame: rafId = requestAnimationFrame(loop)
```

Run condition: `isRunning` flag gates whether loop reschedules itself.

### 6. Rendering (lines 1479–1537)
SVG-based animated progress ring:
```
render(progress01, secondsLeftInt):
  1. Update SVG stroke-dashoffset to animate ring fill (0 = empty, circumference = full)
  2. Update countdown text to remaining seconds
  3. Apply phase color (theme.accent) to ring and phase label
  4. Call renderSessionBar() — width: (totalCyclesComplete / estimatedTotalCycles) * 100%
  5. Call updateStatusUI() — updates status text, cycle counter, elapsed time display
```

**SVG Ring Math:**
- Circumference = 2π × radius (calculated in RING config)
- Stroke-dashoffset = circumference × (1 - progress01) — creates animation from 0% to 100% fill

### 7. Persistence Layer (lines 1313–1393)
Two types of data persisted to `localStorage`:

**Settings** (STORAGE_KEY):
- `soundEnabled`, `vibeEnabled`, `isDarkMode`, `savedDurations` (per-preset custom durations)
- Read on app load: `loadSettings()`
- Written on every toggle or slider change: `saveSettings()`

**Session History** (HISTORY_KEY):
- Array of completed sessions: `[{ date: "2026-06-03", durationMs: 600000, cycles: 10 }, ...]`
- Appended on session finish: `saveHistory()`
- Rendered in history panel: `renderHistory()` (max last 14 entries)

Both use try/catch to fail silently if localStorage unavailable (private mode browsers, quota exceeded).

## Data Flow

```
User Interaction
    ↓
  [Click/Key Event]
    ↓
 Control Handler (start/stop/reset/preset-change)
    ↓
[State Update: isRunning, activePhases, activePresetKey, etc.]
    ↓
[requestAnimationFrame triggered (if isRunning)]
    ↓
  loop(nowTs) — advances phase, calculates progress
    ↓
  render() — updates DOM (SVG ring, text, colors)
    ↓
  [60 FPS visual feedback]
    ↓
[Session Complete] → saveHistory() → localStorage
    ↓
[Settings Change] → saveSettings() → localStorage
```

## Key Abstractions

**1. Phase Objects:**
```javascript
{
  name: "Inhale",
  durationSec: 4,
  breathR: [0, 1],        // lung expansion 0=contracted → 1=expanded
  theme: { accent: "#...", bg: "#..." },
  cue: "IN",
  hint: "Breathe in slowly..."
}
```

**2. Preset Arrays:**
Each preset is an array of phase objects that cycle continuously until user stops.

**3. Configuration Immutability:**
All config objects wrapped in `Object.freeze()` to prevent accidental mutations during runtime.

## Browser APIs Used

| API | Purpose | Fallback |
|-----|---------|----------|
| **Web Audio API** | Generate beep tones on phase transitions | Silent (no sound) |
| **Vibration API** | Haptic feedback on phase transitions | Ignored on unsupported devices |
| **Wake Lock API** | Keep screen on during session | Session proceeds, screen may sleep |
| **Fullscreen API** | Immersive mode toggle | Window fullscreen unavailable |
| **Service Worker** | Offline caching and install prompts | App still loads from cache or network |
| **localStorage** | Persist settings and history | Defaults used, nothing saved |

All APIs checked at runtime with feature detection; app continues without them.

## Entry Points

1. **Initialization (lines 1905–1912):**
   - Load settings from localStorage
   - Build duration input controls
   - Cache DOM references
   - Render initial state
   - Register service worker

2. **User-Triggered:**
   - `start()` — user clicks Start or presses Space
   - `stop()` — user clicks Stop or presses Space again
   - `reset()` — user clicks Reset or presses R
   - Preset/duration changes trigger settings save

3. **Browser-Triggered:**
   - `loop(nowTs)` — called by requestAnimationFrame (~60 FPS)
   - Window `resize` event — responsive layout adjustments
   - `load` event — initialization entry point

## Scaling Constraints

- **File size:** Single file now ~1,350 lines; approaches inflection (~2,500 lines) where monolithic becomes unwieldy
- **localStorage quota:** ~5–10 MB depending on browser; currently uses <1 KB (history capped at 14 entries)
- **Animation frame rate:** Fixed 60 FPS by requestAnimationFrame; no throttling needed
- **Global state:** Mutable globals at module scope; isolated from framework or modularization overhead

---

*Architecture analysis: 2026-06-03*
