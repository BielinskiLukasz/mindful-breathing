---
status: diagnosed
trigger: "G-09-9 / G-09-20a — Edit icon click handler broken on desktop"
created: 2026-08-24T00:00:00Z
updated: 2026-08-24T00:00:00Z
---

## Current Focus

hypothesis: The editIcon element has `pointer-events: none` applied inline, which prevents it from receiving click events. Clicks pass through to the button underneath, triggering the preset selection handler instead.

test: Traced event handlers on editIcon and button; verified CSS property blocks pointer events

expecting: Root cause confirmed — pointer-events CSS prevents click listener from firing

next_action: DIAGNOSIS COMPLETE

## Symptoms

expected: Clicking the edit icon (✎) on desktop should open the edit dialog for custom presets
actual: Click on edit icon does nothing; dialog never opens on desktop
errors: None (silent failure)
reproduction: Hover over custom preset button on desktop, see edit icon appear, click it — nothing happens
started: Introduced in phase 09 (custom preset builder feature)

## Eliminated

None

## Evidence

- timestamp: 2026-08-24
  checked: renderCustomPresets() function (line 1865-1930)
  found: Edit icon is created at lines 1876-1883, click listener attached at lines 1922-1926
  implication: Click listener exists, but may not be receiving clicks

- timestamp: 2026-08-24
  checked: Line 1882 specifically
  found: editIcon.style.pointerEvents = "none";
  implication: pointer-events CSS prevents any pointer events (including clicks) on the edit icon element

- timestamp: 2026-08-24
  checked: Event delegation at lines 2970-2989 (customPresetsContainer click handler)
  found: Clicks on .presetBtn are captured by e.target.closest(".presetBtn") and select the preset
  implication: When editIcon has pointer-events: none, clicks pass through to the button, triggering preset selection instead of edit dialog

- timestamp: 2026-08-24
  checked: Mobile long-press handler (lines 1887-1891)
  found: Works correctly because it listens on the button itself, not the edit icon
  implication: Mobile correctly bypasses the button's click handler via setTimeout logic; desktop clicks are blocked by pointer-events: none

## Resolution

root_cause: The editIcon element has `pointer-events: none` inline style applied (line 1882), which disables all pointer events on that element. This prevents the click listener from firing. Clicks pass through the edit icon to the button underneath, triggering the preset selection handler instead of the edit dialog.

fix: Remove line 1882 entirely, or change it from `editIcon.style.pointerEvents = "none";` to `editIcon.style.pointerEvents = "auto";`

verification: None yet (diagnosis only)

files_changed: []
