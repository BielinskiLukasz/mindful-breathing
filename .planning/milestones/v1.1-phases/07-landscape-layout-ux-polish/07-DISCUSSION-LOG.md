# Phase 7: Landscape Layout & UX Polish - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-07-13
**Phase:** 07-Landscape Layout & UX Polish
**Areas discussed:** Column element placement, Session info row below ring, Controls centering in right column, Hover style + countdown animation

---

## Column Element Placement

### cornerControls behavior in landscape

| Option | Description | Selected |
|--------|-------------|----------|
| Stay absolute, adjust offset | Keep position:absolute but shift offsets to avoid overlap | ✓ |
| Move into left column flow | Make cornerControls static/in-flow inside ringSection | |
| Move into right column flow | Become part of controlsSection header area | |

**User's choice:** Stay absolute, adjust offset

---

### fullscreenBtn placement in landscape

| Option | Description | Selected |
|--------|-------------|----------|
| Move in HTML into ringSection | Place below ringWrap; position:static in landscape | |
| CSS-only via order property | Keep in DOM, use grid placement | |
| Duplicate element | Second button in ringSection, sync via JS | |
| Other (user idea) | Duplicate in both columns with settings for left/right-handed preference | ✓ (partially) |

**User's choice:** First said: duplicate in both sides with a settings option for handedness (left/right). Then clarified for Phase 7 specifically: fullscreenBtn moves INTO the cornerControls group (top-left, both columns share the same single corner).

**Notes:** User had a vision for a future "handedness" setting where fullscreen button can be on either side. For Phase 7, simplified to: merge fullscreenBtn into cornerControls — one cluster, top-left, absolute-positioned.

---

### fullscreenBtn exact landscape position

| Option | Description | Selected |
|--------|-------------|----------|
| Left column only — below ring | Move into ringSection, flow naturally below ring | |
| Both columns — one below ring, one in corner | Duplicate; show in both places | |
| Top of each column (header bar) | A column header bar spans each column | |
| Both inside cornerControls group | Fullscreen joins the existing top-left icon cluster | ✓ |

**User's choice:** Both inside the cornerControls group — all controls in one cluster

---

## Session Info Row Below Ring

### Info row content

| Option | Description | Selected |
|--------|-------------|----------|
| Cycle count + elapsed time | Exactly LAYOUT-04 items | ✓ |
| Cycle count + elapsed + session bar | Add progress bar below | |
| Cycle count + elapsed + phase name | Include current phase label | |

**User's choice:** Cycle count + elapsed time (Recommended)

---

### Cycle count repositioning scope

| Option | Description | Selected |
|--------|-------------|----------|
| Landscape only | Cycle stays above ring in portrait | ✓ |
| Everywhere | Cycle moves below ring in all orientations | |

**User's choice:** Landscape only — cycle stays above ring in portrait

---

### Elapsed time placement approach

| Option | Description | Selected |
|--------|-------------|----------|
| Duplicate — show in left column for landscape, keep right unchanged | Add #elapsedLandscape in ringSection | ✓ |
| Move — bring elapsed into ringSection and hide statusRow in landscape | Physically move the element | |

**User's choice:** Duplicate

---

## Controls Centering in Right Column

### Right column layout arrangement

| Option | Description | Selected |
|--------|-------------|----------|
| Controls centered with history above + status below | History flex:1, controls auto-margin, status at bottom | ✓ |
| Controls at top, history below | Controls order:1, history below | |
| Controls centered — history hidden in landscape | No history in landscape | |

**User's choice:** Controls centered with history above + status below (Recommended)

---

### Definition of "vertically centered"

| Option | Description | Selected |
|--------|-------------|----------|
| Centered between history and status bar | Midpoint of remaining space | |
| Centered within the full column height | 50% from top of column | ✓ |

**User's choice:** Centered within the full column height

---

## Hover Style + Countdown Animation

### Hover visual style

| Option | Description | Selected |
|--------|-------------|----------|
| Subtle lift — brightness + slight scale | filter:brightness(1.08) + scale(1.02), pointer:fine | ✓ |
| Background/border change only | Standardize existing approach | |
| You decide | Claude picks | |

**User's choice:** Subtle lift — brightness + slight scale (Recommended)

---

### Hover scope

| Option | Description | Selected |
|--------|-------------|----------|
| All interactive controls uniformly | Same lift everywhere | ✓ |
| Icon toggles and small buttons only | Large buttons keep background-change hover | |
| Primary buttons stronger, icons subtle | Hierarchy-aware | |

**User's choice:** All interactive controls uniformly

---

### Countdown digit animation

| Option | Description | Selected |
|--------|-------------|----------|
| Keep and refine existing pop animation | countdownPop scale+fade already meets UX-02 | ✓ |
| Slide-up — odometer style | Classic clock animation | |
| Cross-fade only | Simple opacity transition | |

**User's choice:** Existing pop animation is good — keep and refine it

---

## Claude's Discretion

None — user made explicit choices for all areas.

## Deferred Ideas

- **Left/right-handed fullscreen placement toggle**: User wants a settings option so users can choose whether the fullscreen button appears in the left or right column (for left/right-handed preference). Noted for a future phase focused on settings/ergonomics.
