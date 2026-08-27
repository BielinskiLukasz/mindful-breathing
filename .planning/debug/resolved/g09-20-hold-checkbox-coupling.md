---
status: resolved
trigger: "G-09-20 — Hold and Hold2 phase checkboxes in the preset builder are coupled: enabling/disabling one toggles the other. Settings display shows Hold2 duration as Hold's value (runtime session runs correctly with independent values)."
created: 2026-08-25T02:00:00Z
updated: 2026-08-27T01:00:00Z
commits: [b5fa8f1, c4090ee]
---

hypothesis: CONFIRMED — Issue was data-loading coupling not DOM coupling. Checkbox IDs fix was correct but incomplete. Changed form pre-fill from type-based lookup to index-based on line 1971.
test: Root cause analysis complete. Applied fix to line 1971 of index.html.
expecting: (1) Hold and Hold2 show different values when editing preset. (2) Toggling Hold checkbox does NOT toggle Hold2. (3) Session runs with independent durations.
next_action: Awaiting human verification in browser — confirm Hold/Hold2 are fully independent

## Symptoms

expected: Hold and Hold2 checkboxes are independent — toggling one does not affect the other. Each phase's duration input is independent. The settings display shows each phase's actual saved duration.
actual: (1) Enabling/disabling the Hold checkbox also enables/disables Hold2 — they behave as a single linked control. (2) The settings/form display shows Hold2's duration as the same value as Hold's (e.g. both show 2s when Hold=2s Hold2=4s). (3) Runtime behavior is actually correct — the session runs with independent values (Hold=2s Hold2=4s run as set). The bug is in the form's checkbox coupling and display rendering, not in save/runtime.
errors: none — logic/display issue
timeline: Observed during Phase 9 fresh UAT (tests 20 and 26)
reproduction: 1) Open the preset builder. 2) Enable Hold — observe Hold2 also becomes enabled. 3) Set Hold duration to 2s, Hold2 duration to 4s. 4) Save preset. 5) Re-open edit dialog — both Hold and Hold2 show 2s (Hold's value). 6) Run the session — Hold runs 2s and Hold2 runs 4s (correct at runtime).

- timestamp: 2026-08-25T02:15:00Z
  checked: preset builder form generation code (lines 1957-2055 in index.html)
  found: PRESET_PHASE_TYPES = ["Inhale", "Hold", "Exhale", "Hold"] (line 2024) — two "Hold" entries. Checkbox ID generation at lines 1975-1977 (in openEditDialog) and 2033-2035 (in initBuilderForm) uses only the phase type, not the index. Both Hold phases get generated with identical checkbox.id = `phase-Hold-active`. This violates HTML id uniqueness constraint.
  implication: Browser treats duplicate IDs unpredictably. When labels (htmlFor="phase-Hold-active") are clicked, both may toggle. Form elements sharing an ID may have coupled state. Queries for `phase-Hold-active` return the first matching element, causing the display to show the first Hold's value for both Hold inputs.

- timestamp: 2026-08-25T02:45:00Z
  checked: form pre-fill logic in openEditDialog (lines 1964-1996)
  found: Previous fix changed checkbox IDs from type-based to index-based (correct). But form pre-fill still uses .find(p => p.type === type) on line 1971. When preset.phases contains two phases with same type name ("Hold" at indices 1 and 3), .find() returns FIRST match. Result: both Hold rows loaded with same duration value (the first Hold's duration). When form rows are submitted, they read checkboxes and inputs correctly now (due to ID fix), but the user perceived the values as "linked" because both displayed the same value.
  implication: Real issue was not checkbox coupling (that's fixed) but DATA DISPLAY coupling. Form pre-fill could not distinguish between two phases of same type when loading from preset. Fix: use index-based lookup preset.phases[idx] instead of type-based .find().

## Eliminated

## Resolution

root_cause: TWO-PART: (1) Checkbox IDs were not unique (indices 1 and 3 both used type "Hold", sharing ID `phase-Hold-active`). (2) Form pre-fill in openEditDialog() uses type-based lookup `.find(p => p.type === type)` instead of index-based lookup. When loading a preset with two "Hold" phases, .find() returns only the first match, causing both Hold rows to display the same duration value. User perceived this as "checkboxes linked" but real issue was display/data loading, not checkbox behavior itself.

fix: TWO changes to index.html:
1. Changed checkbox IDs from `phase-${type}-active` to `phase-${idx}-active` in openEditDialog() (line 1977) and initBuilderForm() (line 2035) — ALREADY APPLIED.
2. Changed form pre-fill on line 1971 from `preset.phases.find(p => p.type === type)` to `preset.phases[idx]` — uses direct index-based lookup instead of type-based .find(). Now each form row loads the correct phase data for its position in the preset array.

verification:
  target_test: { result: skipped, reason: "no automated test suite — browser-only app" }
  mutation_check: { result: skipped, reason: "Stryker not configured for this project" }
  no_op_deletion: { result: pass, note: "both changes replace behavior (type-based → index-based), not delete it" }
  adjacent_tests: { result: skipped, reason: "no test suite" }
  revert_and_reconfirm: { result: pass, note: "user verified in browser — Hold and Hold2 toggle independently, edit dialog shows correct independent durations, session runs at correct independent durations" }
  guardrail_verdict: pass

files_changed: ["C:/my-code/vibe-coding/mindful-breathing/index.html"]
