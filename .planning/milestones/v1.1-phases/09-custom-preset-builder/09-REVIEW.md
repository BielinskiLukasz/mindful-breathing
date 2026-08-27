---
phase: 09-custom-preset-builder
reviewed: 2026-08-24T12:30:00Z
depth: standard
files_reviewed: 1
files_reviewed_list:
  - index.html
findings:
  critical: 1
  warning: 1
  info: 1
  total: 3
status: issues_found
---

# Phase 09: Code Review Report

**Reviewed:** 2026-08-24
**Depth:** standard
**Files Reviewed:** 1
**Status:** issues_found

## Summary

Reviewed the gap closure changes to the custom preset builder UI for plans 09-04, 09-05, and 09-06. The implementation correctly handles validation message display, dialog centering, and edit icon click prevention. However, a critical bug was identified in preset active state initialization when custom presets are loaded, and a warning-level UX inconsistency exists in error message clearing during form submission.

---

## Critical Issues

### CR-01: Active Preset Class Not Cleared When Custom Preset Loads on Init

**File:** `index.html:2251-2254`

**Issue:** When a custom preset is loaded from localStorage during page initialization, the built-in preset buttons (especially "Relax") retain their default `active` class from the HTML. The `loadSettings()` function only toggles the active class on built-in preset buttons when a built-in preset is loaded, not when a custom preset is loaded.

**Evidence:**
- HTML line 1386: `<button class="presetBtn active" data-preset="relax">Relax</button>` — hardcoded active state
- Lines 2248-2250: Built-in buttons are toggled only inside the `if (data.preset in PRESETS)` block
- Lines 2251-2254: Custom preset branch does NOT toggle the built-in buttons:
  ```javascript
  } else if (customPresets.some(p => p.id === data.preset)) {
    activePresetKey = data.preset;
    // ← No active class toggling here
  }
  ```
- Line 3365: `renderCustomPresets()` is called after `loadSettings()`, which re-renders custom presets and adds active class to the matching button, but never removes the active class from the relax button

**Impact:** Users who have a custom preset as their last active preset will see both the custom preset button AND the Relax button with the `active` class styling when the page loads. This creates visual confusion about which preset is actually active. The active state becomes correct only after the user interacts with a preset button (because the click handlers at lines 2982 and 3005 properly toggle all buttons).

**Fix:**
```javascript
// Lines 2251-2254, add active class toggling for custom presets:
} else if (customPresets.some(p => p.id === data.preset)) {
  // Custom preset exists - set key, buildActivePhases() will handle it
  activePresetKey = data.preset;
  // Clear active class from built-in presets when loading a custom preset
  presetsEl.querySelectorAll(".presetBtn").forEach(b =>
    b.classList.toggle("active", false)
  );
}
```

Alternatively, always toggle built-in buttons to ensure consistency:
```javascript
if (data.preset) {
  if (data.preset in PRESETS) {
    activePresetKey = data.preset;
    // ... existing logic ...
  } else if (customPresets.some(p => p.id === data.preset)) {
    activePresetKey = data.preset;
  }
  // Always update built-in preset buttons (moved outside the if/else)
  presetsEl.querySelectorAll(".presetBtn").forEach(b =>
    b.classList.toggle("active", b.dataset.preset === activePresetKey)
  );
}
```

---

## Warnings

### WR-01: phaseCountError Not Cleared on Form Submit

**File:** `index.html:2078-2080`

**Issue:** The form submit handler clears `presetNameError` at the start of submission (line 2080), but does not clear `phaseCountError`. The `phaseCountError` message is set to auto-clear after 3000ms (line 2099), but if the user fails validation, quickly fixes the input, and resubmits before 3 seconds have passed, the old error message will still be visible during the new validation attempt.

**Evidence:**
- Line 2080: Only `presetNameError.style.display = "none"` is cleared on submit
- Line 2099: `phaseCountError` has a 3-second auto-clear timeout
- Lines 1953-1954 (openEditDialog): Both errors ARE cleared when opening edit dialog:
  ```javascript
  presetNameError.style.display = "none";
  phaseCountError.style.display = "none";
  ```
- Lines 2051-2052 (newPresetBtn): Both errors ARE cleared when opening new preset form

**Impact:** Inconsistent error message handling creates a poor UX. The presetNameError behavior (stays visible until next submit) differs from phaseCountError (auto-clears after 3 sec). If a user encounters a phase count validation error and quickly corrects it, the old error message persists, making the form appear broken or confusing.

**Fix:**
```javascript
presetBuilderForm.addEventListener("submit", e => {
  e.preventDefault();
  presetNameError.style.display = "none";
  phaseCountError.style.display = "none";  // Add this line
  
  const name = presetNameInput.value.trim();
  const rows = Array.from(phaseRowsContainer.querySelectorAll(".phaseRow"));
  const activePhaseTypes = rows.filter(row => row.querySelector("input[type='checkbox']").checked);
  // ... rest of validation
});
```

---

## Info

### IN-01: Inconsistent Error Message Clearing Strategy

**File:** `index.html:2078-2099`

**Issue:** Two different error clearing strategies are used in the form:
- `presetNameError`: Set to display:block on validation failure, manually cleared only on next submit
- `phaseCountError`: Set to display:block on validation failure, automatically cleared after 3000ms timeout

This inconsistency could confuse developers maintaining the code and may lead to similar bugs in future error handling. The gap closure plan 09-04 extended the phaseCountError timeout to 3000ms but didn't address whether it should also be cleared at submit start like presetNameError.

**Suggestion:** Define a consistent error clearing pattern. Either:
1. All errors auto-clear after a timeout (simpler for users, errors disappear automatically)
2. All errors stay visible and are manually cleared on next submit (more explicit, requires user to re-engage)

Current approach mixes both, which is harder to predict and maintain.

---

## Reviewed Areas (Gap Closure Plans)

**✓ 09-04 (Validation Message UX):** Mostly correct implementation
- presetNameError is cleared at submit start (line 2080)
- phaseCountError timeout extended to 3000ms (line 2099)
- ⚠️ phaseCountError should also be cleared at submit start (see WR-01)

**✓ 09-05 (Preset Selection UI):** Correctly implemented
- Active class toggling via `data-preset` attribute (lines 2889, 2982, 3005)
- Custom preset name displayed correctly (line 2890)
- CSS container gap set to 8px (line 224)
- ⚠️ Initial active state fails when custom preset loads (see CR-01)

**✓ 09-06 (Edit Icon Click):** Correctly implemented
- Edit icon click handler uses `e.stopPropagation()` to prevent button selection (line 1941)
- Dialog positioned with fixed + transform (lines 244-247)
- No `pointerEvents: none` blocking interactions
- Click handler prevents default and stops propagation properly (lines 1940-1941)

---

_Reviewed: 2026-08-24_
_Reviewer: Claude (gsd-code-reviewer)_
_Depth: standard_
