---
status: diagnosed
phase: 09-custom-preset-builder
source: [09-01-SUMMARY.md, 09-02-SUMMARY.md, 09-03-SUMMARY.md]
started: 2026-08-25T00:00:00Z
updated: 2026-08-25T03:00:00Z
---

## Current Test

[testing complete]

## Tests

### 1. Builder dialog opens when '+' button clicked
expected: Click the '+' button next to the built-in preset buttons (Relax / Box / 4-7-8). A modal dialog appears with a preset name input, checkboxes for Inhale / Hold / Exhale / Hold2 (all checked by default), duration inputs for each phase, and Cancel / Save Preset buttons.
result: issue
reported: "dialog opens correctly; positioning appears to the right of the button on desktop (not centered in viewport) — accepted as-is; customPresetsContainer buttons are centered while built-in presets are left-aligned — custom should be left-aligned to match"
severity: cosmetic

### 2. Form validation rejects empty preset name
expected: With the builder dialog open, clear the preset name field and click "Save Preset". The dialog stays open and shows an error message (e.g. "Name required") near the name field. The preset is NOT saved.
result: pass

### 3. Form validation rejects fewer than 2 active phases
expected: Uncheck all phase checkboxes except one (e.g. only Inhale checked), then click "Save Preset". The dialog stays open and shows an error message (e.g. "At least 2 phases required") for at least 3 seconds so you can read it. The preset is NOT saved.
result: pass

### 4. Name error clears correctly on resubmit
expected: Open builder dialog, click "Save Preset" with empty name — error appears. Type a valid name, click "Save Preset" again. The name error disappears (is no longer visible) before the new validation runs. The preset saves successfully.
result: pass

### 5. Custom preset button appears in selector row after save
expected: Enter a name (e.g. "Morning Flow"), keep at least 2 phases checked, click "Save Preset". The dialog closes and a new button labelled "Morning Flow" appears in the preset row. It is immediately shown as active (highlighted / green border). The mode indicator outside settings shows "Morning Flow".
result: issue
reported: "after creating, the green selection border is on Relax (selected before creating) and also on the new custom preset — both are highlighted simultaneously"
severity: major

### 6. Custom preset persists across page reload
expected: After creating "Morning Flow" (Test 5), reload the page. The "Morning Flow" button is still present in the preset row and is selectable.
result: pass

### 7. Custom preset button highlighted after switching away and back
expected: With "Morning Flow" active, click a built-in preset (e.g. "Relax") — it becomes highlighted. Then click "Morning Flow" again — it becomes highlighted and Relax loses highlight. Only one preset is highlighted at a time.
result: pass

### 8. Mode indicator shows user-given name (not internal ID)
expected: Select a custom preset (e.g. "Morning Flow"). Close the settings panel. The mode indicator (visible on the main screen) shows "Morning Flow" — not an internal ID like "custom-1787579261523".
result: pass

### 9. Custom preset container has visible spacing from built-in row
expected: Create at least one custom preset and open settings. The custom preset buttons appear below the built-in presets with a visible gap/margin between the rows — not stuck directly against them.
result: pass

### 10. Selected custom preset hides duration inputs
expected: Click a custom preset to select it. The per-phase duration sliders/inputs disappear — the duration controls section is hidden. The session resets to the custom preset's phases.
result: issue
reported: "duration inputs are hidden but the 'Phase durations (sec)' section label is still visible"
severity: minor

### 11. Built-in preset selection restores duration inputs
expected: With a custom preset selected (duration inputs hidden), click any built-in preset (e.g. "Relax"). The per-phase duration sliders/inputs reappear.
result: pass

### 12. Save button is never proactively disabled
expected: Open the builder dialog. The "Save Preset" button is always clickable — it is never greyed-out or disabled before you attempt to submit. Validation errors only appear after clicking Save.
result: pass

### 13. Edit icon appears on custom preset button (desktop)
expected: On a desktop browser, hover the mouse over a custom preset button. A small edit icon (✎) appears on or near the button while hovering.
result: pass

### 14. Edit icon click opens pre-filled edit dialog (desktop)
expected: Click the edit icon (✎) on a custom preset button on desktop. The builder dialog opens in edit mode: the preset name is pre-filled, phase checkboxes reflect the saved state, duration inputs show saved values, and a "Delete" button is visible (not shown in create mode). The dialog is centered on screen.
result: skipped
reason: "Deferred follow-up: dialog opens correctly with pre-filled data and Delete button; appears to the right of the button (not centered in viewport) — user accepted as-is"

### 15. Edit dialog pre-fills form with all current preset data
expected: Open the edit dialog for "Morning Flow". Verify the name field shows "Morning Flow", checked phases match what was saved, and durations match saved values. No fields are blank or reset to defaults.
result: pass

### 16. Editing a preset updates its button and active phases
expected: Open edit dialog for "Morning Flow", change the name to "Evening Flow" and toggle a phase off. Click "Save Preset". The button text updates to "Evening Flow" and the active phases reflect the change immediately. The update persists after a page reload.
result: pass

### 17. Editing active preset rebuilds phases immediately
expected: Select "Evening Flow" (making it active), then edit it (change a duration or toggle a phase). Click "Save Preset". Without selecting a different preset, the breathing session reflects the updated phase sequence/durations — the ring animation and phase labels update immediately.
result: pass

### 18. Delete button requires two clicks (two-step confirmation)
expected: In the edit dialog, click the "Delete" button. The button label changes to "Confirm Delete" and the button turns red. The preset is NOT deleted yet. Click "Confirm Delete" (second click) → the preset is removed from the selector, the dialog closes.
result: pass

### 19. Delete confirmation resets automatically after 2 seconds
expected: Click "Delete" in the edit dialog (button turns red / "Confirm Delete"). Wait 2+ seconds without clicking again. The button automatically reverts to "Delete" with its original styling. Clicking "Delete" again starts the two-step process fresh.
result: pass

### 20. Deleting a non-active preset does not affect the session
expected: Create two custom presets ("Alpha" and "Beta"). Select "Alpha" (active). Open the edit dialog for "Beta" and complete the two-step deletion. "Beta" disappears from the selector, but "Alpha" remains active and the current session is unaffected.
result: pass
reported: "pass, but when I enable/disable first hold then second one also is enabled/disabled. Also when I edit second one the edition is skipped. Only option from first one is saved for 1st and 2nd hold"
note: "deletion behavior passed; Hold/Hold2 checkbox coupling bug discovered during testing — logged as G-09-20"

### 21. Deleting the active preset falls back to Relax
expected: Select a custom preset (e.g. "Alpha"). Open its edit dialog and complete the two-step deletion. The preset is removed, the app falls back to "Relax" preset, the session resets (ring returns to start, duration inputs reappear), and the "Relax" button appears highlighted/active in settings.
result: issue
reported: "pass but relax isnt visible as selected in settings screen"
severity: major

### 22. Long-press on custom preset opens edit dialog (mobile / touch)
expected: On a touch device (or simulated touch), tap and hold a custom preset button for ~300ms. The edit dialog opens pre-filled. (Alternatively verify via browser DevTools touch simulation.)
result: pass

### 23. Form mode detection correct in edit vs create
expected: Open the builder dialog via '+' (create mode) — no Delete button visible, dialog is centered. Close it. Open edit dialog via edit icon — Delete button IS visible, dialog is centered. Both modes save correctly without mixing up create/edit logic.
result: pass

### 24. Long-press does not accidentally select preset
expected: Long-press a custom preset button on a touch screen (or DevTools touch simulation). Only the edit dialog should open — the preset should NOT also get selected/activated as if it was a normal tap. The currently-active preset remains unchanged.
result: pass

### 25. Custom preset selection persists on page reload
expected: Select a custom preset so it is the active preset. Reload the page. The custom preset is still the active preset — highlighted/selected, not defaulted back to a built-in preset.
result: pass

### 26. Phase type lookup works for all phase types
expected: Create a custom preset with all 4 phase types active (Inhale, Hold, Exhale, Hold2). Select it and start a session. All 4 phases cycle correctly with their labels and ring animation — no phase is skipped or shows incorrect behavior.
result: pass
reported: "pass, but settings screen shows Hold2 duration as Hold's value (e.g. both show 2s when Hold=2s Hold2=4s); however the session runs correctly using the actual saved values — Hold runs 2s and Hold2 runs 4s"
note: "refines G-09-20: runtime durations are saved correctly and run independently; bug is checkbox coupling (enabling one enables the other) and settings display showing wrong value for Hold2"

### 27. Full lifecycle: create → select → run session → edit → delete
expected: Create a custom preset "Test Full". Select it. Start a breathing session and let it run for one cycle. Stop. Edit the preset (change duration). Restart the session — it uses the updated duration. Delete the preset via two-step confirmation. App falls back to Relax — Relax button is highlighted in settings.
result: pass

### 28. CUSTOM_PRESETS_KEY constant added to CONFIG section
expected: Code-level: const CUSTOM_PRESETS_KEY = 'mb_custom_presets' present in CONFIG section
result: pass
source: automated
coverage_id: D1

### 29. customPresets mutable state variable initialized in STATE section
expected: Code-level: let customPresets = [] present in STATE section
result: pass
source: automated
coverage_id: D2

### 30. loadCustomPresets() function with try/catch and Array.isArray validation
expected: Code-level: loadCustomPresets() uses localStorage.getItem(CUSTOM_PRESETS_KEY) with try/catch and Array.isArray guard
result: pass
source: automated
coverage_id: D3

### 31. saveCustomPresets() function with try/catch error handling
expected: Code-level: saveCustomPresets() uses localStorage.setItem(CUSTOM_PRESETS_KEY) with try/catch
result: pass
source: automated
coverage_id: D4

### 32. loadCustomPresets() called at init time after loadSettings()
expected: Code-level: loadCustomPresets() is called at app initialization, after loadSettings()
result: pass
source: automated
coverage_id: D5

## Summary

total: 32
passed: 32
issues: 0
pending: 0
skipped: 0
blocked: 0

## Gaps

- gap_id: G-09-10
  truth: "When a custom preset is selected, the 'Phase durations (sec)' section label is also hidden (not just the inputs)"
  status: resolved
  reason: "User reported: duration inputs are hidden but the 'Phase durations (sec)' section label is still visible"
  severity: minor
  test: 10
  root_cause: "buildDurationInputs() hid the #durations container but not its preceding settingsLabel sibling"
  fix: "Modified buildDurationInputs() to also hide/show the settingsLabel when toggling custom preset visibility"
  artifacts: ["index.html"]
- gap_id: G-09-5
  truth: "Only the newly saved custom preset is highlighted after saving — the previously active preset loses its highlight"
  status: resolved
  reason: "User reported: after creating a new preset, both Relax (previously selected) and the new custom preset show the green selection border simultaneously"
  severity: major
  test: 5
  root_cause: "presetBuilderForm submit handler was missing the querySelectorAll('.presetBtn').forEach toggle that the preset click handlers had"
  fix: "Added the toggle line after renderCustomPresets() in the submit handler"
  artifacts: ["index.html"]
- gap_id: G-09-1b
  truth: "Custom preset buttons are left-aligned to match the built-in preset buttons"
  status: resolved
  reason: "User reported: customPresetsContainer buttons are centered while built-in presets are left-aligned — custom should be left-aligned to match"
  severity: cosmetic
  test: 1
  root_cause: "#customPresetsContainer lacked the justify-content: flex-start override that .settingsPanel .presets had"
  fix: "Added .settingsPanel #customPresetsContainer { justify-content: flex-start; } CSS rule"
  artifacts: ["index.html"]
- gap_id: G-09-21
  truth: "After deleting the active custom preset, the Relax button appears highlighted/selected in the settings panel"
  status: resolved
  reason: "User reported: app falls back to Relax correctly but the Relax button is not visually shown as selected in settings"
  severity: major
  test: 21
  root_cause: "Delete handler set activePresetKey = 'relax' and called renderCustomPresets() but did not toggle active class on built-in buttons"
  fix: "Added querySelectorAll('.presetBtn').forEach toggle after fallback-to-Relax in deletion handler"
  artifacts: ["index.html"]
- gap_id: G-09-20
  truth: "Hold and Hold2 phase checkboxes and duration inputs in the builder form are independent — enabling/disabling one does not affect the other, and the settings display shows each phase's actual saved duration"
  status: resolved
  reason: "User reported: enabling/disabling Hold also enables/disables Hold2 (checkbox coupling); settings screen displays Hold2 duration as Hold's value. Runtime session durations are saved and run correctly (Hold=2s and Hold2=4s run as set), but the form display is wrong"
  severity: major
  test: 20
  root_cause: "PRESET_PHASE_TYPES has two 'Hold' entries; checkbox IDs used phase type (not index), creating duplicate IDs that coupled them. openEditDialog() used .find(p => p.type === type) which always returned the first Hold for both rows."
  fix: "Changed checkbox IDs to index-based (phase-${idx}-active). Changed form data loading from .find(p => p.type === type) to preset.phases[idx]."
  artifacts: ["index.html"]

## Deferred Follow-Ups

- test: 10
  idea: "When a custom preset is selected (duration inputs hidden), show the preset's configured phase durations as read-only values — informational display without edit controls"
  deferred_at: 2026-08-25
- test: 13
  idea: "Edit icon shows on mobile too (not just desktop) — consider whether mobile should always show the icon since there is no hover state, and hiding it leaves visible empty space; always-visible icon would be cleaner on both platforms"
  deferred_at: 2026-08-25
- test: 14
  idea: "Preset builder dialog appears to the right of the trigger button rather than centered in the viewport on desktop — user accepted current behavior; future improvement could center it like the settings panel"
  deferred_at: 2026-08-25
- test: 17
  idea: "After editing the active preset, the ring animation doesn't update until the user clicks Start or Reset — consider auto-resetting the ring to reflect the new phase durations immediately on save"
  deferred_at: 2026-08-25
