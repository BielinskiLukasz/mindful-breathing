---
phase: 07-landscape-layout-ux-polish
plan: "01"
subsystem: layout/css
tags: [landscape, layout, fullscreen, css, html]
requires: []
provides: [fullscreenBtn-in-cornerControls, landscapeInfo-row, controls-centered-50pct]
affects: [index.html]
tech_stack:
  added: []
  patterns: [css-absolute-centering, display-none-toggle, css-custom-properties]
key_files:
  created: []
  modified:
    - index.html
decisions:
  - fullscreenBtn moved into cornerControls cluster as last child (D-01)
  - landscapeInfo duplicate elements rather than CSS-only trickery for elapsed/cycle (D-05)
  - position:absolute + translateY(-50%) chosen for exact 50% column centering (D-07)
  - LAYOUT-06 verified as already height-based — no code change needed
metrics:
  duration: "~10 minutes"
  completed: "2026-07-13"
status: complete
---

# Phase 7 Plan 01: Landscape Layout Restructure — Summary

HTML and CSS structural changes to index.html fixing three landscape overlap issues: fullscreenBtn joined the cornerControls cluster, a landscape-only session-info row was added below the ring, and Start/Reset buttons are now centered at exactly 50% of the right column height via CSS absolute positioning.

## Tasks Completed

### Task 1 — Move fullscreenBtn into cornerControls (D-01, D-02)

**HTML change:** The `<button class="fullscreenBtn" id="fullscreenBtn">` element (with `fsEnterIcon` and `fsExitIcon` SVG children) was moved from its position immediately after the closing `</div>` of `.cornerControls` to INSIDE `.cornerControls` as the last child, after `themeToggleBtn`.

**CSS change:** In the `/* ====== FULLSCREEN ====== */` section, removed `position: absolute;`, `top: 10px;`, and `right: 10px;` from `.fullscreenBtn { }`. The button now inherits `.cornerControls` flex-column layout (gap: 6px). All other declarations (width, height, border, background, display, align-items, justify-content) remain untouched.

**Result:** The top-right corner of the viewport is clear of any floating button in landscape. All 5 icon controls (info, settings, sound, theme, fullscreen) are grouped in the single absolutely-positioned `.cornerControls` cluster at top-left.

### Task 2 — Add landscape info row below ring (D-03, D-04, D-05)

**HTML change:** After `<div class="nextPhase" id="nextPhase"></div>` inside `.ringSection`, added:
```html
<div class="landscapeInfo" id="landscapeInfo">
  <span class="cycle" id="cycleLandscape"></span>
  <span class="elapsed" id="elapsedLandscape">0:00</span>
</div>
```
`#cycleLandscape` uses `.cycle` class; `#elapsedLandscape` uses `.elapsed` class (tabular-nums, correct font-size). Both start empty/0:00 — populated by JS in plan 07-02.

**CSS global scope:** Added `#landscapeInfo { display: none; }` after the `.elapsed { }` rule block. Hides the row in portrait by default.

**CSS landscape media query:** Inside `@media (orientation: landscape) and (min-height: 501px)`, after `.ringSection { }`, added:
- `#cycle { display: none; }` — hides above-ring cycle count in landscape
- `#elapsed { display: none; }` — hides elapsed from statusRow in landscape
- `#landscapeInfo { display: flex; gap: 16px; ... margin-top: 10px; }` — shows the landscape duplicate row

`.statusRow` itself remains visible in landscape; only `#elapsed` inside it is hidden.

### Task 3 — Center Start/Reset controls at 50% column height (D-06, D-07)

**CSS change 1:** Added `position: relative;` to `.controlsSection { }` inside the landscape media query. This makes the section a positioning context for the absolutely-positioned `.controls` child.

**CSS change 2:** In `.controls { }` inside the landscape media query, replaced `order: 2; border-top: 1px solid var(--accentSoft); padding-top: 10px;` with:
```css
position: absolute;
top: 50%;
left: 0;
right: 0;
transform: translateY(-50%);
padding: 0 20px;
border-top: none;
padding-top: 0;
margin-top: 0;
```
Kept `flex-shrink: 0;`, `justify-content: flex-start;`, `flex-wrap: wrap;` for internal button layout. Removed `order: 2` (not meaningful when position:absolute takes element out of flex flow). The `padding: 0 20px` matches the column's 20px side padding.

`.history` (flex:1, order:1), `.statusRow` (order:3), and `.sessionBar` (order:4) continue to fill the column naturally as flex children.

### LAYOUT-06 Verification (no code change)

Confirmed: the compact landscape media query at `/* ====== LANDSCAPE MOBILE ====== */` uses `@media (max-height: 500px) and (orientation: landscape)` — a height-based trigger. LAYOUT-06 is already satisfied with no code change required.

## Deviations from Plan

None — plan executed exactly as written. All three structural changes matched the plan spec precisely.

## Key Anchor Strings for Wave 2 Executor

Wave 2 (plan 07-02) needs these DOM elements and CSS patterns:

| Element / Hook | Search Anchor | Purpose |
|---|---|---|
| `#cycleLandscape` | `id="cycleLandscape"` | Wave 2 JS must update this on each render cycle |
| `#elapsedLandscape` | `id="elapsedLandscape"` | Wave 2 JS must update this on each render cycle |
| `#landscapeInfo` wrapper | `id="landscapeInfo"` | Container — no JS needed, purely display:none/flex |
| `.fullscreenBtn` in DOM | Last child inside `.cornerControls` | `id="fullscreenBtn"` unchanged — JS refs unchanged |
| Landscape media query | `/* ====== LANDSCAPE LAYOUT (50/50 SPLIT) ====== */` | Where all landscape CSS lives |
| Compact landscape query | `/* ====== LANDSCAPE MOBILE ====== */` | `@media (max-height: 500px)` — LAYOUT-06 compliant |

## Commit

`3abda4c` — `feat(layout): landscape layout restructure — phase 7 wave 1`

Files changed: `index.html` (36 insertions, 16 deletions)

## Self-Check: PASSED

- [x] `fullscreenBtn` is last child of `.cornerControls` in HTML — verified
- [x] `position: absolute; top: 10px; right: 10px` removed from `.fullscreenBtn` CSS — verified
- [x] `#landscapeInfo` div with `#cycleLandscape` and `#elapsedLandscape` exists in `.ringSection` — verified
- [x] Global CSS has `#landscapeInfo { display: none }` — verified
- [x] Landscape CSS hides `#cycle` and `#elapsed`, shows `#landscapeInfo` — verified
- [x] `.controlsSection` has `position: relative` in landscape CSS — verified
- [x] `.controls` has `position: absolute; top: 50%; transform: translateY(-50%)` in landscape CSS — verified
- [x] LAYOUT-06 compact landscape is height-based — verified (no change made)
- [x] Commit `3abda4c` exists in git log — verified
