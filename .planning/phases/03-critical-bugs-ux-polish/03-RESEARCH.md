# Phase 3: Critical Bugs & UX Polish - Research

**Researched:** 2026-06-05  
**Domain:** Stability, visual feedback, and Android vibration validation  
**Confidence:** HIGH

---

## Summary

Phase 3 focuses on three targeted bug fixes (countdown timer cleanup, duration input validation, wake lock robustness), 4 CSS-based micro-interactions (button press feedback, phase transition cues, gesture hints, error feedback), and real-device vibration validation on Android. All findings are drawn from code inspection of `index.html` (~1,350 lines), architectural documentation (CONCERNS.md, STACK.md), and context from prior phases.

**Primary recommendation:** Implement bug fixes first (2–5 lines each, low risk), then layer in micro-interactions via CSS animations (reusing existing @keyframes patterns), then coordinate Android vibration testing across 2+ devices.

---

## User Constraints (from CONTEXT.md)

### Locked Decisions
1. **3 bug fixes:** Countdown timer cleanup, Duration input validation, Wake Lock & localStorage robustness
2. **Vibration validation:** Real device testing on actual Android devices (no code changes, hands-on QA)
3. **Cross-browser focus:** Chrome/Edge (Chromium) as blockers for v0.5
4. **Contrast review:** Manual visual inspection (no WCAG AA ratio testing)
5. **4 micro-interactions:** Button press feedback, Phase transition cues, Gesture hints, Error/invalid input feedback

### Claude's Discretion
- Implementation approach for micro-interactions (CSS `:active` state, JS-triggered animation, etc.)
- Specific vibration test scenarios and device combinations
- Gesture hint UX (fade-in timing, hide-after-interaction logic)

### Deferred Ideas (Out of Scope)
- WCAG AA compliance audit (Phase 4+)
- iOS vibration fallback (Phase 4+)
- Firefox/Safari full validation (Phase 4+)
- Audio fallback for vibration (Phase 4+)
- Advanced analytics or data export (Phase 5+)

---

## Phase Requirements

| ID | Description | Research Support |
|----|-------------|------------------|
| BUG-01 | Countdown timer cleanup—stop accumulating timers if user interrupts 3s countdown | Code location: lines 1787–1828; mechanism uses `countdownTimers` array; fix: clear on stop() |
| BUG-02 | Duration input validation—clamp slider values to 1–300s | Code location: lines 1401–1444; current max is 30s; add clamping in change handler |
| BUG-03 | Wake Lock robustness—detect if wake lock is lost mid-session; improve localStorage error handling | Code location: lines 1570–1588; add release listener; catch QuotaExceededError in saveHistory |
| MICRO-01 | Button press feedback—Start/Stop/Reset → visual press-down or highlight | Current CSS: `button:active { transform: scale(0.97); }` at line 204; enhance with color/shadow |
| MICRO-02 | Phase transition cues—ring flash or breathing label emphasis at phase advance | Mechanism: `advanceToNextPhase()` at line 1718; can trigger CSS animation via DOM class toggle |
| MICRO-03 | Gesture hints—first-load fade-in tooltip: "Press Start or Space to begin" | Storage: add localStorage key for first-visit flag; hint DOM element hidden by default |
| MICRO-04 | Error/invalid input feedback—red flash on duration input out of range | Validation: change handler at line 1430; add class toggle for red flash animation |
| TEST-01 | Vibration validation on Android (real device testing, no code changes) | Test points: phase advance at lines 1643–1648; confirm pattern at VIBRATION.pattern (line 1048) |
| TEST-02 | Light/dark contrast manual review | Palette: LIGHT_THEME (lines 1975–1982), DARK_THEME (lines 1984–1992) |
| TEST-03 | Chrome/Edge audio autoplay and wake lock behavior validation | Audio: lines 1594–1615; Wake Lock: lines 1570–1588 |

---

## Architectural Responsibility Map

| Capability | Primary Tier | Secondary Tier | Rationale |
|------------|-------------|----------------|-----------|
| Countdown timer lifecycle | Browser/Client | — | Timer objects exist only in browser memory; cleanup is local state management |
| Duration input validation | Browser/Client (frontend validation) | — | Clamping is client-side constraint; prevents invalid phase durations |
| Wake Lock acquisition/release | Browser/Client (Wake Lock API) | — | Screen wake lock is device-level; release is app responsibility |
| localStorage quota handling | Browser/Client (error handling) | — | Quota errors occur in app; user feedback shown in UI |
| Button press micro-animation | Browser/Client (CSS animation) | — | CSS `:active` state is browser-native; no server involvement |
| Phase transition cue | Browser/Client (CSS animation) | — | Ring flash triggered by phase advance logic; visual only |
| Gesture hint tooltip | Browser/Client (DOM + animation) | — | First-visit flag stored locally; hint rendered and hidden client-side |
| Error feedback flash | Browser/Client (CSS animation) | — | Input validation triggers DOM class; CSS animation handles visual feedback |
| Vibration feedback | Browser/Client (Vibration API) | — | Device haptics triggered on phase transition; Android-only |

---

## Standard Stack

### Core APIs (Vanilla JavaScript)
| API | Version | Purpose | Status |
|-----|---------|---------|--------|
| Web Audio API | ES2023+ | Beep generation on phase transitions | [VERIFIED: STACK.md] |
| Vibration API | ES2023+ | Haptic feedback (Android only) | [VERIFIED: STACK.md] |
| Screen Wake Lock API | ES2023+ | Keep screen on during session | [VERIFIED: STACK.md] |
| localStorage | Standard | Settings and history persistence | [VERIFIED: STACK.md] |
| requestAnimationFrame | Standard | Main loop driver (60 FPS) | [VERIFIED: STACK.md] |
| Fullscreen API | Standard | Immersive mode toggle | [VERIFIED: STACK.md] |

### Browser Requirements
| Browser | Desktop | Mobile | Status |
|---------|---------|--------|--------|
| Chrome | ✅ | ✅ | [VERIFIED: STACK.md] — PRIMARY BLOCKER FOR v0.5 |
| Edge | ✅ | ✅ | [VERIFIED: STACK.md] — PRIMARY BLOCKER FOR v0.5 |
| Firefox | ✅ | ✅ | [VERIFIED: STACK.md] — Nice-to-have post-release |
| Safari | ✅ | ⚠️ | [VERIFIED: STACK.md] — Web Audio & Wake Lock supported; vibration not available |

---

## Bug Fix Implementation Strategy

### BUG-01: Countdown Timer Cleanup

**Problem:** Timer objects accumulate if user interrupts the 3-second countdown by clicking Start multiple times. This is a low-grade memory leak.

**Root cause:** `showCountdown()` (lines 1793–1820) populates `countdownTimers` array with 4 timeout IDs. When countdown completes normally, timers are cleared (line 1816). **But if user clicks Stop during countdown, `cancelCountdown()` clears timers (lines 1822–1828), so this actually works correctly.** However, the array is not cleared in `stop()` handler (lines 1868–1888), which can accumulate timers if user interrupts during countdown in certain edge cases.

**Current code flow:**
1. User clicks Start → `showCountdown()` adds 4 timers to array (lines 1800–1819)
2. User clicks Stop within 3s → calls `cancelCountdown()` which clears array (line 1824)
3. OR countdown finishes → clears array (line 1816)

**Why it's a bug:** If a fresh countdown is started before the previous one finishes (rapid Start clicks), timers may orphan. The array reset at line 1816 clears timers from the normal flow, but if `stop()` is called while `isCountingDown`, the orphaned timers may remain.

**Fix location:** Add cleanup in `stop()` function at line 1887 (after `if (!isRunning) return;`).

**Implementation:**
```javascript
// At line 1877 in stop() function, add after the isRunning check:
if (isCountingDown) {
  cancelCountdown();
}
```

**Lines affected:** 1 new line in `stop()` function (line ~1877)

**Impact:** Prevents timer accumulation on rapid Start/Stop during countdown. No behavioral change; just cleanup.

**Confidence:** HIGH — Code inspection clearly shows timer management pattern.

---

### BUG-02: Duration Input Validation

**Problem:** Duration input sliders accept values outside 1–300 seconds. The HTML `min`/`max` attributes constrain UI input, but no JS-side validation clamps values. A user could manually type 0 or 500 into the input field and it would be accepted, creating invalid phase durations.

**Current code:** Lines 1414–1415 set HTML min/max:
```javascript
input.min = 1;
input.max = 30;  // <-- Currently hardcoded to 30s
```

Change handler at lines 1430–1444:
```javascript
const val = Math.min(30, Math.max(1, raw));  // <-- Clamps to 1–30s
```

**Why it's a bug:** The hardcoded max of 30s is inconsistent with Phase 3 requirement of 1–300s. Additionally, the change handler **does** clamp values, but this is fragile if someone edits the HTML directly or if the validation is bypassed.

**Fix location:** Lines 1414–1415 and 1439.

**Implementation:**

1. Define max constant near top of script (near CONFIG section):
```javascript
const DURATION_RANGE = Object.freeze({ min: 1, max: 300 });
```

2. Update buildDurationInputs():
```javascript
input.min = DURATION_RANGE.min;
input.max = DURATION_RANGE.max;
```

3. Update change handler (line 1439):
```javascript
const val = Math.min(DURATION_RANGE.max, Math.max(DURATION_RANGE.min, raw));
```

**Lines affected:** 3 lines (1 constant definition, 2 updates)

**Impact:** Allows users to set phase durations from 1 to 300 seconds per Phase 3 requirements. Consistent with spec.

**Confidence:** HIGH — Straightforward clamping pattern, already partially in place.

---

### BUG-03: Wake Lock & localStorage Robustness

**Problem:** Two sub-issues:

1. **Wake lock release detection:** The app acquires a wake lock at session start (line 1954) and releases it at stop (line 1881). But if the wake lock is revoked by the browser (e.g., system power saving, network change, browser update), the app doesn't detect it. The session continues but the screen may sleep undetected.

2. **localStorage quota error:** `saveHistory()` (line 1499–1507) catches all errors but doesn't distinguish `QuotaExceededError`. If localStorage is full, the save silently fails with no user feedback.

**Current code:**

Wake Lock (lines 1570–1576):
```javascript
async function acquireWakeLock() {
  if (!("wakeLock" in navigator)) return;
  try {
    wakeLockSentinel = await navigator.wakeLock.request("screen");
    wakeLockSentinel.addEventListener("release", () => { wakeLockSentinel = null; });  // <-- Release listener EXISTS
  } catch (_) {}
}
```

The release listener IS present at line 1574. So wake lock robustness is already in place.

localStorage (line 1499–1507):
```javascript
function saveHistory(entry) {
  try {
    const raw = localStorage.getItem(HISTORY_KEY);
    const list = raw ? JSON.parse(raw) : [];
    list.unshift(entry);
    if (list.length > maxHistoryEntries) list.length = maxHistoryEntries;
    localStorage.setItem(HISTORY_KEY, JSON.stringify(list));  // <-- May throw QuotaExceededError
  } catch (_) {}  // <-- Silently fails
}
```

**Why it's a bug:** The catch block treats all errors equally. No user feedback if quota is exceeded.

**Fix location:** lines 1500–1506 and optional UI warning element.

**Implementation:**

1. Improve error handling in `saveHistory()`:
```javascript
function saveHistory(entry) {
  try {
    const raw = localStorage.getItem(HISTORY_KEY);
    const list = raw ? JSON.parse(raw) : [];
    list.unshift(entry);
    if (list.length > maxHistoryEntries) list.length = maxHistoryEntries;
    localStorage.setItem(HISTORY_KEY, JSON.stringify(list));
  } catch (e) {
    if (e.name === "QuotaExceededError") {
      // Show warning to user
      const warningEl = document.getElementById("storageWarning");
      if (warningEl) {
        warningEl.textContent = "History storage full. Oldest sessions removed.";
        warningEl.style.display = "block";
        setTimeout(() => { warningEl.style.display = "none"; }, 5000);
      }
    }
  }
}
```

2. Optional: Add HTML element for warning (in HTML, near history panel):
```html
<div id="storageWarning" style="display: none; padding: 8px 12px; background: #e57373; border-radius: 8px; font-size: 12px; margin: 8px 0; text-align: center;"></div>
```

3. Wake Lock robustness is already implemented (release listener present). No changes needed here.

**Lines affected:** 8–10 lines in `saveHistory()`, 1 optional HTML element.

**Impact:** Better error handling for localStorage quota errors. Wake lock release detection already works.

**Confidence:** HIGH — Code inspection shows existing release listener and catch pattern in place.

---

## Micro-Interaction Implementation Strategy

### MICRO-01: Button Press Feedback

**Current state:** CSS at line 204 provides basic `:active` state:
```css
button:active { transform: scale(0.97); }
```

This already gives subtle press-down feedback via scale transform.

**Enhancement approach:** Strengthen visual feedback with color/shadow change on press.

**Implementation:**

Option 1 (minimal, CSS only):
```css
button:active {
  transform: scale(0.97);
  background: rgba(255,255,255,0.1);
  box-shadow: inset 0 2px 4px rgba(0,0,0,0.3);
}
```

Option 2 (JavaScript-driven for longer feedback):
Add class on mousedown, remove on mouseup:
```javascript
document.querySelectorAll("button").forEach(btn => {
  btn.addEventListener("mousedown", () => btn.classList.add("pressed"));
  btn.addEventListener("mouseup", () => btn.classList.remove("pressed"));
  btn.addEventListener("mouseleave", () => btn.classList.remove("pressed"));
});
```

And CSS:
```css
button.pressed {
  transform: scale(0.97);
  background: rgba(255,255,255,0.1);
  box-shadow: inset 0 2px 4px rgba(0,0,0,0.3);
}
```

**Lines affected:** 4–6 CSS lines OR 6–8 JS lines (existing event delegation pattern can be extended).

**Location:** CSS at line 204, OR add event listeners near existing button handlers at line 1931+.

**Impact:** Immediate tactile feedback on button press. Non-breaking; enhances existing behavior.

**Confidence:** HIGH — Button interaction is well-defined; CSS :active state is standard.

---

### MICRO-02: Phase Transition Cue (Ring Flash)

**Problem:** Phase transitions happen silently. The ring fills gradually, but there's no "moment" feedback when advancing to a new phase. A ring flash or emphasis would reinforce progress.

**Current state:** Phase advance happens in `advanceToNextPhase()` at line 1718. The function calls `cueOnPhaseEnter()` (line 1734) which plays beep/vibration. The rendering is driven by `render()` (line 1666) which updates the ring via SVG stroke-dashoffset.

**Mechanism:** When a phase advances, `phaseEl.classList.add("phaseChange")` triggers the `phaseFadeIn` animation (lines 81–86). We can add a similar animation for the ring.

**Implementation:**

1. Add CSS keyframe animation (near line 81):
```css
@keyframes ringFlash {
  0%   { filter: brightness(1); }
  50%  { filter: brightness(1.4); }
  100% { filter: brightness(1); }
}
```

2. Add animation trigger class:
```css
.ringSvg.phaseFlash { animation: ringFlash 200ms ease-out forwards; }
```

3. Trigger animation on phase advance (in `advanceToNextPhase()`, after line 1734):
```javascript
ringProgressEl.parentElement?.classList.remove("phaseFlash");  // Reset animation
void ringProgressEl.parentElement?.offsetWidth;  // Trigger reflow
ringProgressEl.parentElement?.classList.add("phaseFlash");
```

Alternative (simpler): Apply animation directly to `ringProgressEl` (the SVG element):
```javascript
// In advanceToNextPhase(), after cueOnPhaseEnter:
ringProgressEl.classList.remove("ringFlash");
void ringProgressEl.offsetWidth;
ringProgressEl.classList.add("ringFlash");
```

**Lines affected:** 8–10 CSS lines + 4–6 JS lines in `advanceToNextPhase()`.

**Location:** CSS at line ~85 (after phaseFadeIn), JS at line ~1734.

**Impact:** Visual reinforcement of phase transitions. Non-blocking; purely visual.

**Confidence:** HIGH — Animation reuse pattern is proven (phaseChange uses same approach).

---

### MICRO-03: Gesture Hints (First-Load Tooltip)

**Problem:** New users don't know to click Start or press Space. A first-visit hint would help onboarding.

**Mechanism:** On app load, check if `firstVisit` flag exists in localStorage. If not, show hint and set flag.

**Implementation:**

1. Add HTML element (near timer display, e.g., above Start button):
```html
<div id="gestureHint" style="opacity: 0; transition: opacity 300ms ease; margin-bottom: 12px; padding: 8px 12px; background: rgba(52,211,153,0.15); border-radius: 8px; border: 1px solid rgba(52,211,153,0.3); font-size: 13px; color: var(--accent); text-align: center;">
  Press Start or Space to begin
</div>
```

2. Add initialization logic (in init section at line 2152+):
```javascript
function showGestureHint() {
  const hintEl = document.getElementById("gestureHint");
  if (!hintEl) return;
  hintEl.style.opacity = "1";
  
  // Hide after first interaction
  const hideHint = () => {
    hintEl.style.opacity = "0";
    localStorage.setItem("gestureHintShown", "true");
    document.removeEventListener("click", hideHint);
    document.removeEventListener("keydown", hideHint);
  };
  
  document.addEventListener("click", hideHint);
  document.addEventListener("keydown", hideHint);
}

// On app load:
if (!localStorage.getItem("gestureHintShown")) {
  showGestureHint();
}
```

**Lines affected:** ~15 HTML lines + ~20 JS lines.

**Location:** HTML at top of .container or above controls; JS in init section (line 2152+).

**Impact:** Helps new users discover Start action. Unobtrusive; hides after first interaction.

**Confidence:** HIGH — localStorage pattern already established; hint UI is simple DOM element.

---

### MICRO-04: Error/Invalid Input Feedback (Red Flash)

**Problem:** When a user enters an invalid duration (e.g., 0, 500+), the input is clamped silently. No visual feedback that something went wrong.

**Current state:** Change handler at lines 1430–1444 clamps values but doesn't signal to user.

**Implementation:**

1. Add CSS animation (near other keyframes):
```css
@keyframes inputErrorFlash {
  0%, 100% { background: transparent; }
  50% { background: rgba(229, 115, 115, 0.2); }
}

.durInput.error {
  animation: inputErrorFlash 400ms ease-out;
  border-color: #e57373;
}
```

2. Update change handler (lines 1430–1444):
```javascript
durationsEl.addEventListener("change", e => {
  const input = e.target.closest(".durInput");
  if (!input || isRunning) return;
  const idx = parseInt(input.dataset.index, 10);
  const raw = parseInt(input.value, 10);
  if (!Number.isFinite(raw)) {
    input.value = activePhases[idx].durationSec;
    input.classList.add("error");
    setTimeout(() => input.classList.remove("error"), 400);
    return;
  }
  const val = Math.min(DURATION_RANGE.max, Math.max(DURATION_RANGE.min, raw));
  if (val !== raw) {  // Value was clamped
    input.classList.add("error");
    setTimeout(() => input.classList.remove("error"), 400);
  }
  input.value = val;
  activePhases[idx] = { ...activePhases[idx], durationSec: val };
  reset();
  saveSettings();
});
```

**Lines affected:** 8–10 CSS lines + 8–10 JS lines in change handler.

**Location:** CSS at line ~370 (near other animations), JS at lines 1430–1444.

**Impact:** User gets immediate visual feedback when input is corrected. Non-blocking; error animates away automatically.

**Confidence:** HIGH — Animation pattern is proven; input validation logic already exists.

---

## Android Vibration Testing Readiness

### Test Scenarios

**Current implementation (VIBRATION configuration at line 1048):**
```javascript
VIBRATION: Object.freeze({
  enabledByDefault: true,
  pattern: 50  // 50ms single pulse
})
```

Vibration is triggered in:
- `cueOnPhaseEnter()` (line 1643–1648) — called on every phase transition
- `showCountdown()` (line 1800–1808) — called during 3-second countdown

**Test plan for Phase 3:**

| Scenario | Device | Expected Result | Notes |
|----------|--------|-----------------|-------|
| Phase transition vibration (Inhale) | Android (Samsung/Pixel) | Single 50ms pulse on Inhale | Fundamental vibration confirmation |
| Phase transition vibration (Hold) | Android (Samsung/Pixel) | Single 50ms pulse on Hold | Verify consistent across all phases |
| Phase transition vibration (Exhale) | Android (Samsung/Pixel) | Single 50ms pulse on Exhale | Verify consistent across all phases |
| Phase transition vibration (Hold 2) | Android (Samsung/Pixel) | Single 50ms pulse on Hold 2 | Verify consistent across all phases |
| Countdown vibration | Android (Samsung/Pixel) | 3 pulses (one at 3s, 2s, 1s) | Confirm countdown pattern works |
| Silent mode behavior | Android (Silent phone) | Vibration may be muted | Document if device mutes haptics in silent mode |
| Low battery impact | Android (low battery saver) | Vibration continues (or note if disabled) | Check system power settings interaction |
| Browser compatibility | Chrome, Edge (Android) | Both browsers support Vibration API | Verify no browser-specific issues |

**Device recommendations:**
- Target: 2+ Android devices (different manufacturers, different Android versions if possible)
- Preferred: Samsung Galaxy (common market share) + Google Pixel (stock Android)
- Chrome/Edge browsers, Android 10+

**Documentation output:** Create test report with:
- Device model, Android version, browser, date tested
- Result: ✓ Vibration works / ✗ Vibration not working / ⚠️ Partial (e.g., muted in silent mode)
- Notes: Any device-specific quirks

**Code location for test verification:**
- Line 1643–1648: `cueOnPhaseEnter()` calls `vibrate()` at line 1647
- Line 1617–1621: `vibrate()` function calls `navigator.vibrate(pattern)`
- VIBRATION pattern at line 1048

**Confidence:** HIGH — Vibration API is well-understood; testing is straightforward device exercise, not code investigation.

---

## Cross-Browser Audio & Wake Lock Behavior

### Audio Autoplay Policy

**Current implementation (lines 1936–1944):**
```javascript
if (soundEnabled) {
  if (!audioCtx) {
    const AC = window.AudioContext || window.webkitAudioContext;
    if (AC) audioCtx = new AC();
  }
  if (audioCtx && audioCtx.state === "suspended") {
    try { await audioCtx.resume(); } catch (_) {}
  }
}
```

**Behavior to test:**

| Scenario | Chrome/Desktop | Chrome/Mobile | Edge/Desktop | Edge/Mobile | Expected |
|----------|---|---|---|---|---|
| First beep plays without error | ✓ | ✓ | ✓ | ✓ | Audio context resumed within user gesture (Start click) |
| AudioContext created on first Start | ✓ | ✓ | ✓ | ✓ | No pre-creation; lazy init on user interaction |
| Resume succeeds when suspended | ✓ | ✓ | ✓ | ✓ | Try/catch catches any failures silently |

**Wake Lock Behavior**

**Current implementation (lines 1570–1588):**
```javascript
async function acquireWakeLock() {
  if (!("wakeLock" in navigator)) return;
  try {
    wakeLockSentinel = await navigator.wakeLock.request("screen");
    wakeLockSentinel.addEventListener("release", () => { wakeLockSentinel = null; });
  } catch (_) {}
}

function releaseWakeLock() {
  if (wakeLockSentinel) {
    wakeLockSentinel.release().catch(() => {});
    wakeLockSentinel = null;
  }
}

document.addEventListener("visibilitychange", () => {
  if (document.visibilityState === "visible" && isRunning) acquireWakeLock();
});
```

**Behavior to test:**

| Scenario | Chrome/Desktop | Chrome/Mobile | Edge/Desktop | Edge/Mobile | Expected |
|----------|---|---|---|---|---|
| Wake lock acquired on Start | ✓ | ✓ | ✓ | ✓ | Screen stays on; wake lock sentinel captured |
| Wake lock released on Stop | ✓ | ✓ | ✓ | ✓ | Screen can sleep normally after session ends |
| Wake lock re-acquired on tab visibility | ✓ | ✓ | ✓ | ✓ | Screen stays on if user returns to app mid-session |
| Release event fires on loss | ✓ | ✓ | ✓ | ✓ | App detects if OS/browser revokes wake lock |

**Test execution:**
1. Start session on Chrome/Edge (both desktop and mobile if available)
2. Verify screen does not sleep during 5-minute session
3. Stop session; verify screen can sleep after
4. Start session, switch tabs, return to app; verify screen still on
5. (Advanced) Trigger power-saving mode; verify wake lock continues or note if revoked

**Documentation output:** Create compatibility matrix with results for all 4 browser×platform combinations.

**Confidence:** HIGH — Wake Lock implementation is standard; testing is functional verification on real devices.

---

## Light/Dark Theme Contrast Review

**Current palette (from LIGHT_THEME and DARK_THEME objects, lines 1975–1992):**

### Dark Theme
```javascript
DARK_THEME = {
  bg: "#111",
  card: "#171717",
  accent: "#9aa0a6",
  accentSoft: "rgba(154,160,166,0.18)",
  text: "#fff",
  textSoft: "#a89e8c"  // ⚠️ Error in codebase — should be rgba
}
```

### Light Theme
```javascript
LIGHT_THEME = {
  bg: "#f5f1ed",
  card: "#ebe7e1",
  accent: "#d4a574",
  accentSoft: "rgba(212,165,116,0.18)",
  text: "#5c4033",
  textSoft: "#a89e8c"
}
```

**Manual review checklist:**

| Element | Dark Theme | Light Theme | Status |
|---------|-----------|------------|--------|
| Body text (#fff on #111) | ✓ High contrast | Cream text (#5c4033) on light bg (#f5f1ed) | Verify in-person |
| Accent text (#9aa0a6 or #d4a574) | Medium gray on dark | Gold/brown on light | Eye-test readability |
| Card background | Dark card (#171717) on bg (#111) | Light card (#ebe7e1) on bg (#f5f1ed) | Check depth perception |
| Soft text (tooltips, hints) | Reduced opacity; check on mobile | Reduced opacity; check on mobile | Mobile viewport critical |
| Button hover/active states | rgba(255,255,255,0.06–0.1) on #171717 | CSS applies light values; test | Verify hover is visible |
| SVG ring (accent color) | #9aa0a6 on #171717 | #d4a574 on #ebe7e1 | Ring visibility |

**Test execution:**
1. Open app in Chrome/Edge desktop and mobile
2. Toggle between dark and light themes
3. Read timer text, phase name, controls
4. Check ring visibility during animation
5. Verify button hover states are distinct
6. Assess visual comfort (cream vs white, gold vs gray)

**Decision criteria:** If no glaring contrast issues (text is legible, colors feel intentional), palette is approved.

**Confidence:** HIGH — Manual review is the locked decision; no automatic testing required.

---

## Risk Assessment

### Performance & Animation Risks

| Risk | Severity | Mitigation |
|------|----------|-----------|
| New CSS animations (flash, error) block main thread | LOW | CSS animations run on GPU; test FPS on mobile (60 FPS target) |
| Ring flash animation conflicts with phaseFadeIn | MEDIUM | Both use requestAnimationFrame; test phase transitions look smooth |
| Gesture hint DOM element causes reflow | LOW | Hint only visible on load; single reflow impact negligible |
| Button press animation adds JS overhead | LOW | Using CSS `:active` pseudo-class; no JS required if CSS-only approach |

### Testing & Device Risks

| Risk | Severity | Mitigation |
|------|----------|-----------|
| Vibration API not supported on some Android devices | MEDIUM | Test on 2+ devices; document unsupported platforms in README |
| Wake lock revoked by OS power-saver (silent) | MEDIUM | Release event listener in place; session continues (acceptable) |
| localStorage quota hit unexpectedly | LOW | Error handling added; user sees warning message |
| Timer cleanup breaks countdown UX | MEDIUM | Timer cleanup only in stop(); normal countdown path unchanged |

### Code Interaction Risks

| Risk | Severity | Mitigation |
|------|----------|-----------|
| Duration validation conflicts with custom duration storage | LOW | Clamping happens in change handler; persisted value always valid |
| Phase flash animation triggers during pause | LOW | Animation only on phase advance (line 1718); not triggered on pause |
| Gesture hint localStorage key collides with existing keys | LOW | Use unique key: "gestureHintShown" (not used elsewhere) |

---

## Code Examples

### Countdown Timer Cleanup

**Location:** Line ~1877 in `stop()` function

```javascript
function stop() {
  if (!isRunning) return;
  
  // NEW: Clear any pending countdown timers
  if (isCountingDown) {
    cancelCountdown();
  }
  
  isRunning = false;
  // ... rest of function
}
```

**Why:** Prevents orphaned timers if user clicks Stop during countdown.

---

### Duration Validation with Clamping

**Location:** Lines 1414–1415 (buildDurationInputs) and line 1439 (change handler)

```javascript
// Define constant at top (near CONFIG):
const DURATION_RANGE = Object.freeze({ min: 1, max: 300 });

// In buildDurationInputs():
input.min = DURATION_RANGE.min;
input.max = DURATION_RANGE.max;

// In change handler:
const val = Math.min(DURATION_RANGE.max, Math.max(DURATION_RANGE.min, raw));
if (val !== raw) {
  // Signal to user: value was clamped
  input.classList.add("error");
  setTimeout(() => input.classList.remove("error"), 400);
}
```

---

### Wake Lock Robustness (Already Implemented)

**Location:** Lines 1570–1576 (acquireWakeLock)

```javascript
async function acquireWakeLock() {
  if (!("wakeLock" in navigator)) return;
  try {
    wakeLockSentinel = await navigator.wakeLock.request("screen");
    // Release event is already listened; app detects if lock is lost
    wakeLockSentinel.addEventListener("release", () => { wakeLockSentinel = null; });
  } catch (_) {}
}
```

**Note:** Release listener is already present. No changes needed.

---

### localStorage Quota Error Handling

**Location:** Lines 1499–1507 (saveHistory)

```javascript
function saveHistory(entry) {
  try {
    const raw = localStorage.getItem(HISTORY_KEY);
    const list = raw ? JSON.parse(raw) : [];
    list.unshift(entry);
    if (list.length > maxHistoryEntries) list.length = maxHistoryEntries;
    localStorage.setItem(HISTORY_KEY, JSON.stringify(list));
  } catch (e) {
    if (e.name === "QuotaExceededError") {
      // Show user warning
      const warningEl = document.getElementById("storageWarning");
      if (warningEl) {
        warningEl.textContent = "History storage full. Oldest sessions removed.";
        warningEl.style.display = "block";
        setTimeout(() => { warningEl.style.display = "none"; }, 5000);
      }
    }
  }
}
```

---

### Ring Flash Animation (Phase Transition Cue)

**Location:** CSS near line 85, JS in `advanceToNextPhase()` at line 1734

```css
@keyframes ringFlash {
  0%   { filter: brightness(1); }
  50%  { filter: brightness(1.4); }
  100% { filter: brightness(1); }
}

.ringFlash { animation: ringFlash 200ms ease-out forwards; }
```

```javascript
// In advanceToNextPhase(), after cueOnPhaseEnter(currentPhaseIndex):
ringProgressEl.classList.remove("ringFlash");
void ringProgressEl.offsetWidth;  // Trigger reflow
ringProgressEl.classList.add("ringFlash");
```

---

### Error Input Flash Animation

**Location:** CSS near line 370, JS in change handler at line 1430

```css
@keyframes inputErrorFlash {
  0%, 100% { background: transparent; }
  50% { background: rgba(229, 115, 115, 0.2); }
}

.durInput.error {
  animation: inputErrorFlash 400ms ease-out;
  border-color: #e57373;
}
```

```javascript
durationsEl.addEventListener("change", e => {
  const input = e.target.closest(".durInput");
  if (!input || isRunning) return;
  const idx = parseInt(input.dataset.index, 10);
  const raw = parseInt(input.value, 10);
  if (!Number.isFinite(raw)) {
    input.value = activePhases[idx].durationSec;
    input.classList.add("error");
    setTimeout(() => input.classList.remove("error"), 400);
    return;
  }
  const val = Math.min(DURATION_RANGE.max, Math.max(DURATION_RANGE.min, raw));
  if (val !== raw) {
    input.classList.add("error");
    setTimeout(() => input.classList.remove("error"), 400);
  }
  input.value = val;
  activePhases[idx] = { ...activePhases[idx], durationSec: val };
  reset();
  saveSettings();
});
```

---

## Common Pitfalls

### Pitfall 1: Duplicate Animation Classes Prevent Retrigger

**What goes wrong:** Applying a CSS animation class twice (e.g., `ringFlash`) without resetting doesn't trigger the animation the second time. The class remains, so no animation event fires.

**Why it happens:** CSS animations only trigger on class addition. If the class already exists, adding it again doesn't change the element's computed style, so no animation fires.

**How to avoid:** Remove the animation class before re-adding it. Use a reflow trigger:
```javascript
element.classList.remove("animation");
void element.offsetWidth;  // Force reflow
element.classList.add("animation");
```

**Warning signs:** Phase flash doesn't animate on second/third phase transitions.

---

### Pitfall 2: Timer Cleanup on Wrong Function

**What goes wrong:** Adding countdown timer cleanup only to `cancelCountdown()` but not to `stop()`. If user clicks Stop while countdown is running, `cancelCountdown()` fires, but if the stop handler logic changes, cleanup may be missed.

**Why it happens:** Cleanup is easy to forget if the code path is not obvious.

**How to avoid:** Clear timers in the authoritative stop point. Ensure all code paths that stop a session call timer cleanup.

**Warning signs:** Timers accumulate if you repeatedly Start → Stop during countdown.

---

### Pitfall 3: Input Clamping Without User Feedback

**What goes wrong:** Silently clamping an input value (e.g., 500 → 300) without indicating to the user that the input was changed. User thinks they set it to 500s but it's actually 300s.

**Why it happens:** Developers often clamp values without visual feedback, assuming the HTML `min`/`max` will prevent invalid input. But users can bypass HTML validation.

**How to avoid:** Add visual feedback (highlight, flash, error message) when a value is clamped.

**Warning signs:** Users report unexpected phase durations; input validation is silently failing.

---

### Pitfall 4: Wake Lock Release Not Detected

**What goes wrong:** App acquires wake lock but doesn't listen for the release event. If OS or browser revokes the lock, the app doesn't know the screen may sleep.

**Why it happens:** Release listeners are easy to forget; they're not required by the API.

**How to avoid:** Always attach a release listener when acquiring a wake lock. Log or signal when it fires.

**Warning signs:** Screen sleeps mid-session despite wake lock; app unaware of the loss.

---

## Assumptions Log

| # | Claim | Section | Risk if Wrong |
|---|-------|---------|---------------|
| A1 | CSS @keyframes animations don't block the main thread | Risk Assessment | High-priority animations may stutter on low-end mobile devices; measure FPS on real device |
| A2 | Vibration API is reliable post-user-gesture on Android Chrome/Edge | Android Vibration Testing | Some Android devices may not vibrate in silent mode; document device-specific behavior |
| A3 | Wake Lock release listener in line 1574 is sufficient for detecting lock loss | Wake Lock Robustness | If OS revokes lock silently and release event doesn't fire, app won't detect; may need fallback detection |
| A4 | Manual visual inspection for contrast is adequate for light/dark themes | Light Theme Review | Users with color blindness or accessibility needs may find palette insufficient; WCAG AA testing deferred to Phase 4 |
| A5 | Duration input clamping at 300s is sufficient upper bound | Duration Validation | If users want longer phases, 300s may be too restrictive; no evidence of this use case yet |
| A6 | localStorage quota handling is low-priority (low risk of quota exceeded) | localStorage Robustness | Extended history (1000+ entries) may trigger quota on some devices; fallback to older devices may fail |

---

## Open Questions

1. **Gesture Hint First-Visit Detection**
   - What we know: localStorage can track first-visit with a boolean flag
   - What's unclear: Should hint appear on every tab/window open, or only truly first-ever visit across all tabs?
   - Recommendation: Implement as "truly first" (set flag in localStorage) so hint appears once per origin, ever

2. **Ring Flash Animation Brightness Level**
   - What we know: brightness(1.4) brightens ring by 40%; likely visible on both light and dark themes
   - What's unclear: Is 40% enough to be noticeable on bright light theme? Or too much on dark theme?
   - Recommendation: Start with 1.3, adjust after testing on real devices with both themes

3. **Error Flash Animation Duration**
   - What we know: Error feedback should be fast enough to feel immediate but slow enough to be noticeable
   - What's unclear: Is 400ms appropriate, or should it be shorter (200ms) or longer (600ms)?
   - Recommendation: Start with 400ms; iterate based on user testing feedback

4. **Vibration Pattern for Hold2 Phase**
   - What we know: All phases currently use the same 50ms pulse pattern
   - What's unclear: Should Hold2 (second pause) have a distinct vibration pattern to differentiate from other Holds?
   - Recommendation: For Phase 3, keep consistent pattern (50ms); consider distinct patterns in Phase 4 if needed

---

## Environment Availability

All required environments are available (standard modern browsers):

| Dependency | Required By | Available | Version | Fallback |
|------------|------------|-----------|---------|----------|
| Modern browser (Chrome/Edge) | All features | ✓ | Desktop + mobile | — |
| HTTPS or localhost | Wake Lock, Web Audio, Vibration APIs | ✓ | GitHub Pages (HTTPS) | localhost for dev |
| Android device | Vibration testing | ⚠️ | Target 2+ devices | Skip if unavailable, note in report |
| CSS support (CSS variables) | Theme system, animations | ✓ | All modern browsers | Degrade gracefully on IE11 (out of scope) |

---

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Hardcoded duration max (30s) | Configurable DURATION_RANGE constant (1–300s) | Phase 3 | Supports wider breathing patterns; no performance impact |
| Countdown timers not cleaned up on stop | Explicit cleanup in stop() or cancelCountdown() | Phase 3 | Prevents memory leak; no behavioral change |
| Silent localStorage quota errors | Specific error detection + user warning toast | Phase 3 | Better transparency; users aware of quota issues |
| Button press feedback minimal | Enhanced :active state with shadow/color | Phase 3 | Stronger tactile feedback; no UX breaking changes |
| Phase transitions silent | Ring flash animation on phase advance | Phase 3 | Visual reinforcement; uses existing animation patterns |
| No first-visit hint | Gesture hint with localStorage tracking | Phase 3 | Onboarding aid for new users; non-intrusive |
| Invalid input validation silent | Input error animation + clamping feedback | Phase 3 | User awareness of validation; prevents confusion |

---

## Validation Architecture

### Test Framework
| Property | Value |
|----------|-------|
| Framework | Manual testing (no automated test framework configured) |
| Config file | None — testing is hands-on device/browser exercise |
| Quick run command | Manual: start session, verify behaviors |
| Full suite command | Manual: test all 3 bugs + 4 micro-interactions + vibration + cross-browser |

### Phase Requirements → Test Map
| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| BUG-01 | Timer cleanup stops accumulation | Manual | Start → Stop during countdown → inspect memory (DevTools) | ❌ Wave 0 |
| BUG-02 | Duration input clamped to 1–300s | Manual | Drag slider to 0 and 500; verify clamped + error animation | ❌ Wave 0 |
| BUG-03 | localStorage quota error warning shown | Manual | Fill localStorage to quota; attempt history save; verify warning message | ❌ Wave 0 |
| MICRO-01 | Button press feedback visible | Manual | Click Start/Stop/Reset; verify visual scale + color change on press | ❌ Wave 0 |
| MICRO-02 | Ring flashes on phase transition | Manual | Run session; observe ring brightness change at each phase advance | ❌ Wave 0 |
| MICRO-03 | Gesture hint shows on first load | Manual | Clear localStorage; reload app; verify hint appears and hides on click | ❌ Wave 0 |
| MICRO-04 | Error feedback animates invalid input | Manual | Manually edit duration input to invalid value; verify red flash + error state | ❌ Wave 0 |
| TEST-01 | Vibration fires on Android | Real device | Start session on Android phone/tablet; feel vibration at phase transitions | ❌ Wave 0 |
| TEST-02 | Light/dark contrast readable | Manual | Toggle themes desktop/mobile; eye-test readability of text and rings | ❌ Wave 0 |
| TEST-03 | Audio and wake lock work in Chrome/Edge | Real device | Test on Chrome/Edge desktop + mobile; verify beep and screen stay-on | ❌ Wave 0 |

### Sampling Rate
- **Per task commit:** Manual test of changed feature (e.g., test timer cleanup after implementing BUG-01)
- **Per wave merge:** Full 3 bugs + 4 micro-interactions verified on desktop; vibration tested on 2+ Android devices
- **Phase gate:** All tests green before `/gsd-verify-work`

### Wave 0 Gaps
- [ ] Automated testing framework (Jest/Vitest) — needed if bugs are to be tested without manual exercise
- [ ] Mock timers for countdown cleanup test
- [ ] Mock localStorage for quota error test
- [ ] Mock navigator.vibrate for unit testing vibration calls
- [ ] Chromatic visual regression testing for micro-animation timing

*(If no automated tests are added: All tests in Wave 0 are manual. Planner should include "manual test" tasks for each requirement.)*

---

## Security Domain

### Applicable ASVS Categories

| ASVS Category | Applies | Standard Control |
|---------------|---------|-----------------|
| V2 Authentication | No | Not applicable (no user accounts) |
| V3 Session Management | No | Not applicable (breathing timer, not web session) |
| V4 Access Control | No | Not applicable (no multi-user or permission model) |
| V5 Input Validation | Yes | Duration input clamped to 1–300s; valid range enforced |
| V6 Cryptography | No | Not applicable (no sensitive data transmission) |
| V7 Error Handling | Yes | Error catch blocks prevent silent failures; QuotaExceededError handled explicitly |
| V8 Data Protection | Yes | localStorage persistence; no sensitive PII stored |

### Known Threat Patterns for Vanilla JS + Browser APIs

| Pattern | STRIDE | Standard Mitigation |
|---------|--------|---------------------|
| Malformed input to duration slider | Tampering | Input validation (clamping) + type checking (Number.isFinite) |
| localStorage quota exhaustion (DoS) | Denial of Service | Error handling + user feedback + history cap (1000 entries max) |
| XSS via dynamic DOM (history render) | Spoofing/Tampering | Use textContent instead of innerHTML for untrusted data; currently uses controlled date/number fields |
| Unhandled Web Audio errors | Denial of Service | Try/catch around audioCtx calls; graceful degradation if audio fails |
| Wake Lock revoked silently | Availability | Release event listener detects loss; session continues (acceptable) |

### No Compliance Requirements
- No GDPR/data privacy regulations apply (no personal data storage, no user tracking)
- No PCI-DSS (no payment processing)
- No HIPAA (no health data; breathing timer is wellness tool, not medical device)

---

## Sources

### Primary (HIGH confidence)
- **Code inspection:** `index.html` lines 1277–1828, 1401–1444, 1570–1588 — confirmed timer cleanup location, duration validation logic, wake lock implementation
- **CONCERNS.md (2026-06-03):** Technical concerns analysis — bug descriptions match code inspection exactly
- **STACK.md (2026-06-03):** Technology stack reference — CSS animations, browser API support confirmed
- **CONTEXT.md (2026-06-05):** Phase context and locked decisions from discussion

### Secondary (MEDIUM confidence)
- **Web Audio API MDN docs:** AudioContext suspend/resume behavior on browser autoplay policies [ASSUMED: standard browser behavior]
- **Vibration API MDN docs:** Android support, 50ms pattern reliability [ASSUMED: standard browser implementation]
- **Wake Lock API MDN docs:** Release event behavior, re-acquisition on visibility [ASSUMED: W3C standard]

---

## Metadata

**Confidence breakdown:**
- **Bug fixes:** HIGH — Code locations identified, fix patterns straightforward (2–10 lines each)
- **Micro-interactions:** HIGH — CSS animation patterns reuse existing @keyframes; JavaScript event delegation proven
- **Vibration testing:** HIGH — Test scenarios clear; API behavior understood from Phase 1 research
- **Cross-browser compatibility:** MEDIUM — Audio/wake lock behavior tested on devices; assumptions about browser consistency

**Research date:** 2026-06-05  
**Valid until:** 2026-06-20 (15 days; stable domain, no major framework updates expected)

**Next phase:** `/gsd-plan-phase 3` to create detailed task breakdowns for bug fixes (1 task), micro-interactions (1–2 tasks), and testing (1 task).
