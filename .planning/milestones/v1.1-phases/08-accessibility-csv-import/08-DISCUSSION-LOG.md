# Phase 8: Accessibility & CSV Import - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-07-20
**Phase:** 8-Accessibility & CSV Import
**Areas discussed:** Focus indicator style, ARIA live announcement scope, CSV import entry point, Focus management in panels

---

## Focus Indicator Style

| Option | Description | Selected |
|--------|-------------|----------|
| Use --accent color | Focus ring matches warm theme (gold/teal). Keeps visual cohesion. Needs 3:1 contrast verification. | ✓ |
| Browser-default blue | Simple — remove outline:none. Jarring against warm palette. | |
| Fixed high-contrast color | Hardcoded accessible blue — consistent but ignores theme. | |

**User's choice:** Use `--accent` color

| Option | Description | Selected |
|--------|-------------|----------|
| outline: 2px solid --accent + outline-offset: 3px | Native CSS outline, respects border-radius, outside bounds. Standard pattern. | ✓ |
| box-shadow: 0 0 0 3px --accent | Renders around element. Can be clipped by overflow:hidden. | |
| Both (outline for square, box-shadow for round) | More precise, more complex. | |

**User's choice:** `outline: 2px solid var(--accent)` + `outline-offset: 3px`

| Option | Description | Selected |
|--------|-------------|----------|
| :focus-visible only | Ring shows on keyboard nav, not mouse clicks. Modern standard. | ✓ |
| :focus (all events) | Ring on keyboard and mouse. Looks like visual bug on mouse click. | |

**User's choice:** `:focus-visible` only

**Notes:** All three sub-questions chose the recommended option. User wants a clean, cohesive focus indicator that's keyboard-only.

---

## ARIA Live Announcement Scope

| Option | Description | Selected |
|--------|-------------|----------|
| Announce phase changes + start/stop | Guides screen reader users through breathing. aria-live="polite". | ✓ |
| Announce start/stop only | Minimal — screen reader users can't follow phases without looking. | |
| Announce everything (phase, cycle, elapsed) | Maximum verbosity — unusable with per-second updates. | |

**User's choice:** Phase changes (Inhale/Hold/Exhale) + start/stop

| Option | Description | Selected |
|--------|-------------|----------|
| aria-live="polite" | Waits until current speech finishes. Appropriate for non-critical updates. | ✓ |
| aria-live="assertive" | Interrupts immediately. Too disruptive for routine phase changes. | |

**User's choice:** `aria-live="polite"`

| Option | Description | Selected |
|--------|-------------|----------|
| Add aria-live directly to #phaseEl | Simplest — existing element already updates on phase change. | ✓ |
| Separate visually-hidden aria-live region | More JS control but adds element and complexity. | |
| You decide | Let planner pick. | |

**User's choice:** Add `aria-live="polite"` directly to `#phaseEl`

**Notes:** Straightforward — user wants minimal implementation footprint for ARIA.

---

## CSV Import Entry Point

| Option | Description | Selected |
|--------|-------------|----------|
| Upgrade existing import button (accept .json,.csv) | One button, one interaction. Handler detects extension and routes. | ✓ |
| Separate 'Import CSV' button | Two buttons — explicit but cluttered. | |

**User's choice:** Upgrade existing import button

| Option | Description | Selected |
|--------|-------------|----------|
| Strict: parse exactly app's export format | Safe, predictable, no edge-case surprises. | ✓ |
| Flexible: handle minor variations | More forgiving but complex; RFC 4180 edge cases were deferred from v0.6 for this reason. | |

**User's choice:** Strict parsing only

| Option | Description | Selected |
|--------|-------------|----------|
| Flash banner with counts ("Imported 5, 2 skipped") | Matches existing JSON import pattern. Consistent UX. | ✓ |
| Summary dialog listing skipped rows | More detail but requires new dialog. Overkill for this scale. | |

**User's choice:** Flash banner with import/skip counts

| Option | Description | Selected |
|--------|-------------|----------|
| Yes — import incomplete sessions if they parse | Round-trip fidelity: exports include incomplete sessions, imports should too. | ✓ |
| No — skip incomplete sessions | Simpler parser. Loses data on export/import round-trip. | |

**User's choice:** Import incomplete sessions if parseable

**Notes:** User wants clean round-trip fidelity (export → import brings back all data) with minimal UI change.

---

## Focus Management in Panels

| Option | Description | Selected |
|--------|-------------|----------|
| Move focus to the panel's close button | Standard accessible pattern. User knows they're in panel, can Tab forward. | ✓ |
| Move focus to first focusable element | Slightly efficient but may skip close button. | |
| Keep focus where it was | Not accessible — keyboard users won't know panel opened. | |

**User's choice:** Move focus to close button on open

| Option | Description | Selected |
|--------|-------------|----------|
| Trap focus inside the panel | Standard for modal-like overlays. Return focus to opener on close. | ✓ |
| Just move focus — no trap | Focus can escape to underlying page. Technically incorrect per ARIA APG. | |
| You decide | Let researcher/planner determine. | |

**User's choice:** Full focus trap

| Option | Description | Selected |
|--------|-------------|----------|
| Yes — disable global shortcuts while any panel open | Prevents accidental session start while reading settings. ESC still closes. | ✓ |
| No — shortcuts work everywhere | Confusing; Space could start session while typing in settings. | |

**User's choice:** Disable Space/R/F while panel is open; ESC closes panel

**Notes:** User wants full accessible panel behavior — focus trap, return focus, disabled shortcuts. Standard ARIA modal pattern.

---

## Claude's Discretion

- **Tab order strategy (D-06):** User said "no explicit tabindex unless needed" — researcher to audit which elements are currently non-focusable and flag for planner.
- **CSV incomplete flag (D-14):** Researcher to check current CSV export format to confirm whether `incomplete` flag is written to CSV columns or only to JSON export. Determines whether parser needs to handle it.

## Deferred Ideas

None — discussion stayed within phase scope.
