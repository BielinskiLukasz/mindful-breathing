# External Integrations

**Analysis Date:** 2026-06-03

## APIs & External Services

**No External APIs:**
- Zero third-party API integrations
- No backend service calls
- No cloud platform SDKs
- No analytics or telemetry services
- No authentication services

**Application relies entirely on browser-native APIs:**
- All functionality is self-contained within the browser
- No network requests except Service Worker `fetch` fallback (which serves cached assets)

## Data Storage

**Databases:**
- None - No database servers or cloud databases

**Browser Storage:**
- `localStorage` (client-side only, persistent across sessions)
  - **Storage Keys:**
    - `mb_v1`: Settings persistence (sound toggle, vibration toggle, active preset, cycle goal, custom phase durations)
    - `mb_history`: Session history (last 5 completed sessions with timestamp, duration, cycle count)
  - **Capacity:** 5-10 MB depending on browser (well within limits for this app)
  - **Persistence:** Persists until manually cleared via browser storage settings or app "Clear History" button
  - **No Sync:** Stored locally only, not synced to other devices or cloud

**File Storage:**
- Local filesystem only via PWA installation
- No cloud file storage (Google Drive, Dropbox, etc.)
- Icons and manifest served as static files

**Caching:**
- Service Worker cache via Cache API (`caches.open()`)
  - Cache name: `mb-v1`
  - Cached assets: `./`, `./index.html`, `./icon.svg`, `./manifest.json`
  - Strategy: Network-first for fetch events, falls back to cache
  - No remote cache invalidation (manual cache busting required via version update)

**Session Data:**
- Ephemeral in-memory state during active session
- Variables stored in global scope (STATE section in `index.html`)
- Cleared on page reload or browser tab close

## Authentication & Identity

**Auth Provider:**
- None - No authentication system
- No user accounts required
- No login/signup flows
- No OAuth or API key management
- Fully anonymous and private

**Privacy:**
- All data stays on the user's device
- No server-side storage of user data
- No tracking or analytics
- No third-party cookies
- Suitable for offline-only operation

## Monitoring & Observability

**Error Tracking:**
- None - No error reporting service
- Errors handled silently with `.catch()` / `try/catch` blocks
- No error logs sent anywhere

**Logging:**
- Console-only (no external log collection)
- Debug information available in browser DevTools
- No structured logging framework

**Analytics:**
- None - No usage analytics
- No events sent to analytics services
- No performance monitoring dashboards

## CI/CD & Deployment

**Hosting:**
- GitHub Pages (static file hosting)
- URL: https://bielinskilukasz.github.io/mindful-breathing/
- Branch: `main` (auto-deployed via GitHub Pages)
- HTTPS: Enabled by default (required for Web APIs)

**Repository:**
- GitHub: https://github.com/BielinskiLukasz/mindful-breathing
- Public repository with MIT license
- No private API keys in version control (no secrets needed)

**CI Pipeline:**
- None configured - No GitHub Actions or CI system
- Manual deployment via git push to `main` branch
- No automated testing pipeline

**Build Process:**
- None - Static files deployed as-is
- No compilation, bundling, or minification step
- `index.html`, `manifest.json`, `sw.js`, `icon.svg` deployed directly

## Environment Configuration

**Required Environment Variables:**
- None - Zero environment variables used
- No `.env` file
- All configuration hard-coded as `Object.freeze()` constants in `index.html`

**Configuration Inside Code:**

```javascript
// From CONFIG section in index.html (lines 1042-1129)

const PRESETS = {
  relax: [4s Inhale, 2s Hold, 8s Exhale, 2s Hold],
  box: [4s Inhale, 4s Hold, 4s Exhale, 4s Hold],
  "478": [4s Inhale, 7s Hold, 8s Exhale]
};

const SOUND = {
  enabledByDefault: true,
  waveform: "sine",
  volume: 0.12,
  beepMs: 120
};

const VIBRATION = {
  enabledByDefault: true,
  pattern: [50]
};

const SESSION = {
  defaultCycles: 5,
  maxCycles: 20
};

const STORAGE_KEY = "mb_v1";
const HISTORY_KEY = "mb_history";
```

**Secrets/Credentials:**
- None - No API keys, auth tokens, or private credentials
- No `.env.local` or secrets management needed
- Safe to commit all files to public repository

## Webhooks & Callbacks

**Incoming Webhooks:**
- None - No server endpoint to receive webhooks

**Outgoing Webhooks:**
- None - No HTTP POST requests to external services
- No callbacks to third-party systems

**Event-Driven Integration:**
- Purely internal event handling via DOM events and function callbacks
- Example: `addEventListener("click", ...)` for button interactions
- Example: `requestAnimationFrame(loop)` for animation timing
- No pub/sub, message queue, or event bus integration

## Browser APIs (No Dependencies, These Are Built-In)

**Web Audio API:**
- Isolated to app scope via `AudioContext` instance
- No external audio files or streams
- Synthesis-only: generates tones procedurally
- No licensing or third-party audio content

**Vibration API:**
- Standard W3C API available on Android/Chrome
- No external vibration service
- Gracefully degrades on unsupported browsers

**Screen Wake Lock API:**
- Standard W3C API for preventing display sleep
- No external service
- User grants permission on first request

**Fullscreen API:**
- Standard W3C API for presentation mode
- No external service
- User grants permission on request

**Service Worker API:**
- Caches assets for offline use via Cache API
- No external cache service
- Works entirely client-side

## No Network Requests

**Network Activity:**
- Zero API calls to external services
- No image, font, or stylesheet CDN requests
- No analytics pixels or tracking beacons
- Only network activity: Service Worker `fetch` event (cached response)

**When Offline:**
- App functions normally from cached assets
- All features work without internet (except PWA installation check)
- Settings and history remain available from `localStorage`

## Dependencies Summary

**External Dependencies:**
- **Count:** 0 (zero)
- **Package Manager:** Not used
- **Vulnerabilities:** No third-party packages = no supply chain risk
- **Size:** Single HTML file (~64 KB uncompressed, ~10 KB gzipped with CSS/JS inline)

**All Features Implemented Using:**
- Vanilla JavaScript (ES2023)
- Browser-native Web APIs
- Standard CSS3
- Standard HTML5

---

*Integration audit: 2026-06-03*
