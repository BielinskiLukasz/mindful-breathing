# Mindful Breathing

![Status](https://img.shields.io/badge/status-active_development-brightgreen)
![Version](https://img.shields.io/badge/version-0.3.0-blue)
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
- **Configurable durations** — edit phase lengths per preset on the fly
- **Inspirational quote** shown on the 3-2-1 countdown overlay before each session
- **Sound cues** via Web Audio API — speaker icon toggle, per-phase sine tones
- **Haptic feedback** via Vibration API on supported Android/Chrome devices
- **Screen Wake Lock** — prevents display sleep during a session
- **Session history** with last 5 sessions and a one-tap Clear button
- **Collapsible info panel** — keyboard shortcuts and notes behind an `ⓘ` button
- **Keyboard shortcuts** — `Space` start/stop · `R` reset · `F` fullscreen
- **Fullscreen mode** for an immersive, distraction-free experience
- **Installable PWA** — works offline and can be added to home screen on mobile

---

## Motivation

Most breathing apps are bloated — they require accounts, push notifications, subscriptions, or an internet connection just to count seconds. This project started as a personal tool: something clean, private, and always available, even on a plane with no Wi-Fi.

It also became an experiment in how far you can push a single HTML file before it stops being maintainable — and whether minimalism and good UX can coexist without a framework.

---

## What's Next

- Landscape layout refinements and edge-case polish
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
