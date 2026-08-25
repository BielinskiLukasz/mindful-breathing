---
status: verifying
trigger: "G-09-5 — After saving a new custom preset, both the previously-selected built-in preset and the new custom preset show the active highlight simultaneously"
created: 2026-08-25T02:00:00Z
updated: 2026-08-25T02:06:00Z
---

## Current Focus

hypothesis: "renderCustomPresets() applies active class to new custom preset (line 1891), but presetBuilderForm submit handler does NOT clear active class from previously-selected built-in preset buttons"
test: "Read presetBuilderForm submit handler (lines 2078-2152) and compare with presetsEl click handler (lines 2963-2989) which correctly does document.querySelectorAll('.presetBtn').forEach(b => b.classList.toggle('active', b.dataset.preset === activePresetKey)) at line 2988"
expecting: "presetBuilderForm handler missing the toggle step that clears active from old buttons"
next_action: "Apply fix: add toggle line after renderCustomPresets() call in presetBuilderForm submit handler"

## Symptoms

expected: After creating a new custom preset, only the new preset button shows the active highlight (green border). The previously-selected built-in preset (e.g. Relax) loses its highlight.
actual: Both the previously-selected preset (e.g. Relax) AND the newly created custom preset show the green active-selection border simultaneously — two presets appear selected at once.
errors: none — functional logic issue
timeline: Observed during Phase 9 fresh UAT (test 5); gap 09-05-PLAN claimed to fix stale button reference using b.dataset.preset === activePresetKey but the bug persists specifically when saving a NEW preset while a built-in preset was previously selected.
reproduction: 1) Start with Relax selected (default). 2) Open the preset builder via '+'. 3) Enter a name, keep phases checked, click Save Preset. 4) Observe both Relax and the new custom preset have the green selection border.

## Evidence

- timestamp: 2026-08-25T02:05:00Z
  checked: "presetBuilderForm submit handler (lines 2078-2152)"
  found: "Line 2140 sets activePresetKey = presetId, line 2146 calls renderCustomPresets() which applies active class to new preset, but there is NO call to document.querySelectorAll('.presetBtn').forEach(...) that would remove active class from previously-selected preset buttons"
  implication: "When savePreset creates a new preset, renderCustomPresets() adds active class to new custom button, but the old built-in preset button (e.g., Relax) still has active class from before the save"

- timestamp: 2026-08-25T02:05:00Z
  checked: "presetsEl click handler (lines 2963-2989) and customPresetsContainer handler (lines 2993-3011)"
  found: "Both handlers DO include line 2988 and 3011: document.querySelectorAll('.presetBtn').forEach(b => b.classList.toggle('active', b.dataset.preset === activePresetKey)); which correctly updates ALL buttons"
  implication: "The presetsEl handlers (for clicking) have the fix, but the presetBuilderForm submit handler (for saving) does NOT"

- timestamp: 2026-08-25T02:05:00Z
  checked: "renderCustomPresets() function (lines 1883-1947)"
  found: "Line 1885 clears innerHTML of customPresetsContainer, then lines 1886-1946 recreate custom preset buttons. Line 1891 applies active class: if (activePresetKey === preset.id) btn.classList.add('active'). Built-in preset buttons are NOT touched (they're static HTML in the HTML template)"
  implication: "renderCustomPresets() can only manage custom preset buttons, not built-in ones. The toggle line on all .presetBtn is necessary to also manage built-in buttons"

## Eliminated

## Resolution

root_cause: "presetBuilderForm submit handler (line 2078-2152) does NOT include the document.querySelectorAll('.presetBtn').forEach(b => b.classList.toggle('active', b.dataset.preset === activePresetKey)) call that exists in presetsEl and customPresetsContainer handlers. When saving a new custom preset, the new preset gets the active class via renderCustomPresets(), but the previously-selected built-in preset button retains its active class, resulting in two buttons appearing selected"
fix: "Added line 2155 after renderCustomPresets() call in presetBuilderForm submit handler: document.querySelectorAll('.presetBtn').forEach(b => b.classList.toggle('active', b.dataset.preset === activePresetKey));"
verification: "Code inspection confirms: (1) Line 2155 now has the toggle call, (2) It's placed after renderCustomPresets() at line 2154, exactly where the presetsEl handler has it at line 2997, (3) Logic is identical across all three handlers (presetBuilderForm, presetsEl, customPresetsContainer), (4) Toggle correctly removes active class from all buttons except the one matching activePresetKey. Expected behavior: Save new preset → activePresetKey changes → renderCustomPresets() adds active to new button → toggle removes active from all others → only new preset button has active class."
files_changed: ["index.html (line 2155)"]
