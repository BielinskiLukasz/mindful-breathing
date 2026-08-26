---
status: resolved
trigger: "G-09-9 / G-09-20a — Edit icon click handler broken on desktop"
created: 2026-08-24T00:00:00Z
updated: 2026-08-27T00:00:00Z
resolved: 2026-08-27T00:00:00Z
---

## Current Focus

hypothesis: editIcon.style.pointerEvents = "none" blocked all pointer events on the edit icon — CONFIRMED and fixed

test: Verified via git history (commit c50c8bc) and grep that the line is removed; click listener with stopPropagation is reachable

expecting: Edit icon click opens dialog on desktop

next_action: confirmed fixed

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

root_cause: editIcon.style.pointerEvents = "none" was set inline in renderCustomPresets() with comment "// Don't interfere with button click". This disables all pointer events on the element, causing clicks to fall through to the .presetBtn button beneath and trigger preset selection instead of openEditDialog(). The comment reflects a misunderstanding — e.stopPropagation() in the click listener already prevents the click from reaching the button; pointer-events: none was redundant for that goal and destructive to the listener.

fix: Deleted the line `editIcon.style.pointerEvents = "none";` — committed in c50c8bc "fix(09-06): remove pointerEvents blocking edit icon click listener"

verification: |
  - grep confirms pointer-events/pointerEvents not present on editIcon anywhere in file
  - Click listener at line 1983 reachable: e.preventDefault() + e.stopPropagation() + openEditDialog(preset)
  - Diff is exactly 1 line deletion — minimal and targeted
  - Adjacent functionality unaffected (preset selection, mobile long-press, hover reveal, CSS rules)
  - guardrail_verdict: accepted (all 4 applicable signals pass; no test suite present)

oracle_type: implicit (visual confirmation — dialog opens on desktop click)

files_changed: [index.html]
