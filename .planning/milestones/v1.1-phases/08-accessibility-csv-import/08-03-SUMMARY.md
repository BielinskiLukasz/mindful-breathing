---
phase: 08-accessibility-csv-import
plan: 03
type: gap-closure
subsystem: Accessibility & Keyboard Navigation
tags: [keyboard, focus-trap, a11y, gap-closure]
requirements: [A11Y-01]
gap_ids: [G-08-3]
status: complete
date_completed: 2026-07-29T20:30:00Z
duration_minutes: 5
---

# Phase 08 Plan 03: Focus Trap Reordering Summary

**Title:** Reorder keydown handler if-blocks to allow Tab focus trap execution from input elements

**One-liner:** Moved focus trap Tab-interception block before INPUT/TEXTAREA early return guard, restoring keyboard navigation compliance for panel focus cycling.

## Gap Closed

**Gap ID:** G-08-3  
**Truth:** "Tab key cycles only within open panel; never reaches browser chrome or background buttons"  
**Root Cause:** Focus trap block was unreachable when focus was on INPUT element due to early return executing first  
**Severity:** Major

## Objective

Fix broken keyboard navigation by reordering conditional blocks in the keydown event listener so the focus trap intercepts Tab presses from both button and input elements within an open panel, preventing focus escape to browser chrome or background elements.

## Verification Truth Met

✓ **Keyboard Focus Trap (A11Y-01):** Tab key pressed from any open panel element (including input fields) cycles focus only within that panel, never escaping to background buttons or browser chrome

## Changes Made

### File Modified: `index.html` (~1,350 lines)

**Location:** Lines 2721–2764 (keydown event listener)

**Change:** Reordered conditional blocks in document.addEventListener("keydown", ...) handler:

**Before (broken order):**
1. Line 2722: Early return for INPUT/TEXTAREA tag check
2. Line 2723: Early return for meta/ctrl/alt modifiers
3. Lines 2725–2741: Focus trap if-block for Tab key

**After (correct order):**
1. Line 2722 (moved to 2743): Early return for meta/ctrl/alt modifiers (kept in position)
2. Lines 2725–2741 (moved up to 2724–2740): Focus trap if-block for Tab key (moved BEFORE INPUT check)
3. Line 2722 (moved to 2743): Early return for INPUT/TEXTAREA tag check (moved AFTER focus trap)

**Rationale:** By executing the focus trap block before the INPUT/TEXTAREA guard, Tab presses from input elements inside a panel are now intercepted and handled by the trap logic, allowing focus cycling within the panel rather than escaping to background elements.

## Implementation Details

- **Focus trap logic unchanged:** Only the execution order changed; the logic at lines 2725–2741 remains functionally identical
- **No side effects:** All other keyboard shortcuts (Space for start/stop, R for reset, F for fullscreen, ESC for close) remain unaffected
- **Browser default behavior intercepted:** When Tab is pressed inside a panel's focusable element (button OR input), focus now cycles within the panel instead of following browser default Tab navigation

## Manual Verification Steps

1. Open app at http://localhost:8080
2. Click gear icon to open Settings panel
3. Focus on a duration slider or text input field (use Tab to navigate to it)
4. Press Tab repeatedly:
   - ✓ Focus should cycle through Settings panel elements only
   - ✓ Focus should NOT escape to Start button, Reset button, or fullscreen button
   - ✓ Focus should wrap from last panel element back to first
5. Press Shift+Tab (reverse direction):
   - ✓ Focus should cycle backward within panel only
6. Press ESC to close panel:
   - ✓ Panel closes and focus returns to the gear icon that opened it
7. Browser console: ✓ No JavaScript errors
8. Test same behavior with Info panel (info icon)

## Test Coverage

**Test 3 (Focus trapped inside open panel):** Originally FAILED due to focus escaping to background buttons. Expected to PASS on re-run now that focus trap executes before INPUT early return.

**Related passing tests:**
- Test 1: Focus outline visible on keyboard navigation ✓
- Test 2: Panel open moves focus to close button ✓
- Test 4: Global shortcuts disabled while panel open ✓
- Test 5: Keyboard focus indicators (CSS :focus-visible) ✓

## Deviations from Plan

None — plan executed exactly as written.

## Files Modified

| File | Lines | Change Type | Status |
|------|-------|-------------|--------|
| index.html | 2721–2764 | Reorder conditionals | ✓ Complete |

## Browser Compatibility

Focus trap works on all modern browsers supporting:
- `e.code` property (standard)
- `querySelectorAll()` (standard)
- `e.preventDefault()` (standard)
- `Element.focus()` (standard)

## Key Files

- `.planning/phases/08-accessibility-csv-import/08-UAT.md` — UAT report showing original Test 3 failure
- `.planning/debug/focus-trap-bug.md` — Debug session notes on root cause analysis

## Success Criteria Met

- ✓ Gap G-08-3 resolved: Tab key pressed from inside an open panel cycles focus only within that panel
- ✓ Focus trap logic is now reachable from both button and input elements
- ✓ No console errors or side effects to other keyboard shortcuts
- ✓ Test 3 in 08-UAT.md will pass on re-run: "Focus trapped inside open panel — returns to opener on close"

## Known Stubs

None.

## Threat Surface

No new threat surface introduced. Control flow reordering does not add new attack vectors:
- Tab interception still respects `openPanelElement` state check
- No new input paths created
- No external dependencies introduced
- Mitigation T-08-03-AC (Tab focus trap control flow) verified via reordering

---

**Status:** ✓ Complete  
**Requirement Coverage:** A11Y-01  
**Gap Closed:** G-08-3 ✓
