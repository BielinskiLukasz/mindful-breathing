# Phase 6: Bug Fixes & Visual Polish - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-07-03
**Phase:** 06-bug-fixes-visual-polish
**Areas discussed:** Incomplete sessions, Incomplete styling, Phase bg fade, Landscape layout fix

---

## Incomplete sessions

| Option | Description | Selected |
|--------|-------------|----------|
| Any stop mid-session | Save as incomplete the moment Stop is pressed, even 0 cycles | |
| At least 1 full cycle | Save only if at least one full breathing cycle completed before stopping | ✓ |
| At least 30 seconds | Save only if session ran for 30+ seconds | |

**User's choice:** At least 1 full cycle

---

| Option | Description | Selected |
|--------|-------------|----------|
| Stop only | Only Stop button saves; Reset discards without saving | ✓ |
| Stop and Reset | Both Stop and Reset save an incomplete session | |
| Stop, Reset, and page close | Also save on window beforeunload | |

**User's choice:** Stop only

---

| Option | Description | Selected |
|--------|-------------|----------|
| Elapsed time at stop | durationMs = total elapsed ms when Stop was pressed | ✓ |
| Estimated full session duration | durationMs = what the full session would have taken | |

**User's choice:** Elapsed time at stop

---

| Option | Description | Selected |
|--------|-------------|----------|
| No — countdown stop is never saved | Stopping during 3s countdown treated same as never starting | ✓ |
| Yes, if a prior session was running | Prior session's cycles should count even if stopped during countdown | |

**User's choice:** No — countdown stop is never saved

---

## Incomplete styling

| Option | Description | Selected |
|--------|-------------|----------|
| Muted + italic label | Reduced opacity (0.6) + "Incomplete" italic text | ✓ |
| Strikethrough + badge | Line-through styling + "INCOMPLETE" badge chip | |
| Dashed border left | Dotted/dashed left border, normal opacity | |

**User's choice:** Muted + italic label

---

| Option | Description | Selected |
|--------|-------------|----------|
| Inline suffix | Appended on same line: "● Relax • 2 min • 1 cycle • Today • Incomplete" | ✓ |
| Second line below | "Incomplete" on a second line below the session data | |

**User's choice:** Inline suffix

---

| Option | Description | Selected |
|--------|-------------|----------|
| Inline chronological | Mixed with complete sessions in date order | ✓ |
| Grouped at bottom | Complete sessions first, incomplete below a divider | |

**User's choice:** Inline chronological

---

| Option | Description | Selected |
|--------|-------------|----------|
| Keep preset color dot | Colored dot stays — muted opacity already signals incompleteness | ✓ |
| Neutral grey dot | Replace preset color with grey for incomplete entries | |

**User's choice:** Keep preset color dot

---

## Phase bg fade

| Option | Description | Selected |
|--------|-------------|----------|
| CSS transition on body | Add `transition: background 220ms ease` to body | ✓ |
| JS color interpolation | JS animates between colors over 220ms via requestAnimationFrame | |

**User's choice:** CSS transition on body

---

| Option | Description | Selected |
|--------|-------------|----------|
| Dark mode only | Apply 220ms transition only when isDarkMode is true | |
| Both modes | Apply the same transition in light and dark mode | ✓ |

**User's choice:** Both modes

---

| Option | Description | Selected |
|--------|-------------|----------|
| Also animate | CSS transition fires on theme toggle too — consistent behavior | ✓ |
| Instant on toggle | Suppress transition on theme toggle via JS | |

**User's choice:** Also animate

---

| Option | Description | Selected |
|--------|-------------|----------|
| Unify into one rule | Extend existing body transition declaration | ✓ |
| Add separately | Add a second CSS transition rule | |

**User's choice:** Unify into one rule

---

## Landscape layout fix

| Option | Description | Selected |
|--------|-------------|----------|
| Bug is in CSS rules inside query | Media query condition correct, but styling has issues on certain phones | |
| This may already be fixed | Code may have been corrected in v0.6 without requirement being marked resolved | |
| The 2-column grid is the problem | Interaction issue between compact and 2-column layout | |

**User's choice:** (Free text) — "No idea, only noted that behaviour when test it via chrome mobile view (setted on console)"
**Notes:** Researcher agent to diagnose exact bug. User observed it in Chrome DevTools mobile emulation.

---

| Option | Description | Selected |
|--------|-------------|----------|
| Ring + controls only | Breathing ring and buttons always fully visible; history scrolls or hidden | ✓ |
| Ring + controls + some history | Show ring, controls, and compact 2-3 row history | |

**User's choice:** Ring + controls only

---

| Option | Description | Selected |
|--------|-------------|----------|
| Scrollable below the fold | History accessible via scroll but not immediately visible | ✓ |
| Completely hidden | History is display:none in compact landscape | |

**User's choice:** Scrollable below the fold

---

## Claude's Discretion

- Exact opacity value for muted incomplete rows (0.6 as starting point)
- Exact CSS ease curve for background transition (ease vs ease-in-out)
- Exact placement of version string within info panel footer

## Deferred Ideas

None — discussion stayed within phase scope.
