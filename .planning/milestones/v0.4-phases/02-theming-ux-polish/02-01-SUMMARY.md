---
phase: 02-theming-ux-polish
plan: "01"
subsystem: ui
tags: [css-custom-properties, theme, light-dark, localStorage, 220ms-transition, wcag-aa]

# Dependency graph
requires:
  - phase: 01-session-history-vibration
    provides: localStorage saveSettings/loadSettings patterns, CSS custom property system (--bg, --accent, --text, --card, --accentSoft)
provides:
  - Light theme CSS custom properties (#f5f1ed cream bg, #5c4033 warm brown text, #d4a574 gold accent)
  - toggleTheme() handler with LIGHT_THEME/DARK_THEME frozen color objects
  - isDarkMode state variable persisted in localStorage under STORAGE_KEY
  - Theme toggle button (sun/moon icon) in cornerControls next to sound/vibration toggles
  - applyThemeColors() batch-setter for all 6 CSS custom properties
  - updateThemeToggleUI() for icon sync on load and toggle
affects: [02-02-typography, 02-03-landscape, 02-04-testing]

# Tech tracking
tech-stack:
  added: []
  patterns:
    - "Frozen theme constant objects (LIGHT_THEME/DARK_THEME) with all CSS variable values"
    - "applyThemeColors() pattern: batch document.documentElement.style.setProperty() calls"
    - "isDarkMode boolean in STATE section, persisted to localStorage STORAGE_KEY"
    - "220ms CSS transitions on background, color, border-color, stroke across all themed elements"

key-files:
  created: []
  modified:
    - index.html

key-decisions:
  - "Theme toggle button placed in cornerControls (next to sound/vibration icon toggles) rather than settings overlay panel — keeps all instant-action icon buttons in one location"
  - "isDarkMode state variable in STATE section (line 1211) ensures loadSettings() and saveSettings() can reference it without temporal dead zone issues"
  - "LIGHT_THEME/DARK_THEME as Object.freeze() constants consistent with PRESETS/SOUND/VIBRATION pattern"
  - "applyThemeColors() resets all 6 custom properties atomically — bg, card, accent, accentSoft, text, textSoft"
  - "Dark theme default (isDarkMode=true) preserved; fallback in loadSettings handles missing field for backward compatibility"

patterns-established:
  - "Theme swap: batch setProperty() on document.documentElement (not body or class toggle)"
  - "Persistence: isDarkMode boolean in main STORAGE_KEY JSON object"
  - "Icon button as emoji/HTML entity in .iconToggle button for zero-dependency icons"

requirements-completed: [THEME-01, THEME-02]

# Metrics
duration: 5min
completed: 2026-06-05
---

# Phase 2 Plan 01: Light/Dark Theme Toggle Summary

**CSS custom property-based light/dark theme toggle with warm cream/gold palette, 220ms smooth transitions, and isDarkMode persistence in localStorage**

## Performance

- **Duration:** ~5 min
- **Started:** 2026-06-05T08:02:47Z
- **Completed:** 2026-06-05T08:07:15Z
- **Tasks:** 3 auto tasks completed (1 checkpoint:human-verify pending)
- **Files modified:** 1

## Accomplishments

- Defined 6 light theme CSS variables in :root (--bg-light: #f5f1ed cream, --card-light: #ebe7e1, --accent-light: #d4a574 warm gold, --text-light: #5c4033 warm brown, --textSoft-light: #a89e8c, --accentSoft-light)
- Implemented toggleTheme() handler with LIGHT_THEME/DARK_THEME frozen objects, batch setProperty(), icon update, and saveSettings() call
- Extended saveSettings/loadSettings to persist and restore isDarkMode with dark-default fallback for backward compatibility
- Added --textSoft dark theme variable and CSS color/background transitions on body and container for smooth 220ms theme fade

## Task Commits

Each task was committed atomically:

1. **Task 1: Define Light Theme CSS Custom Properties** - `813c95e` (feat)
2. **Task 2: Implement Theme Toggle Handler and Settings Panel Integration** - `b029289` (feat)
3. **Task 3: Extend Settings Persistence (saveSettings/loadSettings)** - `895e160` (feat)

## Files Created/Modified

- `index.html` - Added light theme :root variables, theme toggle button in cornerControls, toggleTheme()/applyThemeColors()/updateThemeToggleUI() functions, isDarkMode state variable, saveSettings/loadSettings extensions, color/background CSS transitions

## Decisions Made

- Theme toggle button placed in `cornerControls` div (alongside sound/vibration icon toggles) rather than inside the settings overlay panel. CONTEXT.md says "settings panel with sound/vibration toggles" — the sound/vibration icons ARE in cornerControls, making this the natural colocation point.
- Used HTML entity `&#9728;` (sun) for light indication and `&#9790;` (crescent) for dark, avoiding external icon dependencies.
- `isDarkMode` state variable declared in STATE section (before saveSettings) to avoid temporal dead zone issues with `const` in THEME TOGGLE section.
- `applyThemeColors()` is a function declaration (hoisted) called from loadSettings safely despite being defined later in file.

## Deviations from Plan

### Auto-fixed Issues

**1. [Rule 2 - Missing Critical] Added --textSoft CSS custom property**
- **Found during:** Task 1 (Define Light Theme CSS Custom Properties)
- **Issue:** Plan references `--textSoft` in light theme colors but no `--textSoft` existed in dark theme. Adding only the light variant without the dark variant would leave an undefined variable reference.
- **Fix:** Added `--textSoft: rgba(255,255,255,0.55)` to :root dark defaults and `--textSoft-light: #a89e8c` as light variant. applyThemeColors() includes textSoft in both LIGHT_THEME and DARK_THEME objects.
- **Files modified:** index.html
- **Committed in:** 813c95e (Task 1 commit)

**2. [Rule 2 - Missing Critical] Added color and background transitions to body and .container**
- **Found during:** Task 3 (Extend Settings Persistence)
- **Issue:** body had only `transition: background 220ms ease` — text color changes via `color: var(--text)` on body would be instant (no transition). .container background used `--card` with no transition.
- **Fix:** Added `color 220ms ease` to body transition; added `background 220ms ease` to .container transition.
- **Files modified:** index.html
- **Committed in:** 895e160 (Task 3 commit)

---

**Total deviations:** 2 auto-fixed (both Rule 2 — missing critical functionality)
**Impact on plan:** Both fixes necessary for complete smooth 220ms transition per plan requirements. No scope creep.

## Issues Encountered

None — implementation proceeded without blocking issues.

## Known Stubs

None — theme toggle fully wired: button renders, handler toggles colors, persistence saves/loads.

## Threat Flags

No new threat surface introduced. Theme toggle reads/writes only isDarkMode boolean in localStorage. No network requests, no user input beyond button click, no new APIs.

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness

- Light/dark theme toggle functional and persistent — ready for Plan 02-02 (responsive typography)
- applyThemeColors() pattern established for any future theme expansion
- CSS custom property system now covers 6 properties (bg, card, accent, accentSoft, text, textSoft)
- Checkpoint:human-verify pending — user must visually confirm smooth transition, persistence, and ring color adaptation before this plan is marked complete

## Self-Check: PASSED

- `index.html` modified: confirmed (3 commits on release-0-5)
- Commits 813c95e, b029289, 895e160: confirmed in git log
- isDarkMode count: 8 occurrences (saveSettings, loadSettings x2, state variable, toggleTheme x2, updateThemeToggleUI)
- Light theme colors present: 8 matches for f5f1ed|5c4033|a89e8c|d4a574
- SVG ring stroke: `stroke: var(--accent)` confirmed
- 220ms transitions: 8 matching lines confirmed

---
*Phase: 02-theming-ux-polish*
*Completed: 2026-06-05*
