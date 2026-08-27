---
status: resolved
trigger: "G-09-21 — After deleting the active custom preset, the app falls back to Relax but the Relax button is not visually highlighted/selected in the settings panel"
created: 2026-08-25T02:00:00Z
updated: 2026-08-27T10:00:00Z
---

## Current Focus

hypothesis: When a custom preset is deleted, the deletion handler falls back to 'relax' (activePresetKey = 'relax') and calls renderCustomPresets() and buildDurationInputs(), but it does NOT update the active class on the built-in preset buttons (Relax, Box, 4-7-8). The built-in buttons retain their previous active state, leaving all preset buttons unhighlighted.
test: Verify that after deletion, the built-in preset button highlight code is not being called
expecting: Missing toggle call for document.querySelectorAll(".presetBtn") after fallback
next_action: Add the missing toggle line after falling back to Relax in the deletion handler

## Symptoms

expected: After the active custom preset is deleted via two-step confirmation, the app falls back to Relax. The session resets (ring returns to start, duration inputs reappear) AND the Relax button in the settings panel shows the active/selected highlight (green border).
actual: The fallback to Relax works correctly (session resets, duration inputs reappear, mode indicator shows Relax), but the Relax button in the settings panel does NOT show the active highlight — no preset button appears selected/highlighted after deletion.
errors: none — state/rendering issue
timeline: Observed during Phase 9 fresh UAT (test 21). Note: G-09-23 (the original "Relax not highlighted after delete" bug) was supposedly fixed in 09-05-PLAN, but the issue persists in the fresh UAT.
reproduction: 1) Create a custom preset and select it. 2) Open its edit dialog. 3) Click Delete, then Confirm Delete. 4) Observe the settings panel — no preset button shows as highlighted/active.

## Evidence

- timestamp: 2026-08-25T14:30:00Z
  checked: Deletion handler at lines 2165-2211
  found: When a custom preset is deleted and was active (line 2195), the code sets activePresetKey = "relax" but then calls renderCustomPresets() (line 2203) which only re-renders custom presets. It never updates the built-in preset buttons' active class.
  implication: The built-in buttons (Relax, Box, 4-7-8) remain unhighlighted because the toggle logic is missing.

- timestamp: 2026-08-25T14:31:00Z
  checked: Pattern in preset save handler at line 2155
  found: After saving a custom preset, the save handler calls document.querySelectorAll(".presetBtn").forEach(b => b.classList.toggle("active", b.dataset.preset === activePresetKey)) to update all preset buttons.
  implication: This same toggle pattern exists elsewhere but is missing from the deletion handler.

- timestamp: 2026-08-25T14:32:00Z
  checked: Built-in preset buttons HTML at lines 1387-1389
  found: Three built-in preset buttons (Relax, Box, 4-7-8) with data-preset attributes. These are rendered in HTML once and only their active class needs updating.
  implication: The renderCustomPresets() call only updates custom presets, not these static built-in buttons.

## Eliminated

## Resolution

root_cause: After deleting an active custom preset, the deletion handler (lines 2165-2211) falls back to activePresetKey = "relax" but does not update the active CSS class on the built-in preset buttons (Relax, Box, 4-7-8). The renderCustomPresets() call only updates custom preset buttons, leaving the built-in buttons unhighlighted.
fix: Add document.querySelectorAll(".presetBtn").forEach(b => b.classList.toggle("active", b.dataset.preset === activePresetKey)) after renderCustomPresets() in the deletion handler. This matches the pattern used in the save handler and ensures all preset buttons' active states are synchronized.
verification: Pending manual UAT
files_changed: [index.html]
