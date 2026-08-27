# Phase 7: Landscape Layout & UX Polish - Context

**Gathered:** 2026-07-13
**Status:** Ready for planning

<domain>
## Phase Boundary

Fix the landscape orientation layout: eliminate element overlaps caused by absolutely-positioned controls floating over the CSS grid columns, reposition elements into their correct columns, and add hover/countdown micro-animations. This phase delivers a clean, overlap-free landscape experience with visual polish on interactive controls.

**In scope:** LAYOUT-02, LAYOUT-03, LAYOUT-04, LAYOUT-05, LAYOUT-06, UX-01, UX-02  
**Out of scope:** Accessibility/ARIA (Phase 8), CSV import (Phase 8), Custom presets (Phase 9), Streak tracking (Phase 10), Left/right-handed layout settings (deferred)

</domain>

<decisions>
## Implementation Decisions

### Column Element Placement

- **D-01:** `.fullscreenBtn` moves inside the `.cornerControls` div in HTML — it becomes part of the top-left icon cluster. Remove its separate `position:absolute; top:10px; right:10px` CSS rule. All icon controls (info, settings, sound, theme, fullscreen) live in one absolutely-positioned group at top-left.
- **D-02:** `.cornerControls` stays `position:absolute; top:10px; left:10px` in landscape — CSS-only adjustment of offsets if needed to ensure it doesn't overlap ring content or the grid border.

### Session Info Row Below Ring (LAYOUT-04)

- **D-03:** Info row content: cycle count + elapsed time only. No phase name, no session bar in this row.
- **D-04:** Landscape-only repositioning. Cycle count stays above ring in portrait. In landscape, use CSS `order` or `display:none/block` to show cycle count below the ring without touching portrait layout.
- **D-05:** Elapsed time: duplicate a new element (e.g., `id="elapsedLandscape"`) inside `.ringSection` below `ringWrap`. In portrait, hide it (`display:none`). In landscape, show it and hide `#elapsed` inside `.statusRow`. Both elements updated by the same JS that updates `#elapsed`.

### Controls Vertical Centering (LAYOUT-03)

- **D-06:** Right column layout in landscape: history scrolls above (flex:1, order:1), Start/Reset buttons centered at the vertical midpoint of the full column height (50% from top of column), status row + session bar at bottom.
- **D-07:** Controls positioned at 50% of the full column height — implement with `margin: auto 0` on `.controls` within a flex column, or with `position: absolute; top: 50%; transform: translateY(-50%)` on `.controls` inside `.controlsSection`. Planner/researcher to choose the cleanest approach that avoids history overlap.

### Hover States (UX-01)

- **D-08:** All interactive controls uniformly: `filter: brightness(1.08); transform: scale(1.02); transition: filter 120ms ease, transform 120ms ease` on hover. Applied behind `@media (hover: hover) and (pointer: fine)` so touch devices are unaffected. No exceptions — uniformity across Start/Reset, preset buttons, icon toggles, fullscreen, history action buttons, and close buttons.

### Countdown Digit Animation (UX-02)

- **D-09:** Keep the existing `countdownPop` animation (scale 1.5→1→0.8 + opacity fade, 900ms). The current animation already meets UX-02. Refine easing or timing only if testing reveals it feels abrupt — no new animation style needed.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Requirements
- `.planning/REQUIREMENTS.md` — Full v1.1 requirements; Phase 7 items: LAYOUT-02, LAYOUT-03, LAYOUT-04, LAYOUT-05, LAYOUT-06, UX-01, UX-02 with acceptance criteria
- `.planning/ROADMAP.md` — Phase 7 success criteria and phase goal

### Project Constraints
- `CLAUDE.md` — Vanilla JS only, single file, no frameworks, no npm, graceful API degradation

### Codebase Maps
- `.planning/codebase/CONVENTIONS.md` — Naming patterns, CSS class conventions, error handling patterns
- `.planning/codebase/STRUCTURE.md` — index.html section map (CSS sections lines 46–1044, landscape media queries lines 894–1043)

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `@keyframes countdownPop` (index.html ~line 399): existing scale+fade animation — refine, don't replace (D-09)
- `.cornerControls` (index.html line 470): existing absolute-positioned icon group — fullscreenBtn merges here (D-01)
- `#elapsed` (index.html line 1216): existing elapsed time element in statusRow — source for duplicate `#elapsedLandscape` (D-05)
- `@media (orientation: landscape) and (min-height: 501px)` (line 956): existing landscape grid — the section to add LAYOUT-02/03/04 rules
- `@media (max-height: 500px) and (orientation: landscape)` (line 894): compact landscape — verify LAYOUT-06 compliance (already height-based)

### Established Patterns
- CSS custom properties (`--bg`, `--accent`, `--accentSoft`, `--text`) drive all theming — hover states must use these, not hardcoded colors
- Silent `try/catch` for all API calls — no throw anywhere
- Section dividers `/* ====== SECTION NAME ====== */` must be preserved
- `Object.freeze()` for all config constants — don't mutate at runtime
- `@media (pointer: fine)` used for precision-device-specific rules — hover styles follow this pattern (D-08)

### Integration Points
- `.fullscreenBtn` move: update both the HTML element position AND the CSS rules (remove `position:absolute; top:10px; right:10px` from `.fullscreenBtn`, it inherits `.cornerControls` layout)
- JS that updates `#elapsed`: must also update `#elapsedLandscape` — same function call
- Landscape CSS grid (`1fr 1fr` on `.container`): `.ringSection` = left column, `.controlsSection` = right column — all landscape overrides go inside `@media (orientation: landscape) and (min-height: 501px)`
- `.cornerControls` z-index:5 — stays on top of grid content; ensure offset doesn't need increasing after fullscreenBtn is added to the group

</code_context>

<specifics>
## Specific Ideas

- Hover: uniform `scale(1.02) + brightness(1.08)` across ALL controls — user explicitly wants no exceptions
- Hover transition speed: 120ms ease (snappy, not sluggish)
- Elapsed in left column: new `#elapsedLandscape` element (not CSS-only trickery) — update same as `#elapsed` in JS
- Controls at exactly 50% column height — user was clear this is full-column midpoint, not just midpoint of remaining space

</specifics>

<deferred>
## Deferred Ideas

- **Left/right-handed fullscreen layout toggle** — User suggested a settings option to choose whether fullscreen button appears in left column (below ring) or right column (in cornerControls). Interesting for ergonomics but is a new capability. Add to settings phase or a dedicated accessibility/ergonomics phase.

</deferred>

---

*Phase: 7-Landscape Layout & UX Polish*
*Context gathered: 2026-07-13*
