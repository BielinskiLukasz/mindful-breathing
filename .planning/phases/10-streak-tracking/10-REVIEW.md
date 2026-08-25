---
phase: 10-streak-tracking
reviewed: 2026-08-25T00:00:00Z
depth: standard
files_reviewed: 1
files_reviewed_list:
  - index.html
findings:
  critical: 1
  warning: 2
  info: 1
  total: 4
status: issues_found
---

# Phase 10: Code Review Report

**Reviewed:** 2026-08-25
**Depth:** standard
**Files Reviewed:** 1
**Status:** issues_found

## Summary

Phase 10 introduces streak tracking with a `computeStreak()` function, localStorage persistence, and a new streak panel UI. Overall structure and ARIA accessibility are sound, but a critical logic bug in the streak calculation can cause the displayed "Current" streak to be inflated when older streaks exist. Additionally, focus management is missing from panel opening, and date validation could be hardened.

---

## Critical Issues

### CR-01: computeStreak() incorrectly inflates current streak when older longer streaks exist

**File:** `index.html:2941-3001`

**Issue:**
The `computeStreak()` function walks backward through sorted dates to count consecutive days from today. When a gap is found in consecutive dates, it correctly saves the streak as a candidate for `longest`, but then **continues walking backward through older dates and increments `current` if an older streak is longer than the active current streak**.

The function should return the streak actively maintained starting from today/yesterday, but gaps in the date history are not treated as terminal events for the current streak calculation.

**Example scenario:**
- History: Jan 20, 19, 18 (3-day current streak) → gap → Jan 15, 14, 13, 12 (4-day old streak)
- Today: Jan 20
- **Expected:** `current=3` (active from today), `longest=4` (historical)
- **Actual:** `current=4` (incorrectly updated), `longest=4`

The bug occurs at lines 2979–2992 in the `else` branch. After setting `streak = 1` (line 2991), if consecutive old dates follow, the line `current = Math.max(current, streak)` (line 2987) will inflate `current` to reflect the old streak length.

**Fix:**
Break out of the loop after detecting the first gap in consecutive dates. The current streak ends when a gap is detected going backward from today:

```javascript
if (date === prevDateStr) {
  streak++;
  current = Math.max(current, streak);
} else {
  // Gap found; current streak is broken
  longest = Math.max(longest, streak);
  break;  // ← Add this line
}
```

This ensures `current` only reflects the streak actively maintained from today/yesterday, not historical streaks in older history.

---

## Warnings

### WR-01: Missing focus management when opening streak panel

**File:** `index.html:3462-3466`

**Issue:**
The `openStreak()` function opens the panel but does not move keyboard focus into it:

```javascript
function openStreak() {
  streakOverlay.classList.add("visible");
  openPanelElement = streakOverlay;
  updateStreakPanel();
}
```

This violates WCAG 2.1 SC 2.4.3 (Focus Order). Contrast with `openInfo()` (line 3439) and `openSettings()` (line 3486), which both set focus to the close button:

```javascript
function openInfo() {
  infoOverlay.classList.add("visible");
  openPanelElement = infoOverlay;
  infoCloseBtn.focus();  // ← Focus moved into panel
}
```

Keyboard users will not know focus has changed and may be trapped or confused.

**Fix:**
The streak panel lacks a close button, but focus should still be set to the panel or the first focusable descendant. Add a close button and set focus, or at minimum set focus to a tabbable element within the panel. For consistency with info/settings, add a visible close button:

```javascript
function openStreak() {
  streakOverlay.classList.add("visible");
  openPanelElement = streakOverlay;
  updateStreakPanel();
  // Set focus to first focusable element or a dedicated close button
  // Example: streakCloseBtn.focus();
}
```

### WR-02: Invalid date strings in history entries are not validated in computeStreak()

**File:** `index.html:2946-2951`

**Issue:**
The `computeStreak()` function does not validate that `entry.date` is a valid ISO date string before passing it to `new Date()`:

```javascript
for (const entry of history) {
  if (entry.date && !entry.incomplete) {
    const d = new Date(entry.date).toLocaleDateString('en-CA');
    dates.add(d);
  }
}
```

If `entry.date` is malformed (e.g., `"2024-13-45"` or `"not-a-date"`), `new Date()` returns an Invalid Date object. Calling `.toLocaleDateString()` on an Invalid Date may:
- Return a non-date string like `"Invalid Date"` (which won't match today's date and breaks streak logic)
- Throw an exception in some browsers (caught by outer `try-catch` but silently fails)

This is not a security issue (data from localStorage), but it degrades robustness when history is corrupted.

**Fix:**
Validate dates before processing:

```javascript
for (const entry of history) {
  if (entry.date && !entry.incomplete) {
    const dateObj = new Date(entry.date);
    // Validate that dateObj is a valid Date
    if (dateObj instanceof Date && !isNaN(dateObj)) {
      const d = dateObj.toLocaleDateString('en-CA');
      dates.add(d);
    }
  }
}
```

---

## Info

### IN-01: Streak panel lacks visible close button, reducing discoverability

**File:** `index.html:1380-1402` (HTML), `index.html:3462-3477` (JS)

**Issue:**
The streak panel (unlike info and settings panels) has no visible close button. Users must discover that clicking the overlay or pressing Escape closes the panel. The info and settings panels each have an explicit `×` close button with `aria-label`, making the interaction clearer.

While the Escape key handler (line 3562) and overlay click handler (line 3476) correctly close the panel, the lack of a visible affordance violates UI consistency and discoverability best practices.

**Fix:**
Add a close button to the streak panel HTML (mirroring the info and settings panels) and set focus to it when opening:

```html
<div class="infoPanel" id="streakPanel">
  <h2 class="infoTitle" id="streakPanelTitle">Your Streak</h2>
  <button class="infoCloseBtn" id="streakCloseBtn" title="Close streak" aria-label="Close streak">×</button>
  <!-- ... rest of panel ... -->
</div>
```

Then update `openStreak()` to set focus:

```javascript
function openStreak() {
  streakOverlay.classList.add("visible");
  openPanelElement = streakOverlay;
  updateStreakPanel();
  document.getElementById("streakCloseBtn").focus();
}
```

---

## Structural Notes

**Threat T-10-01 (history array input validation):** The `computeStreak()` function does include array type check (`if (!Array.isArray(history))`) and filters incomplete entries, but date string validation is lacking (see WR-02).

**Threat T-10-03 (O(n) performance):** computeStreak is O(n + d log d) where n = history entries (max 14) and d = unique dates (≤ n). This is negligible and acceptable.

**Strengths:**
- Correct localStorage persistence of `longestStreak` with type checking in `loadSettings()` (line 2278)
- Proper high-water mark logic in `maybeUpdateLongestStreak()` (lines 3420–3431)
- Keyboard Escape handler integration is correct (line 3562)
- ARIA attributes present: `role="dialog"`, `aria-modal="true"`, `aria-labelledby` (line 1380)
- Empty catch blocks use ignored underscore pattern `catch (_)` (not dangerous)

---

_Reviewed: 2026-08-25_
_Reviewer: Claude (gsd-code-reviewer)_
_Depth: standard_
