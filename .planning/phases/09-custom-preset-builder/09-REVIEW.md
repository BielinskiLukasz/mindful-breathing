---
phase: 09-custom-preset-builder
reviewed: 2026-07-31T00:00:00Z
depth: standard
files_reviewed: 1
files_reviewed_list:
  - index.html
findings:
  critical: 2
  warning: 4
  info: 2
  total: 8
status: issues_found
---

# Phase 09: Code Review Report

**Reviewed:** 2026-07-31
**Depth:** standard
**Files Reviewed:** 1
**Status:** issues_found

## Summary

The custom preset builder adds significant functionality but contains critical bugs that cause crashes, data loss, and fragile code patterns. The implementation lacks proper null checking, has initialization order issues, and relies on fragile DOM state detection for control flow.

Key findings:
1. **Crash when loading custom presets with missing phase types** (CR-01)
2. **Custom presets not restored after page reload due to initialization order** (CR-02)
3. **Fragile form mode detection via DOM style property** (WR-01)
4. **Missing null check on phase lookup creates undefined references** (WR-02)

These issues must be resolved before shipping.

---

## Critical Issues

### CR-01: Undefined Phase Crash in buildActivePhases()

**File:** `index.html:1747-1758`

**Issue:** When loading a custom preset, the code looks up phase types in PRESETS.relax without null checking:

```javascript
const basePhase = PRESETS.relax.find(bp => bp.name === p.type);
return {
  name: p.type,
  durationSec: p.durationSec,
  breathR: basePhase.breathR,        // CRASH if basePhase undefined
  theme: basePhase.theme,
  cue: basePhase.cue,
  hint: basePhase.hint
};
```

If the phase type `p.type` doesn't exist in PRESETS.relax (e.g., "Hold2", which is defined in PRESET_PHASE_TYPES at line 1992 but NOT in PRESETS), `basePhase` will be `undefined`. Accessing `.breathR` on undefined throws `TypeError: Cannot read property 'breathR' of undefined`, crashing the entire app.

**Crash Scenario:**
1. User creates custom preset with "Hold2" phase selected (line 1992 defines this type)
2. User selects the custom preset
3. `buildActivePhases()` searches PRESETS.relax for phase named "Hold2"
4. Not found (PRESETS.relax only has "Inhale", "Hold", "Exhale", "Hold")
5. `basePhase` is undefined
6. Accessing `basePhase.breathR` crashes the app

**Fix:**

```javascript
const basePhase = PRESETS.relax.find(bp => {
  // "Hold2" should map to the second "Hold" phase
  const searchName = p.type === "Hold2" ? "Hold" : p.type;
  return bp.name === searchName;
});

if (!basePhase) {
  // Fallback to first phase in relax preset to gracefully handle mismatches
  const fallbackPhase = PRESETS.relax[0];
  return {
    name: p.type,
    durationSec: p.durationSec,
    breathR: fallbackPhase.breathR,
    theme: fallbackPhase.theme,
    cue: fallbackPhase.cue,
    hint: fallbackPhase.hint
  };
}

return {
  name: p.type,
  durationSec: p.durationSec,
  breathR: basePhase.breathR,
  theme: basePhase.theme,
  cue: basePhase.cue,
  hint: basePhase.hint
};
```

---

### CR-02: Custom Preset Selection Lost on Page Reload

**File:** `index.html:2195-2225, 3329-3331`

**Issue:** Custom presets are never restored from localStorage because of initialization order.

The init sequence (lines 3329-3331):
```javascript
loadSettings();        // ← Called FIRST
loadCustomPresets();   // ← Called SECOND
renderCustomPresets();
```

In `loadSettings()` (line 2209):
```javascript
if (data.preset && data.preset in PRESETS) {
  activePresetKey = data.preset;  // Only restores built-in presets
  // ...
}
```

When a user saves a custom preset (e.g., `"custom-1624123456"`) and reloads the page:

1. `loadSettings()` runs first, before custom presets are loaded
2. Line 2209 checks: `"custom-1624123456" in PRESETS` → **false**
3. The condition fails, so `activePresetKey` remains `"relax"` (initial value at line 1631)
4. `loadCustomPresets()` runs second, loading the custom preset into memory
5. But it's too late—`activePresetKey` is already set to "relax"
6. The custom preset exists in localStorage but is never selected

**Result:** Users lose their custom preset selection across page reloads, appearing as data loss.

**Fix:** Swap initialization order and update logic:

```javascript
// ====== Init (no cue on load) ======
loadCustomPresets();   // Load FIRST
loadSettings();        // Then restore settings (can now check customPresets)
renderCustomPresets();
// ... rest
```

Then update `loadSettings()` to check custom presets:

```javascript
if (data.preset) {
  if (data.preset in PRESETS) {
    // Built-in preset
    activePresetKey = data.preset;
    activePhases = PRESETS[activePresetKey].map(p => ({...p}));
    const saved = savedDurations[data.preset];
    if (Array.isArray(saved)) {
      saved.forEach((sec, i) => {
        if (i < activePhases.length && Number.isFinite(sec) && sec >= DURATION_RANGE.min && sec <= DURATION_RANGE.max) {
          activePhases[i] = { ...activePhases[i], durationSec: sec };
        }
      });
    }
  } else if (customPresets.some(p => p.id === data.preset)) {
    // Custom preset exists - set key, buildActivePhases() will handle it
    activePresetKey = data.preset;
  }
  // If preset doesn't exist, activePresetKey stays "relax" (safe fallback)
}
```

---

## Warnings

### WR-01: Fragile Form Mode Detection via DOM Style

**File:** `index.html:2076-2078`

**Issue:** Edit vs. create mode is detected by checking if the delete button's inline style is not "none":

```javascript
const presetDeleteBtn = document.getElementById("presetDeleteBtn");
const isEditMode = presetDeleteBtn.style.display !== "none";
```

This is fragile because:
1. The delete button's display state is an implementation detail that could change
2. The condition relies on inline styles, which may not reflect the element's actual display state (affected by CSS, parent rules, media queries)
3. Very difficult to test and maintain
4. Couples form logic to UI implementation details

**Better approach:** Store mode explicitly when opening the dialog:

```javascript
function openEditDialog(preset) {
  presetNameInput.value = preset.name;
  // ... pre-fill logic ...
  presetBuilderDialog._isEditMode = true;
  presetBuilderDialog._editingPresetId = preset.id;
  presetBuilderDialog.showModal();
}

newPresetBtn.addEventListener("click", () => {
  presetNameInput.value = "";
  // ... init form ...
  presetBuilderDialog._isEditMode = false;
  presetBuilderDialog._editingPresetId = null;
  presetBuilderDialog.showModal();
});

presetBuilderForm.addEventListener("submit", e => {
  e.preventDefault();
  // ... validation ...
  
  if (presetBuilderDialog._isEditMode) {
    const presetId = presetBuilderDialog._editingPresetId;
    // ... update logic ...
  } else {
    // ... create logic ...
  }
});
```

---

### WR-02: Missing Null Check on basePhase Access

**File:** `index.html:1749-1756`

**Issue:** Related to CR-01. The phase lookup doesn't validate success before accessing properties. This violates the project's error handling principle: "all optional APIs fail silently with try/catch or capability checks. No throw anywhere."

A missing phase type should degrade gracefully, not crash.

The code assumes the phase type will always be found in PRESETS.relax without checking for null/undefined. When the assumption fails, the app crashes instead of falling back to a safe default.

See CR-01 for the complete fix.

---

### WR-03: Long-Press Event Handler with Passive Listeners

**File:** `index.html:1880-1903`

**Issue:** The long-press handler registers with `{ passive: true }`, which prevents `preventDefault()` from working, but the code inside the timeout tries to call it:

```javascript
btn.addEventListener("touchstart", e => {
  longPressTimer = setTimeout(() => {
    e.preventDefault();  // Won't work because listener is passive
    btn.style.transform = "scale(0.97)";
    openEditDialog(preset);
  }, 300);
}, { passive: true });
```

The `e.preventDefault()` call is ineffective because:
1. The listener is passive (can't prevent default)
2. The call happens inside a setTimeout, outside the event handler scope

While the preset edit functionality still works (because openEditDialog doesn't depend on preventDefault), this indicates confused intent and violates the principle of clarity.

**Fix:** Remove ineffective preventDefault and simplify:

```javascript
let longPressTimer = null;
btn.addEventListener("touchstart", e => {
  longPressTimer = setTimeout(() => {
    openEditDialog(preset);
  }, 300);
}, { passive: true });

btn.addEventListener("touchend", () => {
  if (longPressTimer) {
    clearTimeout(longPressTimer);
    longPressTimer = null;
    btn.style.transform = "";
  }
}, { passive: true });
```

---

### WR-04: Phase Type Schema Mismatch Between UI and PRESETS

**File:** `index.html:1992, 1527-1532`

**Issue:** PRESET_PHASE_TYPES (line 1992) defines four phase types:

```javascript
const PRESET_PHASE_TYPES = ["Inhale", "Hold", "Exhale", "Hold2"];
```

But PRESETS.relax (lines 1527-1532) only has three unique phase names:

```javascript
{ name: "Inhale", durationSec: 4, ... },
{ name: "Hold",   durationSec: 2, ... },
{ name: "Exhale", durationSec: 8, ... },
{ name: "Hold",   durationSec: 2, ... }  // Second "Hold", not "Hold2"
```

The "Hold2" type exists in the UI but not in the template data. Users can select "Hold2" in the form, and it will be stored in custom presets, but when loading, the lookup fails (see CR-01).

**Fix:** Either rename the second Hold phase in PRESETS to "Hold2", or update PRESET_PHASE_TYPES to use the correct name. The cleaner approach is to align PRESET_PHASE_TYPES with actual phase names:

```javascript
const PRESET_PHASE_TYPES = ["Inhale", "Hold", "Exhale", "Hold"];  // Match PRESETS structure
```

Then, in the form rendering and phase management, distinguish between the two "Hold" phases by index, not name.

---

## Info

### IN-01: Edit Icon Style Overrides CSS Unnecessarily

**File:** `index.html:1869-1877`

**Issue:** The edit icon is created with inline style properties:

```javascript
editIcon.style.marginLeft = "4px";
editIcon.style.opacity = "0";
editIcon.style.transition = "opacity 80ms ease";
editIcon.style.pointerEvents = "none";
```

CSS already defines some of these (lines 198-206):

```css
.editIcon {
  opacity: 0;
  transition: opacity 80ms ease;
  /* marginLeft and pointerEvents not in CSS */
}
```

Mixing inline styles with CSS makes maintenance harder and violates DRY. The styles should be consolidated in the stylesheet.

**Fix:** Move all styles to CSS:

```css
.editIcon {
  opacity: 0;
  transition: opacity 80ms ease;
  font-size: 0.9em;
  display: inline-block;
  position: relative;
  top: 1px;
  cursor: pointer;
  margin-left: 4px;
  pointer-events: none;
}
```

Remove inline style assignments from the JavaScript.

---

### IN-02: Button State Properties (_deleteConfirmed, _currentPresetId)

**File:** `index.html:1974-1975, 2082, 2132`

**Issue:** The delete button stores state using private properties on the DOM element:

```javascript
deleteBtn._deleteConfirmed = false;
deleteBtn._currentPresetId = preset.id;
```

While functional in JavaScript, storing application state on DOM elements is non-standard and makes the code harder to understand. It couples state management to the UI.

This is not a bug (JavaScript allows this), but it's a code smell that indicates the state should be managed separately.

**Better approach:** Use a separate state object or pass the preset ID as an argument to the callback.

---

_Reviewed: 2026-07-31_
_Reviewer: Claude (gsd-code-reviewer)_
_Depth: standard_
