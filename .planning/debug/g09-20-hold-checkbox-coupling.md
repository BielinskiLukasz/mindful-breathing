---
status: awaiting_human_verify
trigger: "G-09-20 — Hold and Hold2 phase checkboxes in the preset builder are coupled: enabling/disabling one toggles the other. Settings display shows Hold2 duration as Hold's value (runtime session runs correctly with independent values)."
created: 2026-08-25T02:00:00Z
updated: 2026-08-25T02:00:00Z
---

## Current Focus

hypothesis: Checkbox IDs are not unique — both Hold phases (indices 1 and 3 in PRESET_PHASE_TYPES) share the same ID `phase-Hold-active`. This violates HTML id uniqueness constraint, causing browser to treat them as coupled.
test: Code inspection + fix applied
expecting: Both Hold phases have identical checkbox IDs before fix, but fix makes them unique using index
next_action: CHECKPOINT — Request human verification of the fix in the live app

## Symptoms

expected: Hold and Hold2 checkboxes are independent — toggling one does not affect the other. Each phase's duration input is independent. The settings display shows each phase's actual saved duration.
actual: (1) Enabling/disabling the Hold checkbox also enables/disables Hold2 — they behave as a single linked control. (2) The settings/form display shows Hold2's duration as the same value as Hold's (e.g. both show 2s when Hold=2s Hold2=4s). (3) Runtime behavior is actually correct — the session runs with independent values (Hold=2s Hold2=4s run as set). The bug is in the form's checkbox coupling and display rendering, not in save/runtime.
errors: none — logic/display issue
timeline: Observed during Phase 9 fresh UAT (tests 20 and 26)
reproduction: 1) Open the preset builder. 2) Enable Hold — observe Hold2 also becomes enabled. 3) Set Hold duration to 2s, Hold2 duration to 4s. 4) Save preset. 5) Re-open edit dialog — both Hold and Hold2 show 2s (Hold's value). 6) Run the session — Hold runs 2s and Hold2 runs 4s (correct at runtime).

## Evidence

- timestamp: 2026-08-25T02:15:00Z
  checked: preset builder form generation code (lines 1957-2055 in index.html)
  found: PRESET_PHASE_TYPES = ["Inhale", "Hold", "Exhale", "Hold"] (line 2024) — two "Hold" entries. Checkbox ID generation at lines 1975-1977 (in openEditDialog) and 2033-2035 (in initBuilderForm) uses only the phase type, not the index. Both Hold phases get generated with identical checkbox.id = `phase-Hold-active`. This violates HTML id uniqueness constraint.
  implication: Browser treats duplicate IDs unpredictably. When labels (htmlFor="phase-Hold-active") are clicked, both may toggle. Form elements sharing an ID may have coupled state. Queries for `phase-Hold-active` return the first matching element, causing the display to show the first Hold's value for both Hold inputs.

## Eliminated

## Resolution

root_cause: Checkbox IDs are not unique. PRESET_PHASE_TYPES contains ["Inhale", "Hold", "Exhale", "Hold"] (two Hold entries). Checkbox ID generation uses only the phase type, not the index, so both Hold phases get identical id `phase-Hold-active`. HTML id uniqueness violation causes browser to couple the checkboxes and causes form queries to return only the first matching element, displaying the first Hold's value for both.
fix: Make checkbox IDs unique by including the phase index. Changed checkbox.id from `phase-${type}-active` to `phase-${idx}-active` in both initBuilderForm() (line 2035) and openEditDialog() (line 1977) functions. Each phase now gets a unique ID: phase-0-active, phase-1-active, phase-2-active, phase-3-active. Label htmlFor attributes automatically reference the correct unique checkbox IDs.
verification: Code inspection confirms fix applied at both locations. ID generation now uses index instead of type, eliminating the duplicate ID that was causing the coupling. No other code references the old ID format.
files_changed: ["C:/my-code/vibe-coding/mindful-breathing/index.html"]
