---
status: resolved
trigger: "G-09-10 — 'Phase durations (sec)' section label remains visible when a custom preset is selected (inputs are correctly hidden, but the label is not)"
created: 2026-08-25T02:00:00Z
updated: 2026-08-27T00:00:00Z
---

## Current Focus

hypothesis: "The buildDurationInputs() function hides the #durations element when a custom preset is active, but does not hide the parent settingsGroup's settingsLabel sibling. The label remains visible as an orphaned heading."
test: "Read HTML structure and buildDurationInputs() function logic in index.html + applied fix + verified in code"
expecting: "Function should hide both the durations container AND the label when custom-* preset is selected"
next_action: resolved

## Symptoms

expected: When a custom preset is selected, the entire duration section is hidden — both the per-phase duration inputs AND the 'Phase durations (sec)' section label disappear.
actual: The per-phase duration inputs (sliders) are correctly hidden when a custom preset is selected, but the 'Phase durations (sec)' section label (settingsLabel) remains visible as an orphaned heading with no content beneath it.
errors: none — layout/visibility issue
timeline: Observed during Phase 9 fresh UAT (test 10)
reproduction: 1) Create a custom preset and select it. 2) Open settings panel. 3) Observe that the duration sliders are gone but the section label "Phase durations (sec)" is still visible.

## Evidence

- timestamp: 2026-08-25T02:05:00Z
  checked: "HTML structure around line 1394-1396 and buildDurationInputs() function lines 1813-1851"
  found: "The buildDurationInputs() function only hides durationsSection (#durations), not the settingsLabel sibling above it. HTML shows: <div class='settingsLabel'>Phase durations (sec)</div> followed by <div class='durations' id='durations'></div>. The label has no display:none applied when custom preset is active."
  implication: "The settingsLabel needs to be hidden alongside the durations container"

## Eliminated

## Resolution

root_cause: "buildDurationInputs() function hides the #durations container when a custom preset is active, but fails to hide the preceding settingsLabel sibling. The label 'Phase durations (sec)' remains visible as an orphaned heading."
fix: "Modified buildDurationInputs() to also hide/show the durationsLabel (previousElementSibling) when toggling custom preset visibility. Added safeguard to check that the previous element is indeed a settingsLabel before applying display:none."
fix_commit: "b5fa8f1 fix(09): resolve 5 custom preset bugs from UAT"
verification: "User verified in browser — with a custom preset active, the label is hidden; switching to a built-in preset (Relax/Box/4-7-8) makes the label reappear."
files_changed: ["index.html"]

## Blameless Postmortem

why_not_caught: "No gate existed for this class of DOM sibling visibility bug — the label and its controlled container are sibling elements with no shared wrapper, so hiding one does not affect the other. No automated test covered the custom-preset settings-panel visibility path."
guard: "Manual UAT checklist item G-09-10 caught this; a future automated UI test for settings panel visibility with custom preset active would prevent regression."
