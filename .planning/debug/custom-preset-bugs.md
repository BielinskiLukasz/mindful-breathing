---
status: investigating
trigger: "Custom preset bugs: G-09-4a/5a/6a (button not highlighted), G-09-4b/6b (shows internal ID instead of name), G-09-23 (Relax not highlighted after delete)"
created: 2026-08-24T00:00:00Z
updated: 2026-08-24T00:00:00Z
---

## Current Focus

hypothesis: Three related state management issues: (1) renderCustomPresets() not applying active class, (2) main UI displaying activePresetKey directly instead of looking up .name property, (3) delete flow not re-rendering buttons after fallback to relax
test: Reading index.html to trace renderCustomPresets(), active preset name rendering, and delete flow
expecting: Find specific line numbers showing the bugs
next_action: Read index.html and locate the functions involved

## Symptoms

expected: 
- Custom preset button highlighted with green when selected
- Preset name shown as user-given name (e.g. "Morning Flow"), not internal ID
- After delete, Relax button highlighted as active fallback

actual:
- Button not highlighted when selected
- Shows "custom-1787579261523" instead of "Morning Flow"
- Relax button not highlighted after deleting active preset

errors:
- None (silent failures)

reproduction:
1. Create custom preset with name "Test"
2. Click button - preset is active but button not highlighted
3. Main UI shows preset ID not name
4. Delete the active preset - fallback to Relax but button not highlighted

started: Phase 09 custom preset builder feature

## Eliminated

## Evidence

- timestamp: 2026-08-24
  checked: renderCustomPresets() function at lines 1865-1930
  found: Line 1873 correctly applies active class: `if (activePresetKey === preset.id) btn.classList.add("active")`
  implication: renderCustomPresets() logic is correct; bug must be in click handler

- timestamp: 2026-08-24
  checked: Custom preset button click handler at lines 2970-2989
  found: Line 2982 calls renderCustomPresets() which creates NEW button elements; line 2987 then tries to use stale `btn` reference from event (from OLD buttons). The toggle logic `b === btn` compares new buttons against old button object, causing match to fail
  implication: BUG #1 ROOT CAUSE - Line 2987 removes active class that was just added by renderCustomPresets()

- timestamp: 2026-08-24
  checked: updateModeIndicator() at lines 1733-1736
  found: Line 1735 reads MODE_LABELS dict which only has entries for "relax", "box", "478". Falls back to displaying activePresetKey directly when key not found. For custom presets like "custom-1787579261523", this returns the ID not the name
  implication: BUG #2 ROOT CAUSE - Custom preset name lookup missing; need to find preset object and read .name property

- timestamp: 2026-08-24
  checked: Delete button handler at lines 2137-2183
  found: Line 2168 sets `activePresetKey = "relax"` when active custom preset is deleted. Line 2175 calls renderCustomPresets(). renderCustomPresets() at line 1873 checks activePresetKey === preset.id. But WAIT - at line 2987 the manual toggle runs and removes active class. However, built-in presets are NOT recreated, so the toggle might work for "relax" button
  implication: BUG #3 likely cascade from BUG #1 - the manual toggle logic breaks both custom AND built-in preset highlighting

## Resolution

root_cause: 
  THREE related causes:
  1. Line 2987 (and 2964) - manual toggle uses stale button reference and removes active class that renderCustomPresets() just applied
  2. Line 1735 - updateModeIndicator() displays activePresetKey directly instead of looking up custom preset .name property
  3. Both handlers at 2939-2965 and 2970-2989 recreate buttons with renderCustomPresets() but then undo the highlighting with manual toggle

fix: 
  1. Replace line 2987 (and 2964) with logic that checks activePresetKey value instead of stale button reference
  2. Fix updateModeIndicator() to look up custom preset name
  3. Remove renderCustomPresets() from presetsEl handler or fix the toggle logic to work with recreated buttons

verification: 
files_changed: []
