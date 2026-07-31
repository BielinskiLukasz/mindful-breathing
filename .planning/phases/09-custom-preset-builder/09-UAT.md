---
status: testing
phase: 09-custom-preset-builder
source: [09-01-SUMMARY.md, 09-02-SUMMARY.md, 09-03-SUMMARY.md]
started: 2026-08-01T00:00:00Z
updated: 2026-08-01T00:00:00Z
---

## Current Test

number: 1
name: Builder dialog opens when '+' button clicked
expected: |
  Click the '+' button next to the built-in preset buttons (Relax / Box / 4-7-8). A modal dialog appears with a preset name input, checkboxes for Inhale / Hold / Exhale / Hold2 (all checked by default), duration inputs for each phase, and Cancel / Save Preset buttons.
awaiting: user response

## Tests

### 1. Builder dialog opens when '+' button clicked
expected: Click the '+' button next to the built-in preset buttons (Relax / Box / 4-7-8). A modal dialog appears with a preset name input, checkboxes for Inhale / Hold / Exhale / Hold2 (all checked by default), duration inputs for each phase, and Cancel / Save Preset buttons.
result: [pending]

### 2. Form validation rejects empty preset name
expected: With the builder dialog open, clear the preset name field and click "Save Preset". The dialog stays open and shows an error message (e.g. "Name required") near the name field. The preset is NOT saved.
result: [pending]

### 3. Form validation rejects fewer than 2 active phases
expected: Uncheck all phase checkboxes except one (e.g. only Inhale checked), then click "Save Preset". The dialog stays open and shows an error message (e.g. "At least 2 phases required"). The preset is NOT saved.
result: [pending]

### 4. Custom preset button appears in selector row after save
expected: Enter a name (e.g. "Morning Flow"), keep at least 2 phases checked, click "Save Preset". The dialog closes and a new button labelled "Morning Flow" appears in the preset row next to the built-in presets. It is immediately shown as active (highlighted).
result: [pending]

### 5. Custom preset persists across page reload
expected: After creating "Morning Flow" (Test 4), reload the page. The "Morning Flow" button is still present in the preset row and is selectable.
result: [pending]

### 6. Selected custom preset hides duration inputs
expected: Click "Morning Flow" (or any custom preset) to select it. The per-phase duration sliders/inputs disappear — the duration controls section is hidden. The session resets to the custom preset's phases.
result: [pending]

### 7. Built-in preset selection restores duration inputs
expected: With a custom preset selected (duration inputs hidden), click any built-in preset (e.g. "Relax"). The per-phase duration sliders/inputs reappear.
result: [pending]

### 8. Save button is never proactively disabled
expected: Open the builder dialog. The "Save Preset" button is always clickable — it is never greyed-out or disabled before you attempt to submit. Validation errors only appear after clicking Save.
result: [pending]

### 9. Edit icon appears on custom preset button (desktop)
expected: On a desktop browser, hover the mouse over a custom preset button. A small edit icon (✎) appears on or near the button while hovering.
result: [pending]

### 10. Edit icon click opens pre-filled edit dialog
expected: Click the edit icon (✎) on a custom preset button. The builder dialog opens in edit mode: the preset name is pre-filled, phase checkboxes reflect the saved state, duration inputs show saved values, and a "Delete" button is visible (not shown in create mode).
result: [pending]

### 11. Edit dialog pre-fills form with all current preset data
expected: Open the edit dialog for "Morning Flow". Verify the name field shows "Morning Flow", checked phases match what was saved, and durations match saved values. No fields are blank or reset to defaults.
result: [pending]

### 12. Editing a preset updates its button and active phases
expected: Open edit dialog for "Morning Flow", change the name to "Evening Flow" and toggle a phase off. Click "Save Preset". The button text updates to "Evening Flow" and the active phases reflect the change immediately. The update persists after a page reload.
result: [pending]

### 13. Editing active preset rebuilds phases immediately
expected: Select "Evening Flow" (making it active), then edit it (change a duration or toggle a phase). Click "Save Preset". Without selecting a different preset, the breathing session reflects the updated phase sequence/durations — the ring animation and phase labels update immediately.
result: [pending]

### 14. Delete button requires two clicks (two-step confirmation)
expected: In the edit dialog, click the "Delete" button. The button label changes to "Confirm Delete" and the button turns red. The preset is NOT deleted yet. Click "Confirm Delete" (second click) → the preset is removed from the selector, the dialog closes.
result: [pending]

### 15. Delete confirmation resets automatically after 2 seconds
expected: Click "Delete" in the edit dialog (button turns red / "Confirm Delete"). Wait 2+ seconds without clicking again. The button automatically reverts to "Delete" with its original styling. Clicking "Delete" again starts the two-step process fresh.
result: [pending]

### 16. Deleting a non-active preset does not affect the session
expected: Create two custom presets ("Alpha" and "Beta"). Select "Alpha" (active). Open the edit dialog for "Beta" and complete the two-step deletion. "Beta" disappears from the selector, but "Alpha" remains active and the current session is unaffected.
result: [pending]

### 17. Deleting the active preset falls back to Relax
expected: Select a custom preset (e.g. "Alpha"). Open its edit dialog and complete the two-step deletion. The preset is removed, the app falls back to "Relax" preset, the session resets (ring returns to start, duration inputs reappear), and the "Relax" button appears active.
result: [pending]

### 18. Long-press on custom preset opens edit dialog (mobile / touch)
expected: On a touch device (or simulated touch), tap and hold a custom preset button for ~300ms. The edit dialog opens pre-filled. (Alternatively verify via browser DevTools touch simulation.)
result: [pending]

### 19. Custom preset selection persists on page reload (CR-02 fix)
expected: Select a custom preset (e.g. "Morning Flow") so it is the active preset. Reload the page. "Morning Flow" is still the active preset — it should be highlighted/selected, not defaulted back to a built-in preset.
result: [pending]

### 20. Form mode detection correct in edit vs create (WR-01 fix)
expected: Open the builder dialog via '+' (create mode) — no Delete button visible. Close it. Open edit dialog via edit icon — Delete button IS visible. Both modes save correctly without mixing up create/edit logic.
result: [pending]

### 21. Phase type lookup works for all phase types (WR-04 fix)
expected: Create a custom preset with all 4 phase types active (Inhale, Hold, Exhale, Hold2). Select it and start a session. All 4 phases cycle correctly with their labels and ring animation — no phase is skipped or shows incorrect behavior.
result: [pending]

### 22. Long-press does not accidentally select preset (WR-03 fix)
expected: Long-press a custom preset button on a touch screen (or DevTools touch simulation). Only the edit dialog should open — the preset should NOT also get selected/activated as if it was a normal tap. The currently-active preset remains unchanged.
result: [pending]

### 23. Full lifecycle: create → select → run session → edit → delete
expected: Create a custom preset "Test Full". Select it. Start a breathing session and let it run for one cycle. Stop. Edit the preset (change duration). Restart the session — it uses the updated duration. Delete the preset via two-step confirmation. App falls back to Relax. All changes persisted correctly throughout.
result: [pending]

<!-- Auto-passed from 09-01-SUMMARY.md (all items had automated_ui verification with status: pass) -->

### 24. CUSTOM_PRESETS_KEY constant added to CONFIG section
expected: Code-level: const CUSTOM_PRESETS_KEY = 'mb_custom_presets' present in CONFIG section
result: pass
source: automated
coverage_id: D1

### 25. customPresets mutable state variable initialized in STATE section
expected: Code-level: let customPresets = [] present in STATE section
result: pass
source: automated
coverage_id: D2

### 26. loadCustomPresets() function with try/catch and Array.isArray validation
expected: Code-level: loadCustomPresets() uses localStorage.getItem(CUSTOM_PRESETS_KEY) with try/catch and Array.isArray guard
result: pass
source: automated
coverage_id: D3

### 27. saveCustomPresets() function with try/catch error handling
expected: Code-level: saveCustomPresets() uses localStorage.setItem(CUSTOM_PRESETS_KEY) with try/catch
result: pass
source: automated
coverage_id: D4

### 28. loadCustomPresets() called at init time after loadSettings()
expected: Code-level: loadCustomPresets() is called at app initialization, after loadSettings()
result: pass
source: automated
coverage_id: D5

## Summary

total: 28
passed: 5
issues: 0
pending: 23
skipped: 0
blocked: 0

## Gaps

[none yet]
