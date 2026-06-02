# Technology Stack

**Analysis Date:** 2026-06-03

## Languages

**Primary:**
- HTML5 - Markup, structure, and meta tags for PWA
- CSS3 - Styling, animations (keyframes), media queries for responsive design
- ECMAScript 2023 (JavaScript) - All application logic, Web APIs, browser interactions

**Secondary:**
- None - No transpilers, build tools, or secondary languages used

## Runtime

**Environment:**
- Web browser (ES2023+ support required)
- Modern browser with Web Audio API, Vibration API, Screen Wake Lock API, Fullscreen API support

**No External Runtime:**
- Vanilla JavaScript only
- No Node.js, no server-side runtime
- Single HTML file executes directly in browser

## Package Manager

**Package Management:**
- Not used - Zero external dependencies
- No `package.json`, no npm, no yarn, no pnpm
- All functionality uses browser-native APIs

## Frameworks

**Framework:**
- None - Pure vanilla HTML/CSS/JavaScript

**Build/Dev:**
- No build tools - Open `index.html` directly or serve with `python -m http.server`
- No transpilation, no bundling, no minification pipeline
- No linting or formatting tools configured

## Key Browser APIs (No External Libraries)

**Core Features:**
- **Web Audio API** (`AudioContext`, `OscillatorNode`, `GainNode`) - Sound cues with sine wave tone generation
  - Audio context created on first user interaction (autoplay policy compliance)
  - Per-phase frequency generation: 660 Hz (Inhale), 520 Hz (Hold), 330 Hz (Exhale), 440 Hz (second Hold)

- **Vibration API** (`navigator.vibrate()`) - Haptic feedback on phase transitions
  - Single 50ms pulse pattern per phase change
  - Android/Chrome only - gracefully degrades on unsupported platforms

- **Screen Wake Lock API** (`navigator.wakeLock.request()`) - Prevents display sleep during sessions
  - Acquired when session starts
  - Released when session stops
  - Re-acquired if tab becomes visible after being hidden

- **Fullscreen API** (`Element.requestFullscreen()`) - Immersive presentation mode
  - Supports both standard and webkit-prefixed variants
  - Silent fallback if not supported

- **localStorage** - Persistent settings and session history storage
  - Storage keys: `mb_v1` (settings), `mb_history` (past sessions)
  - Stores: sound toggle, vibration toggle, active preset, cycle goal, custom phase durations, session history (last 5)

- **Service Worker API** - Offline capability and PWA installation
  - File: `sw.js`
  - Cache strategy: Cache assets on install, serve from cache with network fallback
  - Gracefully degrades if Service Workers unavailable

- **requestAnimationFrame** - Smooth animation and frame-synced timing
  - Primary loop mechanism for phase progression
  - Drives SVG ring progress updates and UI re-renders

## Configuration

**PWA Manifest:**
- File: `manifest.json`
- Contains: app name, short name, description, icons, display mode, theme color
- Enables: "Add to Home Screen" on Android/iOS, standalone display mode, custom icon and splash screen

**Icons:**
- Primary: `icon.svg` (scalable SVG icon, supports maskable purpose for adaptive icons)
- Favicon: Inline data URI in `<head>` (no external file needed)

**Service Worker:**
- File: `sw.js`
- Cache version: `mb-v1`
- Assets cached: `./`, `./index.html`, `./icon.svg`, `./manifest.json`
- Fallback: Network on cache miss

## HTML Meta Tags for Mobile

```html
<meta name="viewport" content="width=device-width, initial-scale=1" />
<meta name="apple-mobile-web-app-capable" content="yes" />
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
<meta name="apple-mobile-web-app-title" content="Breathing" />
<meta name="theme-color" content="#34d399" />
```

These enable:
- Responsive viewport scaling
- Full-screen standalone mode on iOS
- Custom status bar appearance
- Custom app name on iOS home screen
- Chrome/Android theme color

## CSS Features

**Modern CSS Used:**
- CSS Custom Properties (CSS variables) - `--bg`, `--accent`, `--accentSoft`, `--text`
- CSS Gradients - Implicit in theme color system
- CSS Animations - `@keyframes` for phase fade-in, idle pulse, countdown pop
- CSS Media Queries - Breakpoints at 480px, 360px, and landscape mode (max-height: 500px)
- CSS Transitions - 220ms theme color transitions, button states
- CSS Grid - `display: grid; place-items: center` for centering
- CSS Flexbox - Layout for controls, status rows, preset buttons
- CSS Filters - `brightness()` filter tied to breathing phase progress

**No CSS Framework:**
- All CSS is hand-written in a single `<style>` block in `index.html`
- No Bootstrap, Tailwind, or Material Design
- Responsive design via media queries, not a utility library

## JavaScript Features

**ES2023 Features Used:**
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

**No Transpilation:**
- Code targets modern evergreen browsers directly
- No Babel, no TypeScript
- No polyfills needed for target browsers

## Platform Requirements

**Development:**
- Any text editor
- Any modern browser (Chrome, Edge, Firefox, Safari)
- Optional: Local HTTP server (Python, Node.js http-server, or equivalent) for HTTPS-dependent APIs

**Production (Live Demo Deployment):**
- GitHub Pages (HTTPS required for Web Audio, Wake Lock, Vibration APIs)
- URL: https://bielinskilukasz.github.io/mindful-breathing/

**Browser Support:**
- Chrome: ✅ Desktop & Mobile
- Edge: ✅ Desktop & Mobile
- Firefox: ✅ Desktop & Mobile
- Safari: ✅ Desktop & Mobile

**Secure Context Requirements:**
- Web Audio API, Vibration API, Screen Wake Lock API, Fullscreen API all require HTTPS or localhost
- Service Worker registration also requires secure context
- Opening `file://` locally disables these APIs but app still functions (basic breathing timer only)

## File Structure (Single File Architecture)

**Primary Artifact:**
- `index.html` (~1,350 lines) - Contains entire application: HTML structure + CSS styling + JavaScript logic

**Supporting Files:**
- `manifest.json` - PWA metadata
- `sw.js` - Service Worker for caching
- `icon.svg` - PWA icon
- `README.md` - Documentation

**Total Code Lines:**
- `index.html`: ~1,350 lines (HTML ~200, CSS ~670, JavaScript ~1,040)
- `sw.js`: 11 lines
- `manifest.json`: 18 lines
- **Total application code: ~1,370 lines (JavaScript dominant)**

## Key Architectural Constraint

**Single-File Monolith:**
- All HTML, CSS, JavaScript in one file
- No module system, no imports/exports
- All variables global scope (intentional for single-file simplicity)
- No separate components or build artifacts
- Freezes `Object` constants at top to simulate immutability

---

*Stack analysis: 2026-06-03*
