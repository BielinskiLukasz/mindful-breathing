---
status: complete
phase: 09-custom-preset-builder
source: [09-01-SUMMARY.md, 09-02-SUMMARY.md, 09-03-SUMMARY.md]
started: 2026-08-01T00:00:00Z
updated: 2026-08-24T00:00:00Z
---

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
result: issue
reported: "y but name error message not disappear even after I write the name (it not appears when I type the name before clicking save, but when I click save -> got error message -> type name -> click save then error is still visible. Also error for fewer than 2 active phases appears and disappear after less than 1 second"
severity: major

### 4. Custom preset button appears in selector row after save
expected: Enter a name (e.g. "Morning Flow"), keep at least 2 phases checked, click "Save Preset". The dialog closes and a new button labelled "Morning Flow" appears in the preset row next to the built-in presets. It is immediately shown as active (highlighted).
result: issue
reported: "y but: Previous selected mode is also highlighted (also after reopen settings both are highlighted). After closing settings screen mode name is custom-1785770816888 instead of selected name"
severity: major

### 5. Custom preset persists across page reload
expected: After creating "Morning Flow" (Test 4), reload the page. The "Morning Flow" button is still present in the preset row and is selectable.
result: issue
reported: "y but: if I select other mode and after that I try to select custom one then is selected but not highlighted. Also if second row of modes appears its stick to first row (no margin)"
severity: major

### 6. Selected custom preset hides duration inputs
expected: Click "Morning Flow" (or any custom preset) to select it. The per-phase duration sliders/inputs disappear — the duration controls section is hidden. The session resets to the custom preset's phases.
result: issue
reported: "When I try to select it there is no green box showing that I select it. After closing windows I see custom-1787579261523 instead name I provided. Also there is no margin between presets and customPresetsContainer divs."
severity: major

### 7. Built-in preset selection restores duration inputs
expected: With a custom preset selected (duration inputs hidden), click any built-in preset (e.g. "Relax"). The per-phase duration sliders/inputs reappear.
result: pass

### 8. Save button is never proactively disabled
expected: Open the builder dialog. The "Save Preset" button is always clickable — it is never greyed-out or disabled before you attempt to submit. Validation errors only appear after clicking Save.
result: pass

### 9. Edit icon appears on custom preset button (desktop)
expected: On a desktop browser, hover the mouse over a custom preset button. A small edit icon (✎) appears on or near the button while hovering.
result: issue
reported: "icon it there but it not working"
severity: major

### 10. Edit icon click opens pre-filled edit dialog
expected: Click the edit icon (✎) on a custom preset button. The builder dialog opens in edit mode: the preset name is pre-filled, phase checkboxes reflect the saved state, duration inputs show saved values, and a "Delete" button is visible (not shown in create mode).
result: issue
reported: "nope, nothing happens after clicking edit icon"
severity: major

### 11. Edit dialog pre-fills form with all current preset data
expected: Open the edit dialog for "Morning Flow". Verify the name field shows "Morning Flow", checked phases match what was saved, and durations match saved values. No fields are blank or reset to defaults.
result: issue
reported: "edit dialog doesnt appears after edit clicking"
severity: major

### 12. Editing a preset updates its button and active phases
expected: Open edit dialog for "Morning Flow", change the name to "Evening Flow" and toggle a phase off. Click "Save Preset". The button text updates to "Evening Flow" and the active phases reflect the change immediately. The update persists after a page reload.
result: issue
reported: "edit dialog doesnt appears after edit clicking"
severity: major

### 13. Editing active preset rebuilds phases immediately
expected: Select "Evening Flow" (making it active), then edit it (change a duration or toggle a phase). Click "Save Preset". Without selecting a different preset, the breathing session reflects the updated phase sequence/durations — the ring animation and phase labels update immediately.
result: issue
reported: "edit dialog doesnt appears after edit clicking"
severity: major

### 14. Delete button requires two clicks (two-step confirmation)
expected: In the edit dialog, click the "Delete" button. The button label changes to "Confirm Delete" and the button turns red. The preset is NOT deleted yet. Click "Confirm Delete" (second click) → the preset is removed from the selector, the dialog closes.
result: blocked
blocked_by: prior-phase
reason: "Edit icon click handler broken (G-09-9) — edit dialog never opens, cannot reach Delete button"

### 15. Delete confirmation resets automatically after 2 seconds
expected: Click "Delete" in the edit dialog (button turns red / "Confirm Delete"). Wait 2+ seconds without clicking again. The button automatically reverts to "Delete" with its original styling. Clicking "Delete" again starts the two-step process fresh.
result: blocked
blocked_by: prior-phase
reason: "Edit icon click handler broken (G-09-9) — edit dialog never opens"

### 16. Deleting a non-active preset does not affect the session
expected: Create two custom presets ("Alpha" and "Beta"). Select "Alpha" (active). Open the edit dialog for "Beta" and complete the two-step deletion. "Beta" disappears from the selector, but "Alpha" remains active and the current session is unaffected.
result: blocked
blocked_by: prior-phase
reason: "Edit icon click handler broken (G-09-9) — edit dialog never opens"

### 17. Deleting the active preset falls back to Relax
expected: Select a custom preset (e.g. "Alpha"). Open its edit dialog and complete the two-step deletion. The preset is removed, the app falls back to "Relax" preset, the session resets (ring returns to start, duration inputs reappear), and the "Relax" button appears active.
result: blocked
blocked_by: prior-phase
reason: "Edit icon click handler broken (G-09-9) — edit dialog never opens"

### 18. Long-press on custom preset opens edit dialog (mobile / touch)
expected: On a touch device (or simulated touch), tap and hold a custom preset button for ~300ms. The edit dialog opens pre-filled. (Alternatively verify via browser DevTools touch simulation.)
result: blocked
blocked_by: prior-phase
reason: "Edit icon click handler broken (G-09-9) — edit dialog never opens"

### 19. Custom preset selection persists on page reload (CR-02 fix)
expected: Select a custom preset (e.g. "Morning Flow") so it is the active preset. Reload the page. "Morning Flow" is still the active preset — it should be highlighted/selected, not defaulted back to a built-in preset.
result: pass

### 20. Form mode detection correct in edit vs create (WR-01 fix)
expected: Open the builder dialog via '+' (create mode) — no Delete button visible. Close it. Open edit dialog via edit icon — Delete button IS visible. Both modes save correctly without mixing up create/edit logic.
result: issue
reported: "I can open it only via + button, cannot open it via edit button. And dialog is on left top corner. Couldn't be in middle like settings one?"
severity: major

### 21. Phase type lookup works for all phase types (WR-04 fix)
expected: Create a custom preset with all 4 phase types active (Inhale, Hold, Exhale, Hold2). Select it and start a session. All 4 phases cycle correctly with their labels and ring animation — no phase is skipped or shows incorrect behavior.
result: pass

### 22. Long-press does not accidentally select preset (WR-03 fix)
expected: Long-press a custom preset button on a touch screen (or DevTools touch simulation). Only the edit dialog should open — the preset should NOT also get selected/activated as if it was a normal tap. The currently-active preset remains unchanged.
result: pass

### 23. Full lifecycle: create → select → run session → edit → delete
expected: Create a custom preset "Test Full". Select it. Start a breathing session and let it run for one cycle. Stop. Edit the preset (change duration). Restart the session — it uses the updated duration. Delete the preset via two-step confirmation. App falls back to Relax. All changes persisted correctly throughout.
result: issue
reported: "It's fallback to relax but it not selected on settings screen after deleting custom one"
severity: major

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
passed: 12
issues: 11
pending: 0
blocked: 5
skipped: 0
blocked: 0

## Gaps

- gap_id: G-09-3a
  truth: "Name error message clears when user types a valid name after a failed save attempt"
  status: failed
  reason: "User reported: error message stays visible even after typing a name — only clears on second valid save, not on input"
  severity: major
  test: 3
  artifacts: []
  missing: []
- gap_id: G-09-3b
  truth: "Validation error for fewer than 2 phases stays visible until user fixes the issue"
  status: failed
  reason: "User reported: 'At least 2 phases required' error appears and disappears in under 1 second — user cannot read it"
  severity: major
  test: 3
  artifacts: []
  missing: []
- gap_id: G-09-4a
  truth: "Selecting a custom preset deactivates the previously selected preset (only one preset highlighted at a time)"
  status: failed
  reason: "User reported: previous selected mode is also highlighted after saving a new custom preset; both remain highlighted on reopening settings"
  severity: major
  test: 4
  artifacts: []
  missing: []
- gap_id: G-09-4b
  truth: "The active preset name shown in the UI (outside settings) matches the user-given name (e.g. 'Morning Flow')"
  status: failed
  reason: "User reported: after closing settings, mode name shows internal ID 'custom-1785770816888' instead of the user-given name"
  severity: major
  test: 4
  artifacts: []
  missing: []
- gap_id: G-09-5a
  truth: "Custom preset button appears highlighted/active when selected after switching away from it and back"
  status: failed
  reason: "User reported: selecting another mode then re-selecting the custom preset — it is selected (functionally) but the button is not highlighted"
  severity: major
  test: 5
  artifacts: []
  missing: []
- gap_id: G-09-5b
  truth: "When preset buttons wrap to a second row, there is visible margin/gap between the rows"
  status: failed
  reason: "User reported: second row of mode buttons sticks to the first row with no margin between them"
  severity: cosmetic
  test: 5
  artifacts: []
  missing: []
- gap_id: G-09-6a
  truth: "Selected custom preset button shows active highlight (green box) immediately on selection"
  status: failed
  reason: "User reported: no green box showing that I select it when trying to select a custom preset"
  severity: major
  test: 6
  artifacts: []
  missing: []
- gap_id: G-09-6b
  truth: "After closing the settings panel, the active preset displays the user-given name (e.g. 'Morning Flow'), not the internal ID"
  status: failed
  reason: "User reported: after closing windows, shows 'custom-1787579261523' instead of the name provided"
  severity: major
  test: 6
  artifacts: []
  missing: []
- gap_id: G-09-6c
  truth: "Visible margin/gap exists between the built-in preset row and the customPresetsContainer div"
  status: failed
  reason: "User reported: no margin between presets and customPresetsContainer divs"
  severity: cosmetic
  test: 6
  artifacts: []
  missing: []
- gap_id: G-09-9
  truth: "Clicking the edit icon (✎) on a custom preset button opens the edit dialog (desktop)"
  status: failed
  reason: "User reported: icon is there but it not working on desktop — long-press on mobile DOES work (confirmed test 22)"
  severity: major
  test: 9
  artifacts: []
  missing: []
- gap_id: G-09-10
  truth: "Edit dialog opens pre-filled with preset name, phase checkboxes, durations, and Delete button visible (desktop click)"
  status: failed
  reason: "User reported: nothing happens after clicking edit icon on desktop — dialog never opens via click"
  severity: major
  test: 10
  artifacts: []
  missing: []
- gap_id: G-09-11
  truth: "Edit dialog opens pre-filled with all saved preset data via desktop click"
  status: failed
  reason: "User reported: edit dialog doesn't appear after clicking edit icon on desktop"
  severity: major
  test: 11
  artifacts: []
  missing: []
- gap_id: G-09-12
  truth: "Editing a preset renames its button and updates active phases immediately, persisting after reload"
  status: failed
  reason: "User reported: edit dialog doesn't appear via desktop click — cannot test edit save flow on desktop"
  severity: major
  test: 12
  artifacts: []
  missing: []
- gap_id: G-09-13
  truth: "Editing the active preset rebuilds phases immediately without needing to reselect"
  status: failed
  reason: "User reported: edit dialog doesn't appear via desktop click — cannot test edit save flow on desktop"
  severity: major
  test: 13
  artifacts: []
  missing: []
- gap_id: G-09-20a
  truth: "Edit dialog can be opened via the edit icon click on desktop (WR-01 mode detection)"
  status: failed
  reason: "User reported: can only open via + button on desktop — edit icon click does not open dialog; mobile long-press confirmed working (test 22)"
  severity: major
  test: 20
  artifacts: []
  missing: []
- gap_id: G-09-20b
  truth: "Preset builder dialog is centered on screen (matches settings panel positioning)"
  status: failed
  reason: "User reported: dialog appears in top-left corner instead of centered like the settings dialog"
  severity: cosmetic
  test: 20
  artifacts: []
  missing: []
- gap_id: G-09-23
  truth: "After deleting a custom preset and falling back to Relax, the Relax button appears highlighted/active in the settings screen"
  status: failed
  reason: "User reported: app falls back to Relax functionally but Relax is not shown as selected in the settings screen"
  severity: major
  test: 23
  artifacts: []
  missing: []

## Deferred Follow-Ups

- test: 7
  idea: "When a custom preset is selected (duration inputs hidden), show the preset's configured phase durations as read-only values — informational display like built-in presets have, but without edit controls"
  deferred_at: 2026-08-24
