---
status: resolved
trigger: "Custom preset bugs: G-09-4a/5a/6a (button not highlighted), G-09-4b/6b (shows internal ID instead of name), G-09-23 (Relax not highlighted after delete)"
created: 2026-08-24T00:00:00Z
updated: 2026-08-27T00:00:00Z
resolved: 2026-08-27T00:00:00Z
---

## Current Focus

reasoning_checkpoint:
  hypothesis: "Two distinct root causes: (1) stale button reference in click handler toggle (`b === btn` always false after renderCustomPresets() recreates elements); (2) updateModeIndicator() missing custom preset name lookup, falling back to raw key"
  confirming_evidence:
    - "git show 8d06b94 confirms old code: `b === btn` in both click handlers — always false after renderCustomPresets() destroys and recreates buttons"
    - "git show 8d06b94 confirms old updateModeIndicator: `el.textContent = MODE_LABELS[activePresetKey] || activePresetKey` — no custom preset lookup, raw ID shown"
    - "Current code uses `b.dataset.preset === activePresetKey` (value-based, works on new elements) and updateModeIndicator() has proper customPresets.find() lookup"
  falsification_test: "If stale-ref hypothesis were wrong, removing `renderCustomPresets()` from click handlers would not affect highlighting — but it does since old buttons are destroyed"
  fix_rationale: "Replacing object identity comparison with value comparison survives element recreation; adding customPresets.find() lookup makes indicator read the .name property"
  blind_spots: "No automated tests — cannot mutation-test or run regression suite"
  candidate_causes:
    - "code: stale DOM reference used in toggle condition after element recreation"
    - "code: missing data-source branch in mode indicator for non-built-in preset keys"
  and_gate: "no — both causes are independent; each produces its own distinct symptom"

guardrail_verdict: accepted
  signal_3: PASS — fix is behavioral correction (wrong comparison → correct comparison, missing branch → added branch), not a deletion
  signal_5: PASS — code trace confirms: old `b === btn` was always false post-recreate; new `b.dataset.preset === activePresetKey` is always correct
  signal_1_2_4: DEGRADED — no automated test suite in project (vanilla HTML, no test runner)

next_action: RESOLVED — human verification passed 2026-08-27

human_verification:
  result: PASS
  date: 2026-08-27
  checks_confirmed:
    - Custom preset button highlights correctly when selected
    - Mode indicator shows the preset name (not the internal ID)
    - Deleting the active custom preset highlights Relax as fallback
  commit: 8d06b94

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

- timestamp: 2026-08-27
  checked: git show 8d06b94 (fix(09-05): correct preset selection UI and layout)
  found: Commit message says "Fixes G-09-4a, G-09-4b, G-09-5a, G-09-5b, G-09-6a, G-09-6b, G-09-6c, G-09-23". Diff shows: (1) updateModeIndicator() changed from `el.textContent = MODE_LABELS[activePresetKey] || activePresetKey` to if/else with customPresets.find() lookup; (2) both click handlers changed from `b === btn` to `b.dataset.preset === activePresetKey`
  implication: All three bugs fixed by this single commit applied 2026-08-25 outside this debug session

- timestamp: 2026-08-27
  checked: Subsequent commits b5fa8f1 and 43e935e
  found: b5fa8f1 added querySelectorAll toggle to save and delete handlers (G-09-5, G-09-21). 43e935e cleared built-in active class on localStorage load (CR-01). Neither reverts 8d06b94's fixes.
  implication: Fix is stable; no regression from later commits

## Resolution

root_cause: Two independent causes (AND-gate: no — distinct symptoms):
  1. Stale DOM reference in click handler toggle: both customPresetsContainer and presetsEl handlers called renderCustomPresets() (which destroys and recreates custom buttons), then compared `b === btn` where `btn` was the OLD element. After recreation, no new element === old element, so all buttons lost "active" class.
  2. updateModeIndicator() used `MODE_LABELS[activePresetKey] || activePresetKey` fallback — for unknown keys (custom preset IDs) it displayed the raw key string instead of looking up the preset's .name property.

fix: Applied in commit 8d06b94 (2026-08-25):
  1. Both click handlers: `b === btn` → `b.dataset.preset === activePresetKey` (value-based comparison survives element recreation)
  2. updateModeIndicator(): added else branch with `customPresets.find(p => p.id === activePresetKey)` lookup
  3. Added CSS for #customPresetsContainer flex layout (cosmetic; same commit)

verification:
  - Code trace confirms old `b === btn` was always false for recreated elements
  - Code trace confirms new `b.dataset.preset === activePresetKey` correctly identifies active button regardless of element recreation
  - All six reported UAT IDs listed in 8d06b94 commit message as fixed
  - Signal 3 (no-op detector): PASS — behavioral correction, not deletion
  - Signal 5 (revert-confirm): PASS via code trace
  - Signals 1, 2, 4: DEGRADED — no automated test suite

files_changed:
  - index.html (commit 8d06b94: updateModeIndicator, two click handlers, CSS)
