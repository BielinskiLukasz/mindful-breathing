---
phase: 09-custom-preset-builder
plan: 04
subsystem: custom-preset-builder
tags: [validation-ux, gap-closure, form-handling]
status: complete
dependencies:
  requires: [09-03]
  provides: []
  affects: [custom-preset-builder]
tech_stack:
  patterns: []
  added: []
key_files:
  created: []
  modified:
    - index.html
decisions:
  - "Error messages must be cleared at submit start to prevent persisting across resubmission attempts"
  - "3000ms (3 seconds) timeout for phase count error ensures user has time to read and respond"
metrics:
  duration: "~5 minutes"
  completed_date: "2026-08-24"
  tasks_completed: 2
  files_modified: 1
---

# Phase 09 Plan 04: Validation Message UX Fix

## Objective

Fix validation message UX in the custom preset builder form. Users were unable to see validation errors (disappeared too quickly) and form errors persisted after correcting input.

## What Was Built

Two targeted fixes to the custom preset builder form submit handler in `index.html`:

1. **G-09-3a (Clear name error before re-validating)**: Added `presetNameError.style.display = "none";` immediately after `e.preventDefault();` in the submit handler. This ensures error messages from a previous failed submission are cleared before validation runs again, so users don't see stale errors when they re-submit with a valid name.

2. **G-09-3b (Extend phaseCountError visibility timeout)**: Changed the phaseCountError visibility timeout from 400ms to 3000ms. This gives users 3 seconds to read and respond to the "Select at least 2 phases" validation error instead of having it disappear in less than half a second.

## Root Causes Addressed

- **G-09-3a**: `presetNameError` was not cleared at the start of the submit handler, causing error messages to persist even after the user typed a valid name and resubmitted.
- **G-09-3b**: `setTimeout` duration of 400ms was too brief for users to read the error message before it disappeared.

## Implementation Details

### Task 1: Clear name error on submit

**File:** `index.html` (line 2063)  
**Change:** Added single line clearing the error display

```javascript
presetBuilderForm.addEventListener("submit", e => {
  e.preventDefault();
  presetNameError.style.display = "none";  // NEW LINE
  // ... rest of handler
});
```

**Verification:** `grep -n "presetNameError.style.display = \"none\"" index.html | grep -E "206[0-9]" | wc -l` returned `1` ✓

### Task 2: Extend phaseCountError timeout

**File:** `index.html` (line 2082)  
**Change:** Updated setTimeout duration

```javascript
// Before:
setTimeout(() => phaseCountError.style.display = "none", 400);

// After:
setTimeout(() => phaseCountError.style.display = "none", 3000);
```

**Verification:** `grep -n "phaseCountError.style.display = \"none\", 3000" index.html | wc -l` returned `1` ✓

## Testing & Verification

Both changes have been committed and verified by the grep patterns defined in the plan:
- ✓ Task 1 verification: 1 occurrence of clear line in correct range (2060–2069)
- ✓ Task 2 verification: 1 occurrence of 3000ms timeout

**Manual verification steps** (to be performed during UAT):
1. Open app → Click '+' button to open preset builder
2. Leave name empty, click Save → "Name required" error appears
3. Type a name, click Save → error disappears (G-09-3a fix verified)
4. Clear name, click Save again → error appears (confirms clearing on each submit)
5. Uncheck all phases except one, click Save → "At least 2 phases required" appears
6. Wait 3+ seconds → error remains visible (G-09-3b fix verified)

## Deviations from Plan

None — plan executed exactly as written. Both fixes applied with no additional changes or corrections needed.

## Key Decisions

- **Error clearing on submit start**: Ensures form state is clean before revalidation, preventing error carryover
- **3-second timeout**: Balances UX (readable duration) with not leaving errors on screen indefinitely
- **No additional validation changes**: Scope limited to timing and display clearing only

## Known Stubs

None.

## Threat Flags

None — changes are internal form validation timing with no security implications.

## Commits

| Commit | Message | Files |
|--------|---------|-------|
| `1651267` | fix(09-04): clear validation errors and extend timeout for readability | index.html |

## Summary

This plan closes the gap identified in UAT for validation message UX. The two small but critical fixes ensure users can see validation errors clearly and that form state doesn't carry error messages across resubmission attempts. Both changes are low-risk, focused interventions with clear verification criteria.
