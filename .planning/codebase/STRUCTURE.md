# Directory and File Structure

**Analysis Date:** 2026-06-03

## Repository Layout

```
mindful-breathing/
├── index.html              [1,350 lines] Main application (HTML + CSS + JS)
├── sw.js                   [50 lines]    Service worker (offline caching)
├── manifest.json           [~30 lines]   PWA manifest (metadata, icons, display)
├── icon.svg                [~200 bytes]  App icon (48×48 SVG)
├── README.md               Project documentation
├── CLAUDE.md               Project guidelines (vanilla JS, single file, offline-first)
├── .gitignore              Git ignore rules
├── .planning/              (GSD planning directory, created by workflow tools)
│   ├── codebase/           Codebase analysis documents
│   │   ├── STACK.md
│   │   ├── INTEGRATIONS.md
│   │   ├── ARCHITECTURE.md
│   │   ├── STRUCTURE.md
│   │   ├── CONVENTIONS.md
│   │   ├── TESTING.md
│   │   └── CONCERNS.md
│   └── ...                 Other GSD planning files
└── .github/                GitHub configuration (if any)
```

## File Purposes

### `index.html` (1,350 lines)
**The entire application** — HTML, CSS, and JavaScript in one file.

**Structure within index.html:**

| Line Range | Section | Purpose |
|-----------|---------|---------|
| 1–45 | HTML Head | Meta tags, title, manifest, favicon, viewport config, theme color |
| 46–1040 | CSS | Global styles, layout, animations, dark/light mode themes |
| 1044–1128 | CONFIG | Immutable constants: UI strings, breathing presets, sound/vibration settings, SVG ring geometry |
| 1131–1164 | STATE | Mutable globals: phase tracking, timing, audio context, wake lock, session state, settings |
| 1168–1209 | DOM | Cached element references (populated on init) |
| 1220–1245 | HELPERS | Utility functions: `hexToRgba()`, `getPhase()`, `getGoal()`, `formatDuration()` |
| 1256–1310 | DURATION INPUTS | Custom phase duration UI: sliders and reset button |
| 1313–1353 | PERSISTENCE | localStorage read/write: `saveSettings()`, `loadSettings()`, `saveHistory()` |
| 1356–1393 | SESSION HISTORY | History panel UI and rendering logic |
| 1396–1416 | WAKE LOCK | Screen Wake Lock API integration |
| 1419–1476 | SOUND/VIBRATION | Web Audio API (beeps) and Vibration API (haptics) |
| 1479–1537 | RENDER | SVG ring progress animation, DOM updates |
| 1540–1573 | PHASE ADVANCE | State machine: phase transitions, cycle counting, session completion |
| 1576–1613 | MAIN LOOP | `requestAnimationFrame` driver: timing, rendering, phase advancement |
| 1616–1827 | CONTROLS | Event handlers: start/stop/reset, preset selection, duration changes |
| 1829–1883 | PANELS | Info panel, settings panel, fullscreen toggle, mode indicator |
| 1886–1896 | KEYBOARD SHORTCUTS | Space (start/stop), R (reset), F (fullscreen) |
| 1898–1912 | INIT / SERVICE WORKER | Initialization routines and service worker registration |

### `sw.js` (Service Worker)

**Purpose:** Offline caching and install prompt suppression.

**Behavior:**
- Intercepts all fetch requests
- Returns cached assets if available
- Falls back to network if not cached
- Caches new responses for offline use

**Scope:** Caches `index.html`, `icon.svg`, `manifest.json` and any external resources.

### `manifest.json` (PWA Manifest)

**Purpose:** Tells browsers how to install and display the app as a PWA.

**Key fields:**
- `name`, `short_name` — app display names
- `start_url` — entry point (usually "/")
- `display` — "standalone" (hide browser UI)
- `theme_color`, `background_color` — app chrome colors
- `icons` — array of icon assets (sizes for different contexts)
- `scope` — where manifest applies

**Not modified during runtime** — static metadata only.

### `icon.svg` (App Icon)

**Purpose:** Branding and app launcher icon.

**Formats served:**
- Favicon (16×16, 32×32 in data URL in head)
- Apple touch icon (180×180, linked in manifest)
- PWA app icon (192×192, 512×512, referenced in manifest)

### `README.md`

**Purpose:** User documentation.

**Contents:**
- What the app does
- How to use it (keyboard shortcuts, settings)
- How to run locally
- Links to live demo and source
- Changelog / version history

### `CLAUDE.md` (Project Guidelines)

**Purpose:** Instructions for Claude Code when working with this repository.

**Key rules:**
- Vanilla JS only — no frameworks, no npm, no transpilers
- Single-file app — keep all code in `index.html`
- No external network requests — works fully offline
- Browser APIs gracefully degrade if unavailable
- Settings persisted to localStorage under `STORAGE_KEY` and `HISTORY_KEY`

## Code Organization Within `index.html`

### CSS Sections (lines ~50–1040)

```
1. Root variables (CSS custom properties for theming)
2. Base styles (body, fonts, backgrounds)
3. Container and card styling
4. Typography (phase, cycle, time display)
5. Progress ring SVG styles
6. Buttons and controls
7. Panels (info, settings, history)
8. Animations and transitions
9. Responsive breakpoints (@media)
10. Dark mode / Light mode overrides
11. Utility classes (.hidden, .running, .active, etc.)
```

### JavaScript Sections (lines ~1040–1912)

Each major section is preceded by a comment banner:
```javascript
/* ====== SECTION NAME ====== */
```

This makes it easy to navigate the file with Ctrl+F or IDE outline view.

## Key File Locations for Common Tasks

| Task | File | Lines | Key Variables |
|------|------|-------|----------------|
| Change breathing presets | `index.html` | 1057–1075 | `PRESETS` object |
| Update UI strings (labels) | `index.html` | ~1080 | `UI` object |
| Add new theme colors | `index.html` | ~16–22 (CSS) | `--bg`, `--accent`, etc. |
| Modify sound beep | `index.html` | 1419–1430 | `playBeep()` function |
| Customize vibration | `index.html` | 1439–1448 | `vibrate()` function |
| Add new keyboard shortcut | `index.html` | 1886–1896 | Keyboard event listeners |
| Adjust animation speed | `index.html` | ~70–75 (CSS) | `@keyframes` definitions |
| Change ring SVG geometry | `index.html` | 1231–1238 | `RING` config, ring calc |
| Modify history storage | `index.html` | 1359–1393 | `SESSION`, `saveHistory()`, `renderHistory()` |
| Adjust wake lock behavior | `index.html` | 1396–1416 | `acquireWakeLock()`, `releaseWakeLock()` |
| Update localStorage keys | `index.html` | ~1125–1127 | `STORAGE_KEY`, `HISTORY_KEY` |

## Naming Conventions

### Files
- `index.html` — main app (lowercase, single file)
- `sw.js` — service worker (lowercase, kebab-case)
- `manifest.json` — PWA manifest (lowercase, lowercase extension)
- `icon.svg` — icon asset (lowercase, descriptive)

### CSS Classes
- Component-level: `.container`, `.ringWrap`, `.presetBtn` (camelCase)
- State modifiers: `.active`, `.running`, `.hidden` (lowercase, separated by dot)
- Responsive: `@media (max-width: ...)` (inline media queries)

### JavaScript Identifiers
- Constants: `PRESETS`, `SOUND`, `STORAGE_KEY` (UPPERCASE)
- Functions: `playBeep()`, `advanceToNextPhase()` (camelCase)
- Global state: `isRunning`, `currentPhaseIndex`, `audioCtx` (camelCase)
- DOM refs: `appEl`, `phaseEl`, `timeEl`, `toggleBtn` (camelCase, optional `El` suffix)

## Import / Export Model

**Not applicable** — single file, no modules, no imports/exports.

All code is implicitly global within the `<script>` block scope. Functions, state variables, and config are accessible to each other directly.

## Where to Add New Code

| Feature Type | Add To | Example |
|--------------|--------|---------|
| New breathing preset | `PRESETS` object (config) | Copy existing preset, modify phase objects |
| New UI string | `UI` object (config) | Add key-value pair for label text |
| New sound effect | `playBeep()` or new function | Call Web Audio API in phase transition handler |
| New keyboard shortcut | Bottom of CONTROLS section | Add event listener in keyboard section |
| New panel or dialog | HTML (head/body), CSS (styles), JS (CONTROLS/PANELS) | Create div, style it, add toggle handler |
| New color theme | CSS root variables, PRESETS theme field | Add `--bg`, `--card`, `--accent` colors |
| New localStorage data | PERSISTENCE section, CONFIG keys | Add key to `STORAGE_KEY` or new key, handle read/write |
| Bug fix in state machine | PHASE ADVANCE section (lines 1540–1573) | Modify `advanceToNextPhase()` logic |
| Performance optimization | RENDER or MAIN LOOP section | Reduce DOM updates, cache calculations |

## Configuration Defaults

**Browser Storage:**
- Settings stored under: `localStorage[STORAGE_KEY]` = JSON string
- History stored under: `localStorage[HISTORY_KEY]` = JSON string
- Max history entries: 14 (oldest entries discarded)

**API Defaults:**
- Sound enabled by default (toggle in settings)
- Vibration enabled by default (toggle in settings)
- Dark mode enabled by default (toggles based on system preference)
- Active preset: "relax" (Relax breathing pattern)

## Cross-File References

```
index.html → sw.js:
  [Service worker registration at init]

index.html → manifest.json:
  [Linked in HTML head]
  [Referenced by service worker for caching]

index.html → icon.svg:
  [Favicon in head (data URL)]
  [Apple touch icon link]
  [Referenced in manifest.json]

manifest.json → icon.svg:
  [Icons array references icon paths]
```

---

*Structure analysis: 2026-06-03*
