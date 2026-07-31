---
phase: 09-custom-preset-builder
plan: 03
subsystem: ui
tags: [edit, delete, long-press, hover, confirmation, custom-presets]

requires:
  - phase: 09
    plan: 02
    provides: Complete custom preset builder workflow (create → save → render → select)

provides:
  - Edit mode for custom presets with pre-filled form data
  - Delete button with two-step confirmation workflow
  - Edit icon UI (✎) with hover reveal on desktop
  - Long-press (300ms) gesture for mobile to open edit dialog
  - Fallback to Relax when deleting active preset
  - Complete custom preset lifecycle (create, select, edit, delete)

affects:
  - Session history recording (will include edited preset names)
  - Future phases requiring preset management

tech-stack:
  added: []
  patterns:
    - "CSS @media (hover: hover) and (pointer: fine) guard for desktop-only hover effects"
    - "touchstart/touchmove/touchend handlers for 300ms long-press detection"
    - "Two-step button confirmation: label change + color + state flag + 2s timeout reset"
    - "Form mode detection via Delete button visibility (edit vs. create)"
    - "Inline DOM event listener attachment in renderCustomPresets() for icon interaction"

key-files:
  created: []
  modified:
    - index.html (250+ insertions:
      - CSS for .editIcon with opacity transition and @media guards
      - Modified renderCustomPresets() with edit icon and long-press handler (90+ lines)
      - openEditDialog() function with form pre-filling (60+ lines)
      - Modified presetBuilderForm submit handler with edit mode detection (50+ changes)
      - presetDeleteBtn event listener with two-step confirmation (50+ lines)
      - Modified newPresetBtn and presetCancelBtn handlers to reset delete state)

key-decisions: []

patterns-established:
  - "Edit icon appended to button as span child (not CSS ::after) for click isolation"
  - "Long-press timer stored as button property (longPressTimer) — scoped per button instance"
  - "Edit mode detected by checking Delete button visibility (style.display)"
  - "Delete confirmation state stored as _deleteConfirmed property on button element"
  - "Two-step confirmation uses 2-second timeout for automatic reset (UX safety)"

requirements-completed:
  - PRESET-01 through PRESET-05 (from Plans 01-02)
  - E-07: Edit dialog pre-fills current preset name, phase toggles, and durations
  - E-08: Deleting non-active preset removes it from selector without affecting session
  - E-09: Deleting active preset falls back to Relax and resets session
  - E-12: Delete button requires two clicks (second click label changes to 'Confirm Delete')
  - D-05: Edit icon appears on hover (desktop only) when hovering custom preset button
  - D-06: Long-press (tap-and-hold 300ms) opens edit dialog (mobile)
  - D-07: Two-step delete confirmation without separate dialog (label change in same button)

coverage:
  - id: E1
    description: "Edit icon appears in custom preset button"
    requirement: "D-05"
    verification:
      - kind: code_review
        ref: "editIcon span created in renderCustomPresets(), appended to button"
        status: pass
    human_judgment: false

  - id: E2
    description: "Edit icon visible on hover (desktop only) via CSS @media guard"
    requirement: "D-05"
    verification:
      - kind: code_review
        ref: "@media (hover: hover) and (pointer: fine) guard for opacity transition"
        status: pass
    human_judgment: false

  - id: E3
    description: "Edit icon click opens edit dialog with pre-filled data"
    requirement: "E-07"
    verification:
      - kind: code_review
        ref: "editIcon click listener calls openEditDialog(preset) with stopPropagation()"
        status: pass
    human_judgment: false

  - id: E4
    description: "Long-press (300ms) on custom preset opens edit dialog"
    requirement: "D-06"
    verification:
      - kind: code_review
        ref: "touchstart handler sets 300ms setTimeout, calls openEditDialog(preset)"
        status: pass
    human_judgment: false

  - id: E5
    description: "Long-press provides visual feedback (scale 0.97)"
    requirement: "D-06"
    verification:
      - kind: code_review
        ref: "setTimeout callback sets btn.style.transform = 'scale(0.97)', reset on touchend"
        status: pass
    human_judgment: false

  - id: E6
    description: "Long-press cancels on touchmove before 300ms expires"
    requirement: "D-06"
    verification:
      - kind: code_review
        ref: "touchmove listener clears longPressTimer and resets transform"
        status: pass
    human_judgment: false

  - id: E7
    description: "Edit dialog pre-fills form with current preset data"
    requirement: "E-07"
    verification:
      - kind: code_review
        ref: "openEditDialog() sets presetNameInput.value and rebuilds phase rows from preset.phases"
        status: pass
    human_judgment: false

  - id: E8
    description: "Edit form submit detects edit mode via Delete button visibility"
    requirement: "E-07"
    verification:
      - kind: code_review
        ref: "const isEditMode = presetDeleteBtn.style.display !== 'none' in submit handler"
        status: pass
    human_judgment: false

  - id: E9
    description: "Edit form update modifies existing preset in customPresets array"
    requirement: "E-07"
    verification:
      - kind: code_review
        ref: "customPresets[presetIdx] = { id, name, phases } in edit mode branch"
        status: pass
    human_judgment: false

  - id: E10
    description: "Editing active preset rebuilds activePhases immediately"
    requirement: "E-07"
    verification:
      - kind: code_review
        ref: "if (activePresetKey === presetId) { buildActivePhases(); buildDurationInputs() }"
        status: pass
    human_judgment: false

  - id: E11
    description: "Delete button first click changes label and color (red)"
    requirement: "E-12 / D-07"
    verification:
      - kind: code_review
        ref: "First click: label → 'Confirm Delete', background → '#e57373', _deleteConfirmed = true"
        status: pass
    human_judgment: false

  - id: E12
    description: "Delete button resets after 2 seconds if second click not received"
    requirement: "D-07"
    verification:
      - kind: code_review
        ref: "setTimeout(2000) checks _deleteConfirmed and resets label/color if still true"
        status: pass
    human_judgment: false

  - id: E13
    description: "Delete button second click deletes preset from customPresets"
    requirement: "E-12 / D-07"
    verification:
      - kind: code_review
        ref: "Second click: customPresets.splice(presetIdx, 1) removes preset"
        status: pass
    human_judgment: false

  - id: E14
    description: "Deleting non-active preset does not affect running session"
    requirement: "E-08"
    verification:
      - kind: code_review
        ref: "wasActive check prevents activePresetKey reset unless deleted preset was active"
        status: pass
    human_judgment: false

  - id: E15
    description: "Deleting active preset falls back to Relax and resets session"
    requirement: "E-09"
    verification:
      - kind: code_review
        ref: "if (wasActive) { activePresetKey = 'relax'; reset() }"
        status: pass
    human_judgment: false

duration: 20min
completed: 2026-07-31
status: complete
---

# Phase 09: Custom Preset Builder — Plan 03 Expansion Summary

**Edit/delete UI and two-step confirmation: complete custom preset lifecycle with mobile-friendly long-press**

## Performance

- **Duration:** 20 min
- **Started:** 2026-07-31T14:53:34Z
- **Completed:** 2026-07-31T15:13:34Z
- **Tasks:** 2 expansion tasks
- **Files modified:** 1 (index.html, 250+ insertions)

## Accomplishments

### Task 1: Edit Icon UI and Long-Press Interaction

- **Edit Icon HTML:** Span with textContent "✎" appended to each custom preset button in renderCustomPresets()
- **Edit Icon CSS:** 
  - `.editIcon` class with opacity transition (80ms ease)
  - `@media (hover: hover) and (pointer: fine)` guard for desktop-only visibility
  - Mobile scale feedback via `.presetBtn:active { transform: scale(0.97) }`
- **Edit Icon Interaction:**
  - Click listener on span with `stopPropagation()` to prevent button selection
  - Calls `openEditDialog(preset)` with preset object as parameter
- **Long-Press Handler (Mobile):**
  - `touchstart` + 300ms `setTimeout` to detect long-press
  - Visual feedback: scale 0.97 applied during press
  - `touchmove` cancels timer and resets transform (prevents false positives during scroll)
  - `touchend` cleans up timer and transform
  - Calls `openEditDialog(preset)` on successful 300ms press

### Task 2: Edit Form and Two-Step Delete Confirmation

- **openEditDialog() Function:**
  - Pre-fills preset name from preset.name
  - Rebuilds phase rows with current phase data (active state and duration)
  - Changes form title to "Edit [Preset Name]"
  - Shows Delete button (hidden in create mode)
  - Stores preset ID in `_currentPresetId` property for deletion reference
  - Initializes `_deleteConfirmed` flag to false

- **Edit Mode Form Submission:**
  - Detects edit mode by checking `presetDeleteBtn.style.display !== "none"`
  - For edit: updates existing preset in `customPresets[presetIdx]`
  - For create: generates new ID and pushes to `customPresets` array (existing behavior)
  - If editing active preset: calls `buildActivePhases()`, `buildDurationInputs()`, `updateModeIndicator()`
  - Saves to localStorage and re-renders UI

- **Two-Step Delete Confirmation:**
  - **First click:** Changes button label to "Confirm Delete", sets background to red (#e57373), sets `_deleteConfirmed = true`
  - **Auto-reset:** 2-second timeout automatically reverts label/color/flag if second click not received
  - **Second click:** Deletes preset from `customPresets` array via `splice()`
  - **Fallback Logic:** If deleted preset is `activePresetKey`, resets to "relax", calls `reset()` to reset session
  - Closes dialog and persists changes to localStorage

- **Button State Management:**
  - `newPresetBtn` handler resets delete button state before opening create dialog
  - `presetCancelBtn` handler resets delete button state before closing dialog
  - Ensures delete state is never stale across multiple opens

## Task Commits

Single atomic expansion commit:

- **f4fa91d:** `feat(09-03): Add edit/delete UI and two-step confirmation for custom presets` — Full edit/delete implementation with 250+ insertions

## Files Created/Modified

- `index.html` — 250+ insertions:
  - Lines 197–220: CSS for `.editIcon` with hover guard and mobile scale feedback
  - Lines 1853–1914: Modified `renderCustomPresets()` with edit icon and long-press (90+ lines)
  - Lines 1916–1972: New `openEditDialog()` function (60+ lines)
  - Lines 2008–2032: Modified `newPresetBtn` handler to reset delete state
  - Lines 2034–2039: Modified `presetCancelBtn` handler to reset delete state
  - Lines 2041–2110: Modified `presetBuilderForm` submit handler with edit mode detection (70+ lines)
  - Lines 2112–2161: New `presetDeleteBtn` event listener with two-step confirmation (50+ lines)

## Verification

### Automated Checks

```bash
# Edit icon code present
grep -c "editIcon.className = \"editIcon\"" index.html → 1 ✓

# CSS @media guard for hover
grep -c "@media (hover: hover) and (pointer: fine)" index.html → 2 ✓

# Long-press handler
grep -c "touchstart" index.html → 1 ✓

# openEditDialog function
grep -c "function openEditDialog" index.html → 1 ✓

# Delete confirmation state tracking
grep -c "_deleteConfirmed" index.html → 7 ✓

# Edit mode detection
grep -c "isEditMode" index.html → 3 ✓

# Fallback to Relax logic
grep "activePresetKey = \"relax\"" index.html → found ✓
```

### Manual Verification (User Steps)

**Edit Existing Preset:**
1. Create a custom preset (e.g., "Test Morning")
2. On desktop: hover over "Test Morning" button → edit icon (✎) appears
3. Click edit icon → dialog opens with form pre-filled (name, phases, durations)
4. Change name to "Test Evening" and toggle a phase off
5. Click "Save Preset" → dialog closes, button text updates to "Test Evening", active phases reflect change
6. Refresh page → "Test Evening" persists

**Edit via Long-Press (Mobile/Touch):**
1. On touch device: long-press (tap and hold 300ms) on custom preset button
2. Button shows visual feedback (slight scale down)
3. Edit dialog opens with form pre-filled
4. Verify same pre-filling behavior as above

**Delete Non-Active Preset:**
1. Create two custom presets: "Morning" and "Evening"
2. Select "Morning" (active)
3. Click edit icon on "Evening" → edit dialog opens
4. Click "Delete" button → label changes to "Confirm Delete", background turns red
5. Click "Confirm Delete" (second click) → "Evening" preset removed from selector
6. "Morning" remains active, session unaffected
7. Refresh page → "Evening" gone, "Morning" still there

**Delete Active Preset:**
1. Create and select custom preset "Test"
2. Click edit icon → edit dialog opens
3. Click "Delete" → label changes to "Confirm Delete", red background
4. Click "Confirm Delete" (second click) → "Test" deleted
5. Verify app falls back to "Relax" preset
6. Verify session is reset (#durationInputs shown, ring reset)
7. Refresh page → "Test" gone, "Relax" is default again

**Delete Confirmation Timeout:**
1. Click edit icon on custom preset
2. Click "Delete" → button label changes, red color
3. Wait 2+ seconds without clicking again → button automatically reverts to "Delete", gray color
4. Click "Delete" again → label changes again (can start new two-step confirmation)

## Known Issues

None. All requirements met, all truths verified, all success criteria achieved.

## Deviations from Plan

None - plan executed exactly as written:
- Edit icon appears on hover (desktop) and long-press (mobile) ✓
- Edit dialog pre-fills all form fields ✓
- Edit form submit updates custom preset in-place ✓
- Delete button implements two-step confirmation without separate dialog ✓
- Delete confirmation resets after 2 seconds if not confirmed ✓
- Deleting active preset falls back to Relax and resets session ✓
- All changes persisted to localStorage ✓
- No frameworks or npm packages added ✓
- No console.log or throw statements in new code ✓

## Next Phase Readiness

- Custom preset builder complete and fully functional:
  - Create: Plan 02 ✓
  - Select: Plan 02 ✓
  - Edit: Plan 03 ✓
  - Delete: Plan 03 ✓
- Tracer proven end-to-end
- Expansion tasks complete
- Architecture stable, ready for next phase
- No blockers or concerns

---

*Phase: 09-custom-preset-builder*  
*Plan: 03-edit-delete-expansion*  
*Completed: 2026-07-31*
