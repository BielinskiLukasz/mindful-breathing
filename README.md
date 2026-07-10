# Mindful Breathing

![Status](https://img.shields.io/badge/status-stable-brightgreen)
![Version](https://img.shields.io/badge/version-1.0.0-blue)
![HTML5](https://img.shields.io/badge/HTML-5-E34F26?logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS-3-1572B6?logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/ECMAScript-2023-F7DF1E?logo=javascript&logoColor=black)
![License](https://img.shields.io/badge/License-MIT-yellow)

A distraction-free guided breathing timer for relaxation, focus, and mindfulness practice.  
No backend. No dependencies. No installation. Works fully offline.

**[Live demo →](https://bielinskilukasz.github.io/mindful-breathing/)**

---

<img width="630" height="635" alt="Mindful Breathing app screenshot" src="https://github.com/user-attachments/assets/4ba25a5b-f2a2-459b-9db8-e9924f370a4b" />

---

## Table of Contents

- [Features](#features)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Breathing Modes](#breathing-modes)
- [History & Data Management](#history--data-management)
- [Browser Compatibility](#browser-compatibility)
- [Development](#development)
- [Design Decisions](#design-decisions)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [Troubleshooting](#troubleshooting)
- [License](#license)

---

## Features

**Core experience**
- Smooth animated SVG ring driven by `requestAnimationFrame`, synchronized with each breathing phase
- Real-time session progress bar that fills continuously across cycles
- Ambient brightness animation — the whole UI breathes with you
- Per-phase atmospheric background tints in light mode (sage, lavender, sky, cream) with 220ms fade transitions
- Visible mode indicator above the cycle counter (e.g. `RELAX`, `BOX`, `4-7-8`) — updates live when mode changes

**Customization**
- Settings panel (`⚙`) with breathing mode selection, per-phase duration sliders (1–30 s), and cycle count (1–20)
- Sound cues via Web Audio API — per-phase sine tones with a toggle
- Haptic feedback via Vibration API (Android/Chrome) — with a toggle
- Light and dark themes, each with WCAG AA contrast across all UI elements

**Session management**
- Session history — last 14 sessions, displayed with date, duration, and cycle count
- Incomplete session tracking — sessions paused after at least one full cycle are saved with an "Incomplete" label
- Export as JSON or CSV, import from JSON (merge with dedup), and clear with a confirmation dialog

**Platform & accessibility**
- Installable PWA — works offline, add to home screen on mobile
- Screen Wake Lock prevents display sleep during sessions
- Fullscreen mode for an immersive experience
- Landscape CSS Grid layout — history panel and breathing ring side-by-side on wider screens
- Keyboard shortcuts: `Space` start/pause · `R` reset · `F` fullscreen
- Info panel (`ⓘ`) with keyboard shortcuts, platform notes, and app version

---

## Getting Started

No build step required. Clone and open:

```bash
git clone https://github.com/BielinskiLukasz/mindful-breathing.git
cd mindful-breathing
open index.html   # or double-click in your file explorer
```

Or serve locally to enable all browser APIs (Web Audio, Wake Lock, Service Worker require a secure context):

```bash
python -m http.server 8080
# then open http://localhost:8080
```

> **Note:** Opening via `file://` disables Web Audio, Wake Lock, and Service Worker. The breathing timer itself still works.

---

## Usage

### Settings panel (`⚙`)

Click the gear icon in the top-left corner to open the settings modal. Configure:

| Setting | Description |
|---|---|
| Breathing mode | Relax, Box, or 4-7-8 — applies immediately and resets to phase 1 |
| Phase durations | Per-phase inputs (Inhale, Hold, Exhale, Hold2) clamped to 1–30 s; persisted per preset |
| Cycles per session | 1–20 cycles; updates the cycle counter and estimated duration in real time |

Dismiss by clicking outside the panel or pressing `×`.

### Info panel (`ⓘ`)

Click the info icon in the top-left corner to view keyboard shortcuts, browser API notes, and the current app version.

### Keyboard shortcuts

| Key | Action |
|---|---|
| `Space` | Start / pause session |
| `R` | Reset session |
| `F` | Toggle fullscreen |

### Landscape layout

On devices rotated horizontally:

- **Height > 500 px** — the breathing ring and history panel display side by side in a CSS Grid two-column layout.
- **Height ≤ 500 px** — compact single-column layout with reduced spacing to keep the ring and controls reachable.

Corner icons reflow into a horizontal row so they do not overlap the ring area.

---

## Breathing Modes

Three scientifically-backed patterns are built in. Phase durations are fully customizable via the settings panel.

| Mode | Pattern | Default timing |
|---|---|---|
| **Relax** | Inhale / Hold / Exhale / Hold2 | 4 s / 2 s / 8 s / 2 s |
| **Box** | Inhale / Hold / Exhale / Hold2 | 4 s / 4 s / 4 s / 4 s |
| **4-7-8** | Inhale / Hold / Exhale | 4 s / 7 s / 8 s |

Custom durations are stored in `localStorage` per preset, so each mode remembers its own configuration independently.

---

## History & Data Management

The history panel footer exposes four data management controls:

| Control | Behavior |
|---|---|
| **Export JSON** | Downloads `mb-sessions-YYYY-MM-DD.json` — all sessions, suitable for backup |
| **Export CSV** | Downloads a spreadsheet with `date`, `duration` (M:SS), `cycles`, and `preset` columns |
| **Import JSON** | Merges a previously exported file into existing history; duplicate dates are silently skipped |
| **Clear** | Opens a native `<dialog>` confirmation before deleting all sessions; ESC and backdrop click cancel safely |

Sessions interrupted after at least one complete cycle are saved to history with muted styling and an italic "Incomplete" label — partial progress is never silently discarded.

---

## Browser Compatibility

| Browser | Desktop | Mobile |
|---|---|---|
| Chrome | Supported | Supported |
| Edge | Supported | Supported |
| Firefox | Supported | Supported |
| Safari | Supported | Supported |

**API availability notes:**

| API | Fallback when unavailable |
|---|---|
| Web Audio API | Silent — no beep cues |
| Vibration API | Ignored — no haptic feedback |
| Screen Wake Lock API | Screen may sleep during sessions |
| Fullscreen API | Button does nothing |
| Service Worker | App loads from network on each visit |
| localStorage | Session defaults only; nothing persisted |

Sound requires a user gesture before the first playback (browser autoplay policy).  
Haptic feedback is available on Android/Chrome only; not supported on iOS or desktop.

---

## Development

The entire application is a single file: `index.html`. All HTML, CSS, and JavaScript live there — no modules, no build tools, no package manager. The companion files are:

| File | Purpose |
|---|---|
| `index.html` | Application — all HTML, CSS, and JS |
| `manifest.json` | PWA manifest |
| `sw.js` | Service Worker (cache-first, cache key `mb-v1`) |
| `icon.svg` | App icon |
| `LICENSE` | MIT license |

### Code organization

The `<script>` block is divided into labeled sections:

| Section | Responsibility |
|---|---|
| `CONFIG` | Frozen constants: `PRESETS`, `SOUND`, `VIBRATION`, `RING`, `COUNTDOWN`, `SESSION`, `UI` |
| `STATE` | Mutable globals: phase index, cycle count, elapsed time, `audioCtx`, wake lock handle |
| `DOM` | Element references collected once at startup |
| `HELPERS` | `hexToRgba()`, `getPhase()`, `getGoal()`, theme application |
| `DURATION INPUTS` | Per-phase duration sliders |
| `PERSISTENCE` | `saveSettings()` / `loadSettings()` / `saveHistory()` via localStorage |
| `SESSION HISTORY` | Save, load, and render past sessions (max 14 entries) |
| `WAKE LOCK` | Screen Wake Lock API wrapper |
| `SOUND/VIBRATION` | Web Audio API beeps + Vibration API haptics |
| `RENDER` | SVG ring progress, countdown text, phase label |
| `PHASE ADVANCE` | Cycles Inhale → Hold → Exhale → Hold2; fires beep/vibration cues |
| `MAIN LOOP` | `requestAnimationFrame` loop driving animation and phase transitions |
| `CONTROLS` | `start()` / `stop()` / `reset()`, 3-second countdown overlay |
| `FULLSCREEN` | Fullscreen API toggle |
| `KEYBOARD SHORTCUTS` | Space, R, F handlers |

### Conventions

- **Indentation:** 2 spaces; semicolons always; `const` by default, `let` only when reassignment is required
- **Config objects:** `Object.freeze()` — treat as immutable; never mutate at runtime
- **Error handling:** Silent `try/catch` with empty or `_`-named catch parameter; capability checks like `if (!("wakeLock" in navigator)) return`
- **Section dividers:** `/* ====== SECTION NAME ====== */` — preserve these when adding code
- **Logging:** No `console.log` in production; use browser DevTools for debugging
- **UI strings:** All user-facing text lives in the frozen `UI` object in `CONFIG` — do not hardcode strings in handlers

---

## Design Decisions

This project is a deliberate exercise in constraint-driven design:

- **Single HTML file** — the entire app ships as one file. Zero build tooling, zero CI pipeline, zero package manager.
- **No external dependencies** — every feature uses a browser-native API: Web Audio, Vibration, Screen Wake Lock, Fullscreen, `localStorage`, and Service Worker. No framework, no CDN, no network required at runtime.
- **`requestAnimationFrame` loop** — animation timing is driven by the browser's frame scheduler rather than `setInterval`, giving smooth phase transitions and accurate elapsed-time tracking regardless of CPU load.
- **Progressive enhancement for PWA** — service worker registration is wrapped in a silent `.catch()`, so `file://` access works identically. The SW only activates when served over HTTP/HTTPS.
- **`Object.freeze` config** — all presets and settings are frozen constants, making data flow easy to trace and preventing accidental mutation.

---

## Roadmap

Planned for v1.1:

- CSV import (deferred from v1.0 — RFC 4180 edge cases require a proper parser)
- Full keyboard navigation and screen reader support (WCAG compliance)
- Custom breathing preset builder — define your own name, phases, and durations
- Streak tracking — consecutive days with completed sessions

---

## Contributing

Contributions are welcome. This project has a hard constraint: **it must remain a single HTML file with no external dependencies and no build step.** Any PR that adds a package manager, bundler, or CDN-hosted library will not be accepted.

To contribute:

1. Fork the repository.
2. Make your changes in `index.html` (or the companion PWA files as appropriate).
3. Test in at least Chrome and Firefox, on both desktop and a mobile viewport.
4. Open a pull request with a clear description of what changed and why.

For substantial changes — new breathing modes, new UI flows, significant refactors — open an issue first to discuss the approach before investing time in implementation.

---

## Troubleshooting

**No sound during sessions**  
Sound requires a user interaction before the browser permits audio playback (autoplay policy). Tap or click the Start button and sound should activate. If the speaker icon is toggled off, tap it to re-enable.

**Vibration not working**  
Haptic feedback is only supported on Android devices running Chrome. It is not available on iOS, desktop browsers, or Firefox for Android. This is a browser API limitation.

**Screen keeps turning off**  
Screen Wake Lock requires a secure context (HTTPS or localhost). If you are opening `index.html` directly via `file://`, Wake Lock is unavailable. Serve the file locally with `python -m http.server 8080` and open `http://localhost:8080` instead.

**Settings or history not saving**  
The app uses `localStorage` for persistence. If localStorage is disabled (e.g., private browsing in some browsers, or storage quota exceeded), settings revert to defaults on each load and history is not retained.

**PWA install prompt not appearing**  
The Service Worker only registers when the app is served over HTTPS or localhost. On `file://`, the PWA install prompt will not appear. Use the [live demo](https://bielinskilukasz.github.io/mindful-breathing/) or a local server.

---

## Motivation

Most breathing apps are bloated — they require accounts, push notifications, subscriptions, or an internet connection just to count seconds. This project started as a personal tool: something clean, private, and always available, even on a plane with no Wi-Fi.

It also became an experiment in how far a single HTML file can go before it stops being maintainable — and whether minimalism and good UX can coexist without a framework.

---

## License

Released under the [MIT License](LICENSE).
