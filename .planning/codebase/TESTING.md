# Testing Patterns

**Analysis Date:** 2026-06-03

## Test Framework

**Runner:**
- Not applicable — no test framework configured

**Assertion Library:**
- Not applicable — no testing library used

**Run Commands:**
- No automated test commands available
- Manual testing in browser: open `index.html` or serve via `python -m http.server 8080`

## Test File Organization

**Location:**
- No test files present in repository
- Single application file: `index.html`

**Naming:**
- Not applicable — no test files

**Structure:**
- Not applicable — no test files

## Test Structure

**Suite Organization:**
- Not applicable — no test framework

**Patterns:**
- Manual testing only
- Feature testing done via browser UI interaction
- Browser DevTools for inspection (Console, Network, Application tabs)

## Mocking

**Framework:**
- Not applicable — no testing library

**Patterns:**
- No mocking framework
- To test sound APIs: disable sound toggle (`soundEnabled` global) or don't call `playBeep()`
- To test vibration APIs: disable vibration toggle (`vibeEnabled` global) or device without Vibration API support
- To test Web Audio: audioCtx is only created on first Start click after user gesture (line 1765–1768)

**What to Mock (if implementing tests):**
- Browser APIs: AudioContext, navigator.vibrate, navigator.wakeLock, Fullscreen API
- localStorage: mock `localStorage.getItem()` and `localStorage.setItem()` for STORAGE_KEY and HISTORY_KEY
- requestAnimationFrame: mock for testing animation loop logic (`loop()` function)
- Date: mock `new Date()` and `Date.now()` for session timestamp testing

**What NOT to Mock (if implementing tests):**
- Phase state transitions: test with real state objects (PRESETS config)
- Duration calculations: test with real durationSec values from activePhases
- DOM manipulation: use real DOM (or JSDOM) to verify class toggles, text content updates
- Event handlers: integrate with click/change events to verify full flow

## Fixtures and Factories

**Test Data:**
- Phase presets defined in PRESETS config (lines 1057–1075): `relax`, `box`, `478`
- Each preset is an array of phase objects with: `{ name, durationSec, breathR, theme, cue, hint }`
- Test data would typically clone a preset: `activePhases = PRESETS.relax.map(p => ({...p}))`
- Mock session data for history: `{ date: "ISO string", durationMs: 300000, cycles: 5 }`

**Location:**
- If test suite added: conventionally under `test/` or `__tests__/` directory (not currently present)
- Fixtures would be in separate file or inline in test file

## Coverage

**Requirements:**
- No coverage requirements or enforcement
- No CI/CD pipeline

**View Coverage:**
- Not applicable — no coverage tooling

## Test Types

**Unit Tests:**
- Not implemented
- Would test: `hexToRgba()`, `formatDuration()`, `getPhase()`, `getGoal()`, `getElapsedDisplay()`
- Scope: Pure functions that don't depend on DOM or global state

**Integration Tests:**
- Not implemented
- Would test: state transitions during start/stop/reset cycle, preset switching, localStorage persistence
- Scope: Multiple functions working together with real state mutations

**E2E Tests:**
- Not implemented
- Framework: Could use Playwright, Cypress, or WebDriver
- Would test: full user flows (start session → run cycles → finish → restart), keyboard shortcuts, fullscreen toggle, settings panel interaction

## Common Patterns

**Async Testing (if implementing tests):**
```javascript
// For testing requestAnimationFrame loop
async function runLoop() {
  return new Promise(resolve => {
    let frameCount = 0;
    const testLoop = (nowTs) => {
      frameCount++;
      if (frameCount >= 3) { resolve(); return; }
      requestAnimationFrame(testLoop);
    };
    requestAnimationFrame(testLoop);
  });
}

// For testing countdown overlay
async function waitForCountdown() {
  return new Promise(resolve => {
    setTimeout(resolve, 3000); // matches showCountdown behavior
  });
}
```

**Error Testing (if implementing tests):**
```javascript
// Test graceful degradation when APIs missing
test('plays sound only if audioCtx exists', () => {
  const originalCtx = audioCtx;
  audioCtx = null;
  playBeep(660); // should silently return
  audioCtx = originalCtx;
  expect(audioCtx).toBe(originalCtx); // unchanged
});

// Test localStorage fallback
test('loads settings gracefully if localStorage unavailable', () => {
  const saveItem = localStorage.setItem;
  localStorage.setItem = () => { throw new Error('quota exceeded'); };
  saveSettings(); // should catch error and continue
  localStorage.setItem = saveItem;
});

// Test browser API absence
test('skips wake lock if not supported', () => {
  const wasSupported = 'wakeLock' in navigator;
  delete navigator.wakeLock;
  acquireWakeLock(); // should return early
  if (wasSupported) navigator.wakeLock = navigator.wakeLock;
});
```

## Architecture for Testing

**Key Testable Functions:**
1. **State Management:**
   - `advanceToNextPhase(nowTs)` — cycle advancement and completion logic
   - `reset()` — state reset to initial
   - `finishSession()` — session completion and history save

2. **Calculations:**
   - `formatDuration(ms)` — time display formatting
   - `getElapsedDisplay()` — elapsed time formatting
   - `getGoal()` — goal clamping and validation
   - `updateTimeEst()` — total session duration estimation

3. **Rendering:**
   - `render(progress01, secondsLeftInt)` — all DOM updates in response to progress
   - `renderSessionBar()` — progress bar fill calculation
   - `renderHistory()` — history list formatting and rendering

4. **Settings & Persistence:**
   - `saveSettings()` — localStorage serialization
   - `loadSettings()` — localStorage deserialization and apply
   - `saveHistory(entry)` — history list append
   - `buildDurationInputs()` — preset duration UI generation

5. **Audio/Haptics:**
   - `playBeep(freqHz, ms, volume)` — Web Audio API orchestration
   - `vibrate(pattern)` — Vibration API call

6. **Event Handlers:**
   - `showCountdown(callback)` — countdown overlay and timer management
   - `start()`, `stop()`, `reset()` — session state transitions
   - Preset selector listener
   - Duration input listener

## Testing Strategy Recommendations

**If implementing tests:**
1. Use Vitest or Jest for unit tests (no DOM)
2. Use jsdom or happy-dom for DOM interaction tests
3. Mock requestAnimationFrame for animation loop tests
4. Create fixtures for PRESETS data
5. Test state mutations and rendering separately
6. Test browser API graceful degradation with feature detection mocks
7. E2E tests with Playwright for real browser behavior (audioContext autoplay policy, fullscreen, etc.)

---

*Testing analysis: 2026-06-03*
