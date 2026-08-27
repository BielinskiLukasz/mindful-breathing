---
status: resolved
trigger: "Focus trap bug: Tab escapes panel when focus is on input/textarea elements"
created: 2026-07-29T00:00:00Z
updated: 2026-07-29T00:00:00Z
resolved_by: "08-03-PLAN.md — reordered keydown handler if-blocks so focus trap executes before INPUT/TEXTAREA early return"
---

## Current Focus

**hypothesis:** The keyboard handler has an early return check (line 2722) that bypasses the focus trap logic entirely when the active element is an INPUT or TEXTAREA. Since the Settings panel contains input fields (duration inputs, goal input, etc.), pressing Tab while focused on these inputs skips the focus trap, allowing Tab to escape the panel.

**confirming_evidence:**
- Line 2722: `if (e.target.tagName === "INPUT" || e.target.tagName === "TEXTAREA") return;` exits BEFORE the focus trap logic
- Lines 2726-2740: Focus trap logic never executes for inputs because of the early return
- Settings panel contains multiple inputs (duration sliders, goal input) where Tab will fail to be trapped

**root_cause:** Early return check (line 2722) placed BEFORE focus trap logic (lines 2726-2740)

## Symptoms

**expected:** When Info or Settings panel is open, Tab key cycles focus only within focusable elements in that panel. Focus wraps from last element back to first.

**actual:** When Info or Settings panel is open and focus is on any INPUT or TEXTAREA element, pressing Tab moves focus outside the panel to browser chrome and background elements.

**reproduction:**
1. Open Settings panel (click gear icon)
2. Click on one of the duration input fields OR the "Cycles per session" input
3. Press Tab
4. Focus escapes to elements outside the panel (browser controls, Start button, etc.)

## Evidence

- **Line 2722:** Early return check exits the handler before focus trap logic: `if (e.target.tagName === "INPUT" || e.target.tagName === "TEXTAREA") return;`
- **Lines 2726-2740:** Focus trap logic that should intercept Tab and cycle within panel
- **Lines 2727-2729:** Selector correctly includes inputs: `'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'`
- **Settings panel HTML:** Contains `<input>` elements (goalInput, durInputs) that are dynamically created via `buildDurationInputs()`

## Root Cause

**File:** `C:/my-code/vibe-coding/mindful-breathing/index.html`
**Line:** 2722
**Issue:** The keyboard event listener has a guard clause that returns early if `e.target` is an INPUT or TEXTAREA. This exits BEFORE the focus trap logic (lines 2726-2740), so Tab presses on input elements never reach the focus trap, allowing browser default Tab behavior to escape the panel.

## Minimal Fix

Move the focus trap logic (lines 2726-2740) to execute BEFORE the early return check on line 2722.

**Specific change:**
1. Move the focus trap block (lines 2726-2740) to lines 2724-2742 (right after the metaKey/ctrlKey/altKey check)
2. Keep the INPUT/TEXTAREA early return as-is, OR make it more specific to only return for non-shortcut keys (not Tab)

Or alternatively:
1. Modify line 2722 to skip only when NOT pressing Tab and a panel is NOT open: `if ((e.target.tagName === "INPUT" || e.target.tagName === "TEXTAREA") && (e.code !== "Tab" || !openPanelElement)) return;`

## Secondary Issues (Optional)

The focus trap logic (lines 2733-2739) only prevents default when wrapping around (first→last or last→first). For intermediate elements, Tab is not intercepted. While this may work in most cases, a complete fix would always prevent Tab when a panel is open and manually handle all Tab navigation within the panel.

## Artifacts

**File:** `index.html`
**Issue:** Focus trap bypass when pressing Tab in input fields  
**Lines:** 2722 (early return), 2726-2740 (focus trap logic)
