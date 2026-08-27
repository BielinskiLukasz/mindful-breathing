---
phase: 10-streak-tracking
fixed_at: 2026-08-25T20:45:00Z
review_path: .planning/phases/10-streak-tracking/10-REVIEW.md
iteration: 1
findings_in_scope: 3
fixed: 3
skipped: 0
status: all_fixed
---

# Phase 10: Code Review Fix Report

**Fixed at:** 2026-08-25T20:45:00Z
**Source review:** .planning/phases/10-streak-tracking/10-REVIEW.md
**Iteration:** 1

**Summary:**
- Findings in scope: 3
- Fixed: 3
- Skipped: 0

## Fixed Issues

### CR-01: computeStreak() incorrectly inflates current streak when older longer streaks exist

**Files modified:** `index.html`
**Commit:** `79e300b`
**Applied fix:** Added `break` statement in the gap detection branch (lines 2985-2992). When a gap is found in consecutive dates going backward from today, the loop now terminates immediately. This ensures that `current` only reflects the streak actively maintained from today/yesterday, not historical streaks in older history.

**Change detail:**
- Removed lines that attempted to start a new streak from an older date (`streak = 1` and `current = Math.max(current, 1)`)
- Added `break` after updating `longest` to exit the loop when a gap is detected
- This fixes the bug where older streaks would inflate the current streak value

### WR-02: Invalid date strings in history entries are not validated in computeStreak()

**Files modified:** `index.html`
**Commit:** `2935bf0`
**Applied fix:** Added date validation at lines 2948-2953 before using date objects. The function now checks `if (dateObj instanceof Date && !isNaN(dateObj))` before calling `.toLocaleDateString()`, preventing Invalid Date objects from corrupting the streak calculation when history entries contain malformed date strings.

**Change detail:**
- Extracted `new Date(entry.date)` into a named variable `dateObj`
- Added a validity check before processing the date
- Only valid dates are added to the `dates` Set, ensuring robustness against corrupted localStorage data

### WR-01: Missing focus management when opening streak panel

**Files modified:** `index.html`
**Commit:** `e030f1e`
**Applied fix:** Added a visible close button (×) to the streak panel HTML (line 1383) and implemented focus management in the JavaScript (lines 3465, 3471, 3481). The close button matches the design pattern used in the info and settings panels, and `openStreak()` now sets focus to the close button, improving keyboard navigation and WCAG 2.1 SC 2.4.3 compliance.

**Change detail:**
- Added `<button class="infoCloseBtn" id="streakCloseBtn" title="Close streak" aria-label="Close streak">×</button>` after the h2 title
- Added `const streakCloseBtn = document.getElementById("streakCloseBtn");` to cache the element
- Updated `openStreak()` to call `streakCloseBtn.focus()` after opening the panel
- Added event listener `streakCloseBtn.addEventListener("click", closeStreak);` for the close button

---

_Fixed: 2026-08-25_
_Fixer: Claude (gsd-code-fixer)_
_Iteration: 1_
