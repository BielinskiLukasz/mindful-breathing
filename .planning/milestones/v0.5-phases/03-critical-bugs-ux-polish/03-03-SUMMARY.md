---
phase: 03-critical-bugs-ux-polish
plan: "03"
subsystem: micro-interactions
tags: [animation, css, ux, onboarding, localStorage]
dependency_graph:
  requires: ["03-01"]
  provides: [ring-flash-animation, gesture-hint]
  affects: [index.html]
tech_stack:
  added: []
  patterns: [css-keyframes, reflow-restart, localstorage-flag, event-delegation]
key_files:
  created: []
  modified:
    - index.html
decisions:
  - "Use brightness filter (not opacity/transform) for ring flash to preserve SVG stroke rendering"
  - "Use offsetWidth reflow trick to restart CSS animation on consecutive phase advances"
  - "Gesture hint positioned above controls div in controlsSection to avoid obscuring ring/timer"
  - "Event delegation (document-level click/keydown) hides hint on any user interaction"
metrics:
  duration: "2m"
  completed: "2026-06-06"
---

# Phase 03 Plan 03: Micro-Interactions (Ring Flash & Gesture Hint) Summary

Phase transition ring flash animation and first-visit gesture hint implemented in index.html using CSS keyframes, SVG filter reflow, and localStorage flag tracking.

## Tasks Completed

| Task | Name | Commit | Files |
|------|------|--------|-------|
| 1 | Add Phase Transition Ring Flash Animation | 36df392 | index.html |
| 2 | Add First-Visit Gesture Hint with localStorage Tracking | ce46b63 | index.html |

## Implementation Details

### Task 1: Phase Transition Ring Flash Animation (MICRO-02)

**CSS added (lines 88-94):**
- `@keyframes ringFlash`: brightness 1 → 1.3 → 1 over 200ms with ease-out timing
- `.ringFlash` class applies the animation with `forwards` fill mode

**JS trigger added in `advanceToNextPhase()` (lines 1765-1767):**
```javascript
ringProgressEl.classList.remove("ringFlash");
void ringProgressEl.offsetWidth;  // Force reflow to restart animation
ringProgressEl.classList.add("ringFlash");
```
The `offsetWidth` read forces a browser reflow, ensuring the animation restarts even on rapid consecutive phase transitions (e.g., end of one phase immediately followed by another).

**Theme compatibility:** Brightness filter works on both dark (#9aa0a6 accent) and light (#d4a574 accent) themes — both brighten visibly at 1.3x.

### Task 2: First-Visit Gesture Hint with localStorage Tracking (MICRO-03)

**HTML element added (line 1149):** `gestureHint` div above the controls buttons, styled with green accent (rgba(52,211,153)) background tint and border. Starts at `opacity: 0` with 300ms CSS transition for smooth fade.

**`showGestureHint()` function (lines 1823-1839):**
- Fades hint in by setting `opacity: 1`
- Registers document-level `click` and `keydown` listeners
- On first interaction: fades hint out, sets `localStorage.gestureHintShown = "true"`, removes listeners

**Init check (lines 2218-2220):**
```javascript
if (!localStorage.getItem("gestureHintShown")) {
  showGestureHint();
}
```
Runs after all other initialization, showing the hint only when the flag is absent.

## Verification

### Ring Flash
- Triggers on every `advanceToNextPhase()` call (4 times per Relax cycle, 4 times per Box cycle, 3 times per 4-7-8 cycle)
- 200ms duration matches countdown pop animation timing
- `offsetWidth` reflow ensures animation restarts even when phase advances happen in rapid succession
- No interference with `strokeDashoffset` ring fill animation (separate CSS properties)

### Gesture Hint
- Starts hidden (`opacity: 0`) on load; shown only if `gestureHintShown` key absent from localStorage
- Smooth fade-in via CSS transition (300ms ease)
- Hides on any user interaction: Start button click, Space keypress, preset click, settings click, etc.
- localStorage flag persists across page reloads, browser restarts (same origin)
- Non-intrusive: positioned above controls, uses accent color palette, does not block timer or ring

## Deviations from Plan

None - plan executed exactly as written.

## Threat Surface Scan

No new network endpoints, auth paths, file access patterns, or schema changes introduced. The `gestureHintShown` localStorage key is unique and does not collide with existing keys (`mb_v1`, `mb_history`).

## Self-Check: PASSED

- index.html modified with all changes present (verified via grep)
- Commit 36df392: feat(03-03) ring flash animation
- Commit ce46b63: feat(03-03) gesture hint
- Both commits confirmed in git log
