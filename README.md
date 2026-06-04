# Mindful Breathing

![Status](https://img.shields.io/badge/status-active_development-brightgreen)
![Version](https://img.shields.io/badge/version-0.4.0-blue)
![HTML5](https://img.shields.io/badge/HTML-5-E34F26?logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS-3-1572B6?logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/ECMAScript-2023-F7DF1E?logo=javascript&logoColor=black)
![License](https://img.shields.io/badge/License-MIT-yellow)

A distraction-free breathing timer for relaxation, focus, and mindfulness practice.  
No backend. No dependencies. No installation. Works offline.

> **Actively developed** — new features and UI improvements are being added regularly.

**[Live demo →](https://bielinskilukasz.github.io/mindful-breathing/)**

---

<img width="639" height="805" alt="Mindful Breathing app screenshot" src="https://github.com/user-attachments/assets/e492c052-1ad8-44f7-a865-865139bd0c2d" />

---

## Features

- **Three scientifically-backed breathing modes** — Relax (4-2-8-2), Box (4-4-4-4), and 4-7-8
- **Smooth animated ring** driven by `requestAnimationFrame`, synchronized with each phase
- **Real-time session progress bar** — fills continuously across cycles, not just at boundaries
- **Ambient brightness animation** — the whole UI breathes with you
- **Visible mode indicator** — a small uppercase label above the cycle counter (e.g. `RELAX`, `BOX`, `4-7-8`) always shows which preset is active; updates live when the mode is changed in settings
- **Settings panel** — a proper cogwheel `⚙` icon in the top-left corner opens a modal with all session customization in one place
- **Breathing mode selection inside settings** — pick Relax, Box, or 4-7-8 from the settings panel; changes apply immediately
- **Editable phase durations inside settings** — set Inhale, Hold, Exhale (and second Hold where applicable) in seconds, clamped to 1–30 sec
- **Editable cycle count inside settings** — choose 1–20 cycles per session; the displayed cycle counter and estimated session duration update live
- **Inspirational quote** shown on the 3-2-1 countdown overlay before each session
- **Sound cues** via Web Audio API — speaker icon toggle, per-phase sine tones
- **Haptic feedback** via Vibration API — vibration icon toggle, supported on Android/Chrome
- **Screen Wake Lock** — prevents display sleep during a session
- **Session history** with last 5 sessions and a one-tap Clear button
- **Dedicated info panel** — the `ⓘ` icon now opens a modal with keyboard shortcuts and platform notes (replacing the previous inline collapsible block)
- **Keyboard shortcuts** — `Space` start/stop · `R` reset · `F` fullscreen
- **Fullscreen mode** for an immersive, distraction-free experience
- **Installable PWA** — works offline and can be added to home screen on mobile
- **Landscape-aware layout** — main UI re-centers, corner icons reflow into a row, and Start/Reset buttons stay centered when the device is rotated

---

## Settings Panel

All session customization lives behind the `⚙` icon in the top-left corner. Open it to configure:

- **Breathing mode** — Relax (4-2-8-2), Box (4-4-4-4), or 4-7-8. Switching modes immediately updates the breathing cycle and resets to the start of phase 1.
- **Phase durations** — per-phase numeric inputs (Inhale, Hold, Exhale, and second Hold for Relax/Box), each clamped to 1–30 seconds. Values are persisted to `localStorage` per preset, so each mode keeps its own customizations.
- **Cycles per session** — a numeric input from 1 to 20. Changes update the displayed cycle counter (`Cycle: X / Y`) and the estimated total session duration in real time.

The panel is a lightweight modal — click outside it or the `×` button to dismiss.

---

## Info Panel

The `ⓘ` icon in the top-left corner column now opens a dedicated modal (matching the settings panel's style) instead of toggling an inline block under the controls. The panel surfaces two groups:

- **Keyboard shortcuts** — `Space` (start / stop), `R` (reset), `F` (fullscreen), shown as `<kbd>`-style chips.
- **Notes** — sound-autoplay caveat (browser autoplay policy) and the Android-/Chrome-only nature of vibration.

Dismiss the same way as the settings panel: the `×` button or a click outside the panel.

---

## Landscape Layout

When the device is rotated horizontally (`max-height: 500px` and `orientation: landscape`), the layout adapts so nothing crowds or overlaps:

- **Centered main UI** — the title row, mode indicator, cycle counter, phase label, timer ring, and status text all stay horizontally centered.
- **Corner icons reflow into a row** — the (i), gear, and sound/vibration toggles sit horizontally across the top of the card so they no longer stack down into the ring area. All four icons (plus the fullscreen toggle in the top-right) are normalised to the same 32×32 size with consistent spacing, so nothing touches or overlaps.
- **Centered controls** — Start / Reset buttons, status row, goal row, and session bar are explicitly centered in landscape, matching portrait alignment.
- **Consistent vertical rhythm** — top padding reserves room for the icon row, and the surrounding elements share a uniform 8 px rhythm so spacing reads the same way in either orientation.

---

## Motivation

Most breathing apps are bloated — they require accounts, push notifications, subscriptions, or an internet connection just to count seconds. This project started as a personal tool: something clean, private, and always available, even on a plane with no Wi-Fi.

It also became an experiment in how far you can push a single HTML file before it stops being maintainable — and whether minimalism and good UX can coexist without a framework.

---

## What's Next

- Expand session history beyond 5 entries with scroll
- Custom breathing preset builder (name, phases, durations)
- Streak tracking — consecutive days with completed sessions
- Ambient background sound option (white noise, rain)

---

## Design Decisions

This project is a deliberate exercise in **constraint-driven design**:

- **Single HTML file** — the entire app (HTML, CSS, JS) ships as one file. Zero build tooling, zero CI pipeline, zero package manager. The only companion files are the PWA manifest, icon, and service worker.
- **No external dependencies** — every feature uses a browser-native API: Web Audio API (sound cues), Vibration API (haptics), Screen Wake Lock API, Fullscreen API, `localStorage` (persistence), and the Service Worker API (offline/installability). No framework, no CDN, no network required.
- **`requestAnimationFrame` loop** — animation timing is driven by the browser's frame scheduler rather than `setInterval`, giving smooth phase transitions and accurate elapsed-time tracking regardless of CPU load.
- **Progressive enhancement for PWA** — the service worker registration is wrapped in a silent `.catch()`, so opening `index.html` directly via `file://` works identically to before. The SW only activates when served over HTTP/HTTPS (e.g. GitHub Pages).
- **`Object.freeze` config** — all presets and settings are frozen constants, making data flow easy to trace and preventing accidental mutation.

---

## Getting Started

```bash
git clone https://github.com/BielinskiLukasz/mindful-breathing.git
cd mindful-breathing
open index.html   # or double-click in your file explorer
```

No build steps. No `npm install`.

---

## Browser Compatibility

| Browser | Desktop | Mobile |
|---------|---------|--------|
| Chrome  | ✅      | ✅     |
| Edge    | ✅      | ✅     |
| Firefox | ✅      | ✅     |
| Safari  | ✅      | ✅     |

Sound requires a user gesture to activate (browser autoplay policy).  
Haptic feedback is available on Android/Chrome; not available on iOS or desktop.

---

## License

Released under the [MIT License](LICENSE).
