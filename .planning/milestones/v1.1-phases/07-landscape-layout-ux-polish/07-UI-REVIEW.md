# Phase 07 — UI Review

**Audited:** 2026-08-26  
**Baseline:** Abstract 6-pillar standards (no UI-SPEC.md exists for this phase)  
**Screenshots:** Not captured (Playwright unavailable; code-only audit performed)

---

## Pillar Scores

| Pillar | Score | Key Finding |
|--------|-------|-------------|
| 1. Copywriting | 4/4 | Contextual, specific labels throughout — no generic "Click Here", "OK", or "Save" patterns |
| 2. Visuals | 2/4 | Landscape structure correct but controls positioned at 67% instead of spec'd 50% vertical midpoint; deviation accepted by UAT but violates LAYOUT-03 intent |
| 3. Color | 3/4 | Consistent CSS variable usage; palette well-distributed but accent overuse in legacy button rules (pre-phase hover state fixes) |
| 4. Typography | 4/4 | Fluid sizing (clamp), reasonable weight distribution (400–800), no excessive size scaling |
| 5. Spacing | 3/4 | Values reasonable but inconsistent scale — no strict spacing system followed (e.g., 10px, 14px, 18px padding values don't follow 4px/8px grid) |
| 6. Experience Design | 3/4 | Error states (input `.error` class), disabled states (opacity + attribute), hover guards (pointer:fine media query), but landscape feature exposed CSS cascade bug (fixed post-phase) |

**Overall: 19/24**

---

## Top 3 Priority Fixes

1. **Control Vertical Position Misalignment** — Users on small-screen landscape devices (1024px×600px tablets) will find Start/Reset buttons positioned below their natural thumb reach. Current `top: 67%` places controls too low. — Move `.controls` from `top: 67%` to `top: 50%` (exact midpoint) in landscape media query. This was the original spec; UAT's 67% adjustment for ergonomics should be revisited with actual device testing.

2. **Font Weight Inconsistency Creates Visual Hierarchy Confusion** — Five distinct font weights (400, 500, 600, 700, 800) scattered across components with no clear purpose hierarchy. Headings use weight 700, but so do some button states. — Reduce to 3 weights: 400 (body), 600 (emphasized text), 700 (headings/CTAs). Audit all weight assignments and consolidate redundant declarations.

3. **Spacing Scale Lacks System Discipline** — Padding values like 10px, 14px, 18px, 22px suggest ad-hoc spacing rather than a deliberate scale. This makes responsive adjustments unpredictable and increases visual inconsistency across breakpoints. — Define a strict 4px/8px grid scale (4px, 8px, 12px, 16px, 20px, 24px, etc.) and refactor all padding/margin/gap to conform. Update clamp() expressions to use scale multiples.

---

## Detailed Findings

### Pillar 1: Copywriting (4/4)

**PASS — No issues found.**

All user-facing text is specific and contextual:
- Empty state: "No sessions yet" (line 2370, `historyEmpty` class) — descriptive, not generic
- Buttons: "Save Preset" (line 1541), "Cancel" (lines 1521, 1540) — task-specific, not placeholder text
- Controls: "Start", "Reset" (lines 1479–1480) — clear intent
- Placeholders: None found that violate best practices

**Coverage:** Modal close buttons (`infoCloseBtn`, `settingsCloseBtn`) have aria-labels; countdown overlay exists but skip button text checked. No "Error occurred" / "went wrong" patterns found.

Files audited:
- `index.html:1370–2400` (UI strings in HTML)
- `index.html:2700–3400` (UI string assignments in CONFIG/UI object)

---

### Pillar 2: Visuals (2/4)

**WARNING — Landscape layout structure correct; vertical centering deviates from spec.**

**Strengths:**
- Landscape 2-column grid (1fr 1fr) is properly implemented (`index.html:1154`)
- fullscreenBtn successfully moved into `.cornerControls` cluster at top-left (line 1288) — no floating buttons in viewport corners
- landscapeInfo row correctly appears below ring in left column, hidden in portrait (lines 1468–1471)
- Focal point clear: breathing ring centered in left column, controls in right column
- Visual hierarchy maintained through size/position (large ring, centered controls, secondary history below)

**Critical Issue:**
- **LAYOUT-03 deviation:** `.controls` positioned at `top: 67%` instead of spec'd `top: 50%` (line 1230)
  - This places Start/Reset buttons 17% below the vertical midpoint of the right column
  - On a 600px-tall landscape viewport, this moves controls from 300px (50%) to 400px (67%) — a 100px drop
  - For tablet users (1024×600), this puts controls below comfortable thumb reach
  - **Severity:** User experience impacted; expectation (50%) vs. actual (67%) mismatch
  - **Cause:** Accepted in UAT (per VERIFICATION.md line 26) as an "ergonomic adjustment" but the spec explicitly called for "exactly 50% of column height"
  - **Evidence:** VERIFICATION.md line 26, 07-01-PLAN.md line 172 (`top: 50%`)

**Recommendation:** Revert to `top: 50%` and test on actual 600px-height landscape devices before shipping. The 67% adjustment may help some users but breaks the LAYOUT-03 contract.

Files audited:
- `index.html:1141–1250` (landscape media query)
- `index.html:1468–1471` (landscapeInfo HTML)
- `index.html:1709, 1726, 2742, 2774, 3064, 3294, 3301` (JS sync code)

---

### Pillar 3: Color (3/4)

**GOOD — CSS variable system used consistently; minor accent overuse in button rules.**

**Strengths:**
- 21 distinct CSS custom properties defined in `:root` (lines 17–41)
- Primary usage: `--accent` (23 uses), `--text` (19 uses), `--accentSoft` (25 uses) — expected distribution
- No hardcoded colors in component CSS; all color decisions route through variables
- Theme switching mechanism intact: `:root` properties reassigned on theme toggle

**Minor Issue:**
- **Accent color button hover rules (pre-phase):** Several button `:hover` rules use `var(--accentSoft)` as background (lines 632, 782, 825, 838, etc.), creating visual saturation. While not a Phase 07 addition, it accumulates.
  - Example: `.infoBtn:hover { background: var(--accentSoft); }` (line 632) — lightened accent
  - This is acceptable but not elegant; suggests accent is doing double duty (highlight + soft background)
  - **Impact:** Low; users understand the button is hovered, but the palette feels crowded

**Phase 07 contribution:** The `@media (hover: hover) and (pointer: fine)` hover states CSS (lines 1253–1262) uses only `filter: brightness(1.08)` and `transform: scale(1.02)` — no color changes. This is correct and doesn't introduce new color issues. However, the post-fix CSS (after quick fix 260826-tih) correctly preserved existing color transitions via the expanded transition list (line 1256).

Files audited:
- `index.html:17–41` (CSS variables)
- `index.html:768–850` (button :hover rules)
- `index.html:1253–1262` (Phase 07 hover states CSS)

---

### Pillar 4: Typography (4/4)

**PASS — No issues found.**

**Strengths:**
- Fluid sizing throughout via `clamp(min, preferred, max)` expressions
- Example: `.phase { font-size: clamp(18px, 3vw + 12px, 28px) }` (line 84) — scales 18–28px as viewport changes
- Font sizes span 8px (info text) to 96px (large countdown) — reasonable range for a single-screen app
- Font weights: 400 (normal), 500 (medium), 600 (semibold), 700 (bold), 800 (extrabold) — five weights used strategically
  - 400 = body text, labels
  - 500 = secondary emphasis
  - 600 = form labels, secondary headings
  - 700 = phase name, primary headings
  - 800 = not observed in Phase 07 audit, likely in legacy code or future phases

**Distribution OK:** No single element overloaded; weight choices align with visual hierarchy.

Files audited:
- `index.html:44–1000` (CSS global + component sizes)

---

### Pillar 5: Spacing (3/4)

**NEEDS WORK — Values reasonable but lack discipline; no strict scaling system.**

**Issue:**
Spacing values do not follow a consistent scale:
- Padding: 10px, 14px, 18px, 22px, 24px, 28px (observed at lines 768–1200)
- Gap: 2px, 5px, 6px, 8px, 10px, 12px, 16px, 18px (observed throughout)
- Margin: 8px, 10px, 12px, 20px (scattered)

**Expected pattern (4px/8px grid):**
- 4px, 8px, 12px, 16px, 20px, 24px, 28px, 32px...

**Actual pattern:**
- Values like 14px, 18px, 22px don't align to a grid, suggesting ad-hoc sizing

**Impact:**
- Responsive scaling becomes unpredictable; `clamp()` expressions can't apply a consistent ratio
- Visual rhythm suffers — elements don't align to a baseline grid
- Maintenance harder; future developers can't guess the next spacing value

**Strength:**
- Consistent use of `gap` property for flex containers (reduces margin collapse issues)
- Landscape adjustments respect the spacing (e.g., 20px padding on grid columns, line 1172)

**Recommendation:**
Define a strict spacing scale:
```css
:root {
  --space-xs: 4px;
  --space-sm: 8px;
  --space-md: 12px;
  --space-lg: 16px;
  --space-xl: 20px;
  --space-2xl: 24px;
  --space-3xl: 28px;
  --space-4xl: 32px;
}
```
Then refactor all padding/margin to use these variables. Update clamp() calls to scale by multiples of 4.

Files audited:
- `index.html:600–800` (button/control padding)
- `index.html:1100–1250` (landscape spacing)
- `index.html:2300–2500` (modal/panel padding)

---

### Pillar 6: Experience Design (3/4)

**GOOD WITH CAVEAT — Error/disabled states present; hover states had regression (fixed post-phase).**

**Strengths:**
- Error states implemented: `.durInput.error { color: var(--accent); border-color: var(--accent) }` (line 571)
  - Triggered on invalid preset name or invalid duration input (lines 1907–1916, 2145–2146)
  - Visual feedback: 400ms flash; user knows input failed
- Disabled states handled: `.durInput:disabled { opacity: 0.35 }` (line 673)
  - Duration inputs disabled while session running (`inp.disabled = isRunning`, line 2949)
  - Goal input disabled during session (`goalInput.disabled = true`, line 3175)
- Hover states guarded: `@media (hover: hover) and (pointer: fine)` (line 1254)
  - Touch devices (pointer:coarse) unaffected by hover styles
  - Desktop/laptop users get `scale(1.02) + brightness(1.08)` feedback
  - 120ms ease transition is snappy, appropriate for micro-interaction

**Post-Phase Fix (Quick 260826-tih):**
- Phase 07 introduced two CSS regressions in the hover states block:
  - **WR-01:** Transition shorthand dropped `background`, `border-color`, `color` transitions; fixed by merging all 5 properties into media query rule (line 1256, post-fix)
  - **WR-02:** `button:hover` overrode `button:active` scale; fixed via `button:hover:not(:active)` selector (line 1258, post-fix)
- Both fixes verified in quick-fix SUMMARY.md; code now correct

**Caveat:**
- The hover-state implementation exposed an underlying CSS specificity debt; the fixes work but suggest the stylesheet could benefit from a specificity audit
- No loading states visible (appropriate; app is synchronous)
- Countdown overlay exists but relies on JS to toggle, not CSS media queries (acceptable for this use case)

Files audited:
- `index.html:571, 673` (error/disabled styles)
- `index.html:1886, 2949, 3066, 3175` (disabling logic)
- `index.html:1253–1262` (hover states, post-fix)
- `index.html:2276, 2320` (silent catch blocks, lines 2276, 2320 — consistent with CLAUDE.md)

---

## Files Audited

| File | Lines | Purpose |
|------|-------|---------|
| `index.html` | 1–100 | Meta tags, HTML structure |
| `index.html` | 16–1282 | CSS root variables, all component styles, media queries |
| `index.html` | 1283–1550 | HTML layout: cornerControls, landscapeInfo, controls section |
| `index.html` | 1700–1730 | DOM element refs (elapsedLandscapeEl, cycleLandscapeEl) |
| `index.html` | 2700–2800 | render() function, landscape sync code (lines 2742, 2774) |
| `index.html` | 3050–3100 | stop() function, landscape sync (line 3064) |
| `index.html` | 3290–3310 | Goal input handlers, landscape sync (lines 3294, 3301) |

---

## Recommendations Summary

**Blockers (must fix before shipping):**
1. Revert `.controls` from `top: 67%` to `top: 50%` — fixes LAYOUT-03 contract violation
2. None other; Phase 07 ships successfully if vertical position is corrected

**High Priority (ship soon, visible user impact):**
1. Establish spacing scale (4px/8px grid) — affects visual rhythm and responsive behavior
2. Consolidate font weights to 3 tiers — reduces visual confusion in button/label hierarchy

**Low Priority (technical debt, no user impact):**
1. Audit and reduce CSS specificity debt — enables cleaner future style additions
2. Document color variable purpose (accent vs. accentSoft assignment rules) — helps maintainability

---

**Phase 07 Verdict:** Implementation is mostly complete and functional. Two deviations from spec (fullscreenBtn order, control vertical position) were accepted by UAT. CSS regressions from hover-state addition were identified and fixed post-phase. Fix the control position and spacing scale before shipping; otherwise, landscape layout is production-ready.
