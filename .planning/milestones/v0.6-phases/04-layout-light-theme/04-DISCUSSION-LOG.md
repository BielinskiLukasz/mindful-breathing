# Phase 4: Layout & Light Theme - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-06-30
**Phase:** 4-Layout & Light Theme
**Areas discussed:** Column split ratio, Breakpoint strategy, Contrast fix scope, Phase colors in light mode

---

## Column Split Ratio

| Option | Description | Selected |
|--------|-------------|----------|
| 50/50 (1fr 1fr) | Equal split. Ring and history panel get the same width. Consistent with the current v0.4 layout. | ✓ |
| Ring larger (3fr 2fr) | Ring column gets 60%, controls get 40%. Emphasizes the ring as focal point. | |
| Adaptive via clamp() | Ring column: clamp(300px, 50%, 600px). Gives ring a min/max on wider screens. | |

**User's choice:** 50/50 (1fr 1fr)
**Notes:** Followed with decision to switch to CSS Grid (not just update breakpoint), keep internal scroll for history, and keep the border-left column divider.

---

## Breakpoint Strategy

| Option | Description | Selected |
|--------|-------------|----------|
| orientation:landscape + min-width:600px | Covers tablets and wide phones in landscape. STATE.md recommendation. | |
| orientation:landscape + min-width:768px | Strict tablet threshold; phones in landscape stay single-column. | |
| orientation:landscape only (any size) | Any landscape device (height >500px) gets 2-column. | ✓ |

**User's choice:** orientation:landscape only (any size)
**Notes:** When clarified, user intent is all landscape devices with height >500px — the REQUIREMENTS.md "tablet-width" phrasing was approximate. The existing LANDSCAPE MOBILE override (max-height:500px) is retained for very short landscape views. Column padding stays at 20px.

---

## Contrast Fix Scope

| Option | Description | Selected |
|--------|-------------|----------|
| Fix only failing values | Minimal change; keep the warm cream/gold feel. | ✓ |
| Redesign the full light palette | Replace all light theme colors with a new WCAG-compliant set. | |
| Let Claude determine the scope | Claude audits each element and fixes only what fails. | |

**User's choice:** Fix only failing values
**Notes:** Accent strategy: darken accent to pass 4.5:1 (single color, no new variable). Document each change with inline contrast ratio comment. Audit ALL visible UI elements (not just text). Strong preference to stay warm browns/golds — no gray or cool-neutral fallbacks.

---

## Phase Colors in Light Mode

| Option | Description | Selected |
|--------|-------------|----------|
| Keep dark phase colors always | Sessions always use atmospheric dark colors regardless of theme. | |
| Light mode stays light during sessions | Disable bg overrides in light mode; only accent changes. | |
| Separate light-mode phase colors | Tinted cream backgrounds (soft sage, lavender, sky blue) per phase. | ✓ |

**User's choice:** Separate light-mode phase colors
**Notes:** Palette direction: tinted cream base. Storage: new `bgLight` and `accentLight` fields per phase preset object. Phase accents also change per phase in light mode. Background transitions use 220ms ease matching the theme toggle.

---

## Claude's Discretion

- Exact hex values for darkened `accent` and `textSoft` — implementer calculates minimum-change values passing 4.5:1 on `#f5f1ed`
- Exact hex values for `bgLight` and `accentLight` per phase — implementer chooses WCAG-compliant warm tints
- Whether `--card` needs adjustment against text contrast in light mode

## Deferred Ideas

None — discussion stayed within phase scope.
