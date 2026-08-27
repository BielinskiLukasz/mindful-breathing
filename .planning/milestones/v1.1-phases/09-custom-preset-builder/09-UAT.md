---
status: complete
phase: 09-custom-preset-builder
source: [09-01-SUMMARY.md, 09-02-SUMMARY.md, 09-03-SUMMARY.md]
started: 2026-08-27T00:00:00Z
updated: 2026-08-27T12:00:00Z
---

## Current Test

## Current Test

[testing complete]

## Tests

### 1. Builder dialog opens when '+' button clicked
expected: Click the '+' button next to the built-in preset buttons (Relax / Box / 4-7-8). A modal dialog appears with a preset name input, checkboxes for Inhale / Hold / Exhale / Hold2 (all checked by default), duration inputs for each phase, and Cancel / Save Preset buttons.
result: pass

### 2. Form validation rejects empty preset name
expected: With the builder dialog open, clear the preset name field and click "Save Preset". The dialog stays open and shows an error message (e.g. "Name required") near the name field. The preset is NOT saved.
result: pass

### 3. Form validation rejects fewer than 2 active phases
expected: Uncheck all phase checkboxes except one (e.g. only Inhale checked), then click "Save Preset". The dialog stays open and shows an error message (e.g. "At least 2 phases required"). The preset is NOT saved.
result: pass

### 4. Custom preset button appears in selector row after save
expected: Enter a valid name (e.g. "Morning Flow"), keep at least 2 phases checked, click "Save Preset". The dialog closes and a new button labelled "Morning Flow" appears in the preset row. It is immediately shown as active (highlighted / green border). The built-in presets lose their highlight — only "Morning Flow" is highlighted.
result: pass

### 5. Custom preset persists across page reload
expected: After creating "Morning Flow" (Test 4), reload the page. The "Morning Flow" button is still present in the preset row and is selectable.
result: pass
note: "After save, settings panel closes — intentional modification added after this phase"

### 6. Selected custom preset hides duration inputs and section label
expected: Click a custom preset to select it. Both the per-phase duration sliders/inputs AND the "Phase durations (sec)" section label disappear — neither is visible. The session resets to the custom preset's phases.
result: pass

### 7. Built-in preset selection restores duration inputs
expected: With a custom preset selected (duration inputs hidden), click any built-in preset (e.g. "Relax"). The per-phase duration sliders/inputs and section label reappear.
result: pass

### 8. Save button is never proactively disabled
expected: Open the builder dialog. The "Save Preset" button is always clickable — it is never greyed-out or disabled before you attempt to submit. Validation errors only appear after clicking Save.
result: pass

### 9. Edit icon appears on custom preset button (desktop)
expected: On a desktop browser, hover the mouse over a custom preset button. A small edit icon (✎) appears on or near the button while hovering. It is not visible when not hovering.
result: pass

### 10. Edit icon click opens pre-filled edit dialog (desktop)
expected: Click the edit icon (✎) on a custom preset button on desktop. The builder dialog opens in edit mode: the preset name is pre-filled, phase checkboxes reflect the saved state, duration inputs show saved values, and a "Delete" button is visible (not shown in create mode).
result: pass

### 11. Long-press on custom preset opens edit dialog (mobile / touch)
expected: On a touch device (or simulated touch in DevTools), tap and hold a custom preset button for ~300ms. The edit dialog opens pre-filled. During the hold, the button shows a slight scale-down visual feedback.
result: pass

### 12. Long-press cancels on scroll (touchmove)
expected: Start a long-press on a custom preset button, but before 300ms elapses, drag your finger (touchmove). The edit dialog does NOT open — the press is cancelled cleanly.
result: pass

### 13. Edit dialog pre-fills form with all current preset data
expected: Open the edit dialog for "Morning Flow". Verify the name field shows "Morning Flow", checked phases match what was saved, and durations match saved values. No fields are blank or reset to defaults.
result: pass

### 14. Editing a preset updates its button and active phases
expected: Open edit dialog for "Morning Flow", change the name to "Evening Flow" and toggle a phase off. Click "Save Preset". The button text updates to "Evening Flow" and the active phases reflect the change immediately. The update persists after a page reload.
result: pass

### 15. Editing active preset rebuilds phases immediately
expected: Select a custom preset (making it active), then edit it (change a duration or toggle a phase). Click "Save Preset". Without selecting a different preset, the breathing session reflects the updated phase sequence/durations — the ring animation and phase labels update immediately.
result: pass
note: "Phases update when clicking Start or Reset after save"

### 16. Delete button requires two clicks (two-step confirmation)
expected: In the edit dialog, click the "Delete" button. The button label changes to "Confirm Delete" and the button turns red. The preset is NOT deleted yet. Click "Confirm Delete" (second click) → the preset is removed from the selector, the dialog closes.
result: pass

### 17. Delete confirmation resets automatically after 2 seconds
expected: Click "Delete" in the edit dialog (button turns red / "Confirm Delete"). Wait 2+ seconds without clicking again. The button automatically reverts to "Delete" with its original styling. Clicking "Delete" again starts the two-step process fresh.
result: pass

### 18. Deleting a non-active preset does not affect the session
expected: Create two custom presets ("Alpha" and "Beta"). Select "Alpha" (active). Open the edit dialog for "Beta" and complete the two-step deletion. "Beta" disappears from the selector, but "Alpha" remains active and the current session is unaffected.
result: pass

### 19. Deleting the active preset falls back to Relax with Relax highlighted
expected: Select a custom preset (e.g. "Alpha"). Open its edit dialog and complete the two-step deletion. The preset is removed, the app falls back to "Relax" preset, the session resets (ring returns to start, duration inputs reappear), AND the "Relax" button appears highlighted/active in the settings panel.
result: pass

### 20. Hold and Hold2 checkboxes are independent
expected: In the builder dialog, check only Inhale, Hold, and Hold2 (uncheck Exhale). Enabling or disabling Hold does NOT affect Hold2 and vice versa. Set Hold to 2s and Hold2 to 4s — both inputs are independent and save correctly.
result: pass

### 21. Settings display shows correct durations for Hold and Hold2
expected: Create a custom preset with Hold=2s and Hold2=4s. Open the edit dialog. The Hold duration input shows 2s and the Hold2 duration input shows 4s — not the same value for both.
result: pass

### 22. Full lifecycle: create → select → run session → edit → delete
expected: Create a custom preset "Test Full". Select it. Start a breathing session and let it run for one cycle. Stop. Edit the preset (change duration). Restart the session — it uses the updated duration. Delete the preset via two-step confirmation. App falls back to Relax — Relax button is highlighted in settings.
result: pass

### 23. Custom preset buttons are left-aligned (matching built-in row)
expected: Create at least one custom preset and open settings. The custom preset buttons are left-aligned in their row — matching the alignment of the built-in preset buttons (Relax / Box / 4-7-8), not centered.
result: pass

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
passed: 28
issues: 0
pending: 0
skipped: 0
blocked: 0

## Gaps

[none yet]
