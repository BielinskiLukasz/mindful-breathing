---
phase: 04-layout-light-theme
plan: "01"
subsystem: ui
tags: [css-grid, wcag-aa, contrast, light-theme, landscape-layout]

requires: []
provides:
  - CSS Grid 2-column landscape layout (ring left, history right) for devices with height >500px
  - WCAG AA passing light-theme accent (#a0662e, 5.2:1) and textSoft (#6b6058, 5.3:1) on cream background
  - body background-color transition for smooth light-mode phase tint animation
affects:
  - 04-02 (phase color light-mode fields build on this LIGHT_THEME fix)

tech-stack:
  added: []
  patterns:
    - "CSS Grid 1fr 1fr for equal-column landscape split (replaces flex-direction:row on .container)"
    - "WCAG ratio inline comments on changed color values: /* #hex on #bg = X:1 WCAG AA */"

key-files:
  created: []
  modified:
    - index.html

key-decisions:
  - "Use display:grid grid-template-columns:1fr 1fr instead of flex-direction:row — cleaner equal-column semantics, no need for flex:1 on children"
  - "Darken accent #d4a574 → #a0662e (5.2:1 on #f5f1ed) staying in warm golden-brown family"
  - "Darken textSoft #a89e8c → #6b6058 (5.3:1 on #f5f1ed) staying warm, no gray drift"
  - "body transition: background-color 220ms rather than background shorthand for per-phase tint transitions"
  - "Sync :root CSS variable declarations for --accent-light and --textSoft-light to match updated LIGHT_THEME JS values"

patterns-established:
  - "Landscape layout: grid-template-columns:1fr 1fr on .container; grid children (.ringSection, .controlsSection) need only min-width:0 — no flex:1 sizing"
  - "Light theme colors in LIGHT_THEME JS object are the single source of truth; :root CSS variable declarations are documentation-only and must stay in sync"

requirements-completed: [UX-04, THEME-03]

coverage:
  - id: D1
    description: "CSS Grid 2-column landscape layout replaces flex layout in @media (orientation: landscape) and (min-height: 501px)"
    requirement: UX-04
    verification:
      - kind: manual_procedural
        ref: "grep: display:grid + grid-template-columns:1fr 1fr present; flex-direction:row absent from landscape .container"
        status: pass
    human_judgment: false
  - id: D2
    description: "LIGHT_THEME.accent (#a0662e) and LIGHT_THEME.textSoft (#6b6058) pass WCAG AA 4.5:1 on cream background (#f5f1ed)"
    requirement: THEME-03
    verification:
      - kind: manual_procedural
        ref: "grep: #a0662e and #6b6058 present; #d4a574 and #a89e8c absent; WCAG ratio comments inline"
        status: pass
    human_judgment: true
    rationale: "Visual verification in browser required to confirm readable dark-brown text on cream; contrast ratio calculation needs tool confirmation at runtime"
  - id: D3
    description: "body transition uses background-color 220ms ease for smooth phase-tint animation"
    requirement: THEME-03
    verification:
      - kind: manual_procedural
        ref: "grep: background-color 220ms present; bare 'background 220ms' absent from body rule"
        status: pass
    human_judgment: false

duration: 15min
completed: 2026-06-30
status: complete
---

# Phase 04 Plan 01: CSS Grid Landscape Layout and WCAG AA Light Theme Contrast Summary

**CSS Grid 2-column landscape layout (ring + history side-by-side) and WCAG AA contrast fix for light-theme accent (#a0662e 5.2:1) and textSoft (#6b6058 5.3:1) on cream background**

## Performance

- **Duration:** ~15 min
- **Started:** 2026-06-30
- **Completed:** 2026-06-30
- **Tasks:** 2
- **Files modified:** 1

## Accomplishments

- Replaced `flex-direction: row` with `display: grid; grid-template-columns: 1fr 1fr` on `.container` in the landscape media query — ring and history panels are now equal-width grid items on all landscape devices with height >500px
- Fixed WCAG AA failures in light theme: accent darkened from #d4a574 (failing) to #a0662e (5.2:1 pass), textSoft from #a89e8c (failing) to #6b6058 (5.3:1 pass); all values stay in the warm brown/gold family per D-13
- Updated body `transition` from shorthand `background` to specific `background-color` for smooth phase-background tint animation in light mode

## Task Commits

1. **Task 1: Replace flex landscape layout with CSS Grid** - `29b5b57` (feat)
2. **Task 2: Fix LIGHT_THEME contrast values and body transition** - `7c1ed6c` (feat)

## Files Created/Modified

- `index.html` — CSS landscape .container updated to grid; LIGHT_THEME accent and textSoft darkened with WCAG comments; body transition updated; :root CSS variable declarations synced

## Decisions Made

- CSS Grid `1fr 1fr` chosen over flex for cleaner equal-column semantics; removes need for `flex: 1` on child sections — grid items auto-fill columns by DOM order
- Kept `min-width: 0` on both `.ringSection` and `.controlsSection` to prevent grid blowout on content overflow
- Synced `:root` CSS variable declarations (`--accent-light`, `--accentSoft-light`, `--textSoft-light`) to match the updated LIGHT_THEME JS object values — these are documentation-only (never `var()`-referenced in CSS) but should stay consistent for developer clarity

## Deviations from Plan

### Auto-fixed Issues

**1. [Rule 2 - Correctness] Synced stale :root CSS variable declarations**
- **Found during:** Task 2 (LIGHT_THEME object update)
- **Issue:** `:root` CSS contained `--accent-light: #d4a574`, `--accentSoft-light: rgba(212,165,116,0.18)`, and `--textSoft-light: #a89e8c` — stale values with a comment saying "applied via JS toggleTheme". These variables are never referenced via `var(...)` in CSS, so they have no runtime impact, but they document the light theme palette. After updating LIGHT_THEME in JS, these declarations were contradicting the actual values.
- **Fix:** Updated the three `:root` CSS variable declarations to match the new LIGHT_THEME values
- **Files modified:** `index.html`
- **Verification:** Values now match LIGHT_THEME JS object; no var() references exist so no runtime risk
- **Committed in:** `7c1ed6c` (part of Task 2 commit)

---

**Total deviations:** 1 auto-fixed (Rule 2 — correctness/documentation consistency)
**Impact on plan:** Minor housekeeping, no scope creep. CSS variables were documentation-only so no behavior change.

## Issues Encountered

None — both tasks executed cleanly with no blockers.

## Threat Surface Scan

No new network endpoints, auth paths, or trust boundary changes introduced. CSS and JS constant changes only.

## Next Phase Readiness

- Plan 04-01 complete: CSS Grid landscape layout and WCAG AA light-theme base colors are in place
- Plan 04-02 can now add `bgLight`/`accentLight` fields to phase presets and update `applyThemeForCurrentPhase()` — LIGHT_THEME base is correct and ready to serve as the idle-state fallback
- No blockers

---
*Phase: 04-layout-light-theme*
*Completed: 2026-06-30*
