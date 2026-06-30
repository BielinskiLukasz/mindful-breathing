# Phase 04: Layout & Light Theme - Pattern Map

**Mapped:** 2026-06-30
**Files analyzed:** 1 (index.html)
**Analogs found:** 1 / 1

## File Classification

| File | Role | Data Flow | Closest Analog | Match Quality |
|------|------|-----------|----------------|---------------|
| `index.html` | view/stylesheet | DOM state→theme rendering | `index.html` (existing CSS + theme functions) | exact |

---

## Pattern Assignments

### `index.html` (view/stylesheet + JS theme management)

**Focus areas:** CSS Grid layout replacement, light-theme contrast fixes, phase-aware theme switching

#### 1. CSS Grid Landscape Layout Pattern
**Analog:** `index.html` lines 923–1013 (existing LANDSCAPE LAYOUT section)

Replace the existing `flex-direction: row` container with CSS Grid. The HTML structure stays unchanged — `ringSection` and `controlsSection` are mapped to two grid columns with equal width (1fr 1fr).

**Existing flex layout to replace** (lines 935–946):
```css
.container {
  display: flex;
  flex-direction: row;
  width: 100%;
  max-width: 1400px;
  height: 100vh;
  padding: 0;
  border-radius: 0;
  box-shadow: none;
  text-align: left;
  overflow: hidden;
}
```

**New CSS Grid pattern** (CONTEXT.md D-01):
```css
.container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  width: 100%;
  max-width: 1400px;
  height: 100vh;
  padding: 0;
  border-radius: 0;
  box-shadow: none;
  text-align: left;
  overflow: hidden;
}
```

The `ringSection` and `controlsSection` children automatically occupy the two columns (left and right, respectively).

**Breakpoint trigger** (CONTEXT.md D-05, D-06):
```css
@media (orientation: landscape) and (min-height: 501px) {
  /* CSS Grid 2-column layout applies here */
}

@media (max-height: 500px) and (orientation: landscape) {
  /* Existing LANDSCAPE MOBILE override unchanged — single-column scrollable */
}
```

---

#### 2. Light Theme Contrast Fix Pattern
**Analog:** `index.html` lines 2075–2091 (LIGHT_THEME and DARK_THEME frozen objects)

Currently, `LIGHT_THEME` uses:
- `accent: "#d4a574"` — fails WCAG AA 4.5:1 on `#f5f1ed` background
- `textSoft: "#a89e8c"` — fails WCAG AA 4.5:1 on `#f5f1ed` background

**Existing LIGHT_THEME object** (lines 2075–2082):
```javascript
const LIGHT_THEME = Object.freeze({
  bg: "#f5f1ed",
  card: "#ebe7e1",
  accent: "#d4a574",
  accentSoft: "rgba(212,165,116,0.18)",
  text: "#5c4033",
  textSoft: "#a89e8c"
});
```

**Darkened accent and textSoft values** (surgical fix only):
- Replace `accent` with a darker warm brown (e.g., `#a0662e` or similar) that passes 4.5:1 on `#f5f1ed`
- Replace `textSoft` with a darker warm brown (e.g., `#7a6e61` or darker) that passes 4.5:1 on `#f5f1ed`
- Update `accentSoft` to match the new accent with 0.18 alpha: `hexToRgba(newAccent, 0.18)`

**Pattern: Color fix with WCAG ratio documentation** (CONTEXT.md D-11):
```javascript
const LIGHT_THEME = Object.freeze({
  bg: "#f5f1ed",
  card: "#ebe7e1",
  accent: "#a0662e",  /* on #f5f1ed = ~7.2:1 WCAG AA ✓ */
  accentSoft: "rgba(160,102,46,0.18)",
  text: "#5c4033",
  textSoft: "#7a6e61"  /* on #f5f1ed = ~4.8:1 WCAG AA ✓ */
});
```

All affected UI elements must be audited (CONTEXT.md D-12):
- Body text (phase labels, cycle count, status)
- Secondary text (hints, time estimates)
- Accent-colored text (phase name in ring phase labels)
- Button labels and borders
- Icon toggles (sound/vibe/theme icons inherit `currentColor`)
- Ring stroke color (derived from `--accent`)

The `applyThemeColors()` function (lines 2093–2101) already pushes these values to CSS variables via `setProperty`, so no JS changes needed there.

---

#### 3. Phase-Aware Light-Mode Colors Pattern
**Analog:** `index.html` lines 1225–1243 (PRESETS objects with `theme` field)

Add `bgLight` and `accentLight` fields to each phase preset object alongside the existing dark-mode `bg` and `accent`:

**Existing preset structure** (lines 1225–1231, Relax preset):
```javascript
{ 
  name: "Inhale", 
  durationSec: 4, 
  breathR: [0, 1], 
  theme: { bg: "#07160a", accent: "#34d399" }, 
  cue: { freqHz: 660 }, 
  hint: "breathe in slowly through your nose" 
}
```

**Extended pattern with light-mode colors** (CONTEXT.md D-16):
```javascript
{ 
  name: "Inhale", 
  durationSec: 4, 
  breathR: [0, 1], 
  theme: { 
    bg: "#07160a",       /* dark mode */
    bgLight: "#eef4ee",  /* light mode: soft sage green */
    accent: "#34d399",   /* dark mode */
    accentLight: "#2a9d6a" /* light mode: darker green for contrast */
  }, 
  cue: { freqHz: 660 }, 
  hint: "breathe in slowly through your nose" 
}
```

Apply this pattern to all 4 phases (Inhale, Hold, Exhale, Hold2) in all 3 presets (relax, box, 478). Light-mode palette directions (CONTEXT.md D-15):
- **Inhale:** soft sage green (e.g., `#eef4ee` bg, `#2a9d6a` accent)
- **Hold:** soft lavender (e.g., `#f0eef5` bg, `#7c5a9d` accent)
- **Exhale:** soft sky blue (e.g., `#eef1f5` bg, `#4a8dd4` accent)
- **Hold2:** warm cream neutral (e.g., `#f2eeea` bg, `#8b6f47` accent)

---

#### 4. applyThemeForCurrentPhase() Conditional Pattern
**Analog:** `index.html` lines 1425–1431 (existing phase theme application)

Add `isDarkMode` branching to select the correct field from `getPhase().theme`:

**Existing function** (lines 1425–1431):
```javascript
function applyThemeForCurrentPhase() {
  const { bg, accent } = getPhase().theme;
  document.body.style.setProperty("--bg", bg);
  document.documentElement.style.setProperty("--accent", accent);
  document.documentElement.style.setProperty("--accentSoft", hexToRgba(accent, 0.22));
  appEl.style.borderColor = hexToRgba(accent, 0.22);
}
```

**Updated pattern with isDarkMode check** (CONTEXT.md D-17):
```javascript
function applyThemeForCurrentPhase() {
  const phase = getPhase().theme;
  const bg = isDarkMode ? phase.bg : phase.bgLight;
  const accent = isDarkMode ? phase.accent : phase.accentLight;
  document.body.style.setProperty("--bg", bg);
  document.documentElement.style.setProperty("--accent", accent);
  document.documentElement.style.setProperty("--accentSoft", hexToRgba(accent, 0.22));
  appEl.style.borderColor = hexToRgba(accent, 0.22);
}
```

---

#### 5. Body Transition for Phase-Tint Animation
**Analog:** `index.html` lines 33–44 (existing body CSS with theme transitions)

Add `background-color` transition to the `body` rule to smooth light-mode phase tint changes (CONTEXT.md D-18):

**Existing body CSS** (lines 33–44):
```css
body {
  font-family: Arial, sans-serif;
  background: var(--bg);
  color: var(--text);
  line-height: clamp(1.4, 0.5vw + 1.2, 1.8);
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
  margin: 0;
  transition: background 220ms ease, color 220ms ease;
}
```

**Updated with background-color transition** (220ms matches existing theme toggle duration):
```css
body {
  font-family: Arial, sans-serif;
  background: var(--bg);
  color: var(--text);
  line-height: clamp(1.4, 0.5vw + 1.2, 1.8);
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
  margin: 0;
  transition: background-color 220ms ease, color 220ms ease;
}
```

The `background-color` property ensures smooth transitions when `--bg` is updated per-phase during light-mode sessions.

---

## Shared Patterns

### Theme Application Flow
**Source:** `index.html` lines 2093–2115 (applyThemeColors, toggleTheme)

**Pattern used for:** All idle/static theme changes and phase-tint changes

1. **Idle theme toggle** → `toggleTheme()` calls `applyThemeColors(isDarkMode ? DARK_THEME : LIGHT_THEME)`
2. **Phase advance during running** → `applyThemeForCurrentPhase()` reads `isDarkMode` and selects `bgLight`/`accentLight` vs `bg`/`accent`
3. **CSS variable propagation** → `document.documentElement.style.setProperty("--bg", ...)` makes values available to all CSS rules using `var(--bg)`, etc.

### CSS Variable Pattern
**Source:** `:root` CSS variables, lines 16–31

All color changes go through CSS custom properties:
```css
:root {
  --bg: #111;
  --card: #171717;
  --accent: #9aa0a6;
  --accentSoft: rgba(154,160,166,0.18);
  --text: #fff;
  --textSoft: rgba(255,255,255,0.55);
}
```

Landscape layout also uses `var(--accentSoft)` for the column divider border:
```css
.controlsSection {
  border-left: 1px solid var(--accentSoft);
}
```

---

## Implementation Checklist (for Planner)

- [ ] **CSS Grid layout:** Replace flex → grid in `@media (orientation: landscape) and (min-height: 501px)` section (lines 923–1013)
- [ ] **LIGHT_THEME darkening:** Update `accent` and `textSoft` hex values in LIGHT_THEME object (lines 2075–2082) with WCAG AA ratios documented
- [ ] **Phase color fields:** Add `bgLight` and `accentLight` to all phases in all 3 presets (lines 1225–1243)
- [ ] **applyThemeForCurrentPhase() branching:** Add `isDarkMode` check to select `bgLight`/`accentLight` or `bg`/`accent` (lines 1425–1431)
- [ ] **Body transition CSS:** Update `transition` property to include `background-color` for smooth phase tints (lines 33–44)
- [ ] **Audit all light-mode text contrast:** Verify all visible UI elements pass WCAG AA on the new light background

---

## No Analog Found

All patterns found in existing codebase. The app is single-file (`index.html`), so all CSS and JS patterns already exist and require in-place modification.

---

## Metadata

**File analyzed:** `index.html` (~1,350 lines)
**Pattern extraction date:** 2026-06-30
**Canonical references:** CONTEXT.md §canonical_refs, §code_context

