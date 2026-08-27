---
phase: quick-260826-tih
plan: "01"
subsystem: css
tags: [css, ux, hover, transition, specificity]
status: complete

dependency_graph:
  requires: []
  provides: [WR-01-fix, WR-02-fix]
  affects: [index.html]

tech_stack:
  added: []
  patterns: [css-specificity-guard, transition-merge]

key_files:
  modified:
    - index.html

decisions:
  - Merged all five transition properties into the hover-media-query button rule rather than relying on cascade additive behaviour (CSS transitions are not additive; the hover block was silently overwriting the base rule)
  - Used button:hover:not(:active) to give :active higher effective specificity without changing selector weight

metrics:
  duration: "5m"
  completed: "2026-08-26"
  tasks_completed: 1
  commits: 1

actuals:
  tokens: 500
  tasks: 1
  commits: 1
---

# Quick Task 260826-tih: Fix Two CSS Hover-State Regressions Summary

**One-liner:** Merged button transition to five properties and guarded hover scale from overriding active press-down on pointer-fine devices.

## What Was Done

Fixed two CSS correctness bugs in the `@media (hover: hover) and (pointer: fine)` block (~line 1254 of `index.html`).

**WR-01 — Transition merge:** The hover-block `button` rule previously listed only `filter 120ms ease, transform 120ms ease`, silently dropping the base rule's `background 0.2s ease, border-color 0.2s ease, color 0.2s ease`. On desktop (pointer-fine) devices, theme switches and preset selections snapped instantly instead of animating. Fixed by listing all five properties in a single declaration.

**WR-02 — Specificity conflict:** `button:hover` and `button:active` had identical specificity; because the hover rule was declared later it overrode the active scale-down, causing a scale-up on click. Fixed by changing the selector to `button:hover:not(:active)`.

## Commits

| Hash | Message |
|------|---------|
| 64b1f28 | fix(ux): merge hover transitions, guard :active from :hover override |

## Verification

```
grep -n "button:hover:not(:active)" index.html
  → 1258:      button:hover:not(:active) {          ✓ (one match)

grep -n "background 0.2s ease, border-color 0.2s ease, color 0.2s ease, filter 120ms ease, transform 120ms ease" index.html
  → 1256:        transition: ...                      ✓ (one match)

grep -n "button:hover {" index.html (excluding not(:active))
  → 370: button:hover { background: var(--cardHover); }  (unrelated rule, expected)
```

Old `button:hover {` selector inside the hover media query is gone.

## Deviations from Plan

None — plan executed exactly as written.

## Known Stubs

None.

## Threat Flags

None. Pure CSS cascade fix, no data boundary or user input involved.

## Self-Check: PASSED

- `index.html` modified: confirmed
- Commit `64b1f28` exists: confirmed
- Both grep checks pass: confirmed
- No unrelated lines modified: confirmed
