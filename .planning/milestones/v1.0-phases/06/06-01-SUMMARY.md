---
phase: 06
plan: 01
subsystem: css-js-ui
status: complete
tags: [visual-polish, bug-fix, css, layout, versioning]
dependency_graph:
  requires: []
  provides: [VISUAL-01, LAYOUT-01, INFO-01]
  affects: [index.html]
tech_stack:
  added: []
  patterns: [css-custom-properties, css-media-query, js-dom-injection]
key_files:
  modified:
    - index.html
decisions:
  - "Used 'background' shorthand (not 'background-color') in body transition to animate CSS custom property changes reliably (D-10, D-13)"
  - "Added height: auto + min-height: 100vh to compact landscape body to override base 100vh constraint (D-14, D-15)"
  - "APP_VERSION injected via JS at init time — HTML element starts empty, constant is the single source of truth (D-16)"
metrics:
  duration: "~8 minutes"
  completed: "2026-07-04"
  tasks_completed: 3
  tasks_total: 3
  files_changed: 1
---

# Phase 06 Plan 01: CSS/HTML Bug Fixes & Version Display Summary

**One-liner:** Three targeted index.html fixes — smooth background transition via `background` shorthand, compact landscape scroll fix via `height: auto`, and `APP_VERSION` constant injected into info panel footer.

---

## Tasks Completed

| # | Task | Commit | Files |
|---|------|--------|-------|
| 1 | Extend body CSS transition to 'background' for smooth phase animation (VISUAL-01) | 6d4f4e8 | index.html |
| 2 | Add height: auto and min-height: 100vh to compact landscape body (LAYOUT-01) | 344d8f9 | index.html |
| 3 | Add APP_VERSION constant and inject version into info panel footer (INFO-01) | a55afc6 | index.html |

---

## What Was Built

### Task 1 — Smooth Background Transition (VISUAL-01)

Changed the body CSS `transition` property from:
```css
transition: background-color 220ms ease, color 220ms ease;
```
to:
```css
transition: background 220ms ease, color 220ms ease;
```

`applyThemeForCurrentPhase()` updates the `--bg` CSS custom property. The body uses `background: var(--bg)`. Transitioning `background-color` (a sub-property) fails to animate when the parent shorthand changes; using `background` is unambiguous and reliable.

### Task 2 — Compact Landscape Height Fix (LAYOUT-01)

Added `height: auto;` and `min-height: 100vh;` to the body block inside `@media (max-height: 500px) and (orientation: landscape)`. The base body rule has `height: 100vh` which clamps the body even when content exceeds viewport height. The compact landscape layout (ring + controls + history) can exceed 430px on tall phones; the override allows natural document scroll while `min-height: 100vh` preserves centering for short content.

### Task 3 — App Version Display (INFO-01)

Four additions in index.html:
- `const APP_VERSION = "1.0";` — scalar constant in CONFIG section after DURATION_RANGE
- `.infoPanelVersion { margin: 12px 0 0; font-size: clamp(10px, 1vw + 7px, 12px); color: var(--panelSubtext); text-align: right; }` — CSS class matching info panel subtext style
- `<p id="appVersionEl" class="infoPanelVersion"></p>` — empty element inside `.infoPanel` div after last infoSection
- `const verEl = document.getElementById("appVersionEl"); if (verEl) verEl.textContent = "v" + APP_VERSION;` — JS init injection

---

## Deviations from Plan

None — plan executed exactly as written.

---

## Known Stubs

None. All three changes are fully wired with no placeholder values in the data flow path.

---

## Threat Flags

None. As documented in the plan threat model:
- `APP_VERSION` is client-side only and intentionally public (T-06-01: accepted)
- `renderHistory()` has no new injection surface in this plan (T-06-02: accepted)

---

## Self-Check

Files created/modified:
- [x] index.html — modified (all three changes applied)

Commits:
- [x] 6d4f4e8 — fix(06-01): extend body CSS transition
- [x] 344d8f9 — fix(06-01): compact landscape height fix
- [x] a55afc6 — feat(06-01): APP_VERSION and version display

## Self-Check: PASSED
