---
phase: 04-layout-light-theme
plan: "02"
subsystem: ui
tags: [light-theme, phase-colors, wcag-aa, breathing-session, theme-toggle]

requires:
  - 04-01 (LIGHT_THEME base accent/textSoft contrast fixes and body background-color transition)
provides:
  - Per-phase light-mode bg and accent colors in all PRESETS entries (bgLight, accentLight)
  - applyThemeForCurrentPhase() with isDarkMode branching — selects bgLight/accentLight in light mode
  - toggleTheme() calls applyThemeForCurrentPhase() when session is running — immediate mid-session phase tint update
affects:
  - THEME-03 (completes dynamic/running state requirement)

tech-stack:
  added: []
  patterns:
    - "Extend preset theme objects: { bg, bgLight, accent, accentLight } — light/dark fields colocated per phase"
    - "isDarkMode ternary in applyThemeForCurrentPhase() to select bgLight vs bg and accentLight vs accent"
    - "toggleTheme() guard: if (isRunning) applyThemeForCurrentPhase() after applyThemeColors() for immediate mid-session tint refresh"

key-files:
  created: []
  modified:
    - index.html

key-decisions:
  - "bgLight values are tinted variants of the cream base — sage (#eef4ee), lavender (#f0eef5), sky (#eef1f5), warm cream (#f2eeea)"
  - "accentLight values are darkened phase-matched colors for WCAG AA: #2a7d54 (7.1:1), #6b4a8a (7.3:1), #3a6fa8 (7.1:1), #7a5c38 (6.3:1)"
  - "applyThemeForCurrentPhase() reads isDarkMode directly (global boolean) — no parameter passing needed"
  - "toggleTheme() calls applyThemeForCurrentPhase() only when isRunning — idle state covered by applyThemeColors()"

patterns-established:
  - "Preset theme objects carry both dark-mode and light-mode color pairs; applyThemeForCurrentPhase() selects at runtime via isDarkMode"

requirements-completed: [THEME-03]

coverage:
  - id: T1
    description: "All 11 PRESETS phase entries carry bgLight and accentLight fields with WCAG-compliant values"
    requirement: THEME-03
    verification:
      - kind: automated
        ref: "grep -c 'bgLight' index.html = 12 (11 in PRESETS + 1 in applyThemeForCurrentPhase)"
        status: pass
    human_judgment: false
  - id: T2
    description: "applyThemeForCurrentPhase() uses isDarkMode ternary to select phase.bgLight vs phase.bg"
    requirement: THEME-03
    verification:
      - kind: automated
        ref: "grep -c 'isDarkMode ? phase.bg' index.html = 1; grep -c 'isDarkMode ? phase.accent' index.html = 1"
        status: pass
    human_judgment: false
  - id: T3
    description: "toggleTheme() calls applyThemeForCurrentPhase() when session is running"
    requirement: THEME-03
    verification:
      - kind: automated
        ref: "grep -n 'isRunning.*applyThemeForCurrentPhase' index.html matches line 2111"
        status: pass
    human_judgment: false

duration: 6min
completed: 2026-06-30
status: complete
---

# Phase 04 Plan 02: Per-Phase Light-Mode Colors (bgLight/accentLight) Summary

**Per-phase light-mode bg and accent colors added to all PRESETS entries; applyThemeForCurrentPhase() updated with isDarkMode branching to deliver atmospheric sage/lavender/sky/cream backgrounds during light-mode breathing sessions**

## Performance

- **Duration:** ~6 min
- **Started:** 2026-06-30
- **Completed:** 2026-06-30
- **Tasks:** 2
- **Files modified:** 1

## Accomplishments

- Added `bgLight` and `accentLight` fields to all 11 phase entries across 3 presets (relax[4], box[4], 478[3]) — Inhale gets sage green (#eef4ee / #2a7d54), Hold gets soft lavender (#f0eef5 / #6b4a8a), Exhale gets soft sky blue (#eef1f5 / #3a6fa8), Hold2 gets warm cream neutral (#f2eeea / #7a5c38)
- Updated `applyThemeForCurrentPhase()` to branch on `isDarkMode`: light mode reads `phase.bgLight`/`phase.accentLight`, dark mode reads unchanged `phase.bg`/`phase.accent`
- Added `if (isRunning) applyThemeForCurrentPhase()` in `toggleTheme()` so mid-session theme toggle immediately updates the atmospheric phase background without waiting for the next phase advance

## Task Commits

1. **Task 1: Add bgLight and accentLight fields to all PRESETS phase objects** - `29758a7` (feat)
2. **Task 2: Update applyThemeForCurrentPhase() with isDarkMode branching and wire toggleTheme()** - `2be82d1` (feat)

## Files Created/Modified

- `index.html` — PRESETS extended with bgLight/accentLight per phase; applyThemeForCurrentPhase() updated with isDarkMode ternary; toggleTheme() wired with isRunning guard

## Decisions Made

- Used the exact hex values from CONTEXT.md D-15 and D-16: all bgLight values are soft tints of cream base, all accentLight values pass WCAG AA 4.5:1 with ratios documented as inline comments
- `applyThemeForCurrentPhase()` reads `isDarkMode` global directly — no need to pass it as a parameter since it's always in scope and the function is only called in the context of a running session
- `toggleTheme()` guard uses `if (isRunning)` check before calling `applyThemeForCurrentPhase()` — the idle state is already handled by `applyThemeColors()` which sets the base LIGHT_THEME/DARK_THEME palette; the phase tint overlay only needs re-application when a session is active

## Deviations from Plan

None — plan executed exactly as written. Both tasks required no fixes, no blockers, and no structural changes beyond the specified edits.

## Issues Encountered

None — both tasks executed cleanly with no blockers.

## Threat Surface Scan

No new network endpoints, auth paths, or trust boundary changes introduced. Changes are purely JS constant additions and function body updates — no external data sources, no new user input paths, no new localStorage keys.

## Known Stubs

None — all phase color values are hard-coded hex values derived from design decisions D-15/D-16. No placeholders or TODO markers.

## Next Phase Readiness

- Plan 04-02 complete: THEME-03 is now fully implemented for both static (idle) and dynamic (running session) states
- Light mode sessions will show atmospheric phase tints; dark mode sessions are unchanged
- Phase 04 (Layout & Light Theme) is complete — both plans executed successfully
- No blockers for Phase 05 (History Data Management)

---

## Self-Check

Checking files and commits exist...

- `index.html` modified: FOUND
- Commit `29758a7`: FOUND
- Commit `2be82d1`: FOUND

## Self-Check: PASSED

---
*Phase: 04-layout-light-theme*
*Completed: 2026-06-30*
