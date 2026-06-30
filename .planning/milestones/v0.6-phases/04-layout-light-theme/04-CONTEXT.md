# Phase 4: Layout & Light Theme - Context

**Gathered:** 2026-06-30
**Status:** Ready for planning

<domain>
## Phase Boundary

Deliver a CSS Grid 2-column landscape layout with history always visible alongside the ring for all landscape devices (height >500px), and fix the light theme to meet WCAG AA contrast (4.5:1 normal text, 3:1 large text) across all visible UI elements. Also introduce separate light-mode phase colors so breathing sessions feel atmospheric in light theme without forcing a dark background.

</domain>

<decisions>
## Implementation Decisions

### Layout: CSS Grid Column Split

- **D-01:** Replace the existing `flex-direction: row` landscape container with `display: grid; grid-template-columns: 1fr 1fr` — equal 50/50 split.
- **D-02:** The existing HTML structure (`ringSection` left, `controlsSection` right) maps directly to the two grid columns; no HTML changes needed.
- **D-03:** History panel uses internal scroll (`overflow-y: auto` within the right column); controls remain fixed at the bottom of the right column (same behavior as current flex layout).
- **D-04:** Keep the visual column divider: `border-left: 1px solid var(--accentSoft)` on `controlsSection`.

### Layout: Breakpoint Strategy

- **D-05:** The 2-column CSS Grid triggers on `@media (orientation: landscape) and (min-height: 501px)` — covers ALL landscape devices with height >500px (phones + tablets + desktop) with no min-width constraint. The REQUIREMENTS.md "tablet-width" phrasing was approximate; user intent is all landscape devices.
- **D-06:** Retain the existing `@media (max-height: 500px) and (orientation: landscape)` LANDSCAPE MOBILE override unchanged — very short landscape views (e.g., iPhone SE landscape) stay in single-column scrollable mode.
- **D-07:** Column padding: keep 20px on both `ringSection` and `controlsSection`; the ring's `clamp(200px, 25vw, 400px)` handles size on narrower screens.

### Light Theme: Contrast Fixes

- **D-08:** Surgical fix only — change only the specific color values that fail WCAG AA. Do not redesign the full palette.
- **D-09:** Darken the `accent` color (`#d4a574`) to a darker golden-brown that passes 4.5:1 on the cream background (`#f5f1ed`). Use a single darkened value — no new CSS variable.
- **D-10:** Darken `textSoft` (`#a89e8c`) to a warmer, darker value that passes 4.5:1 on cream. Direction: warm brown, not cool gray.
- **D-11:** Document every changed color with an inline CSS comment showing the calculated ratio: `/* #6b4a2a on #f5f1ed = 7.2:1 WCAG AA */`.
- **D-12:** Audit ALL visible UI elements — body text, secondary text, accent-colored text, button labels, borders, icon toggles, ring stroke. Not just body copy.
- **D-13:** Color direction constraint: all darkened replacements must stay warm browns/golds. No gray, no cool-neutral fallbacks.

### Phase Colors in Light Mode

- **D-14:** Create separate light-mode phase background and accent colors per phase (most polished option). Sessions in light mode use cream-tinted backgrounds, not the existing dark atmospheric ones.
- **D-15:** Light-mode phase bg palette: tinted versions of the cream base — soft sage green for Inhale, soft lavender for Hold, soft sky blue for Exhale, warm neutral for Hold2.
- **D-16:** Storage: add `bgLight` field to each phase preset object alongside the existing `bg` field (e.g., `{ name: "Inhale", bg: "#07160a", bgLight: "#eef4ee", ... }`). Same pattern for `accentLight` alongside `accent`.
- **D-17:** `applyThemeForCurrentPhase()` reads `bgLight`/`accentLight` when `isDarkMode === false`, and `bg`/`accent` when `isDarkMode === true`.
- **D-18:** Add `transition: background-color 220ms ease` to `body` for smooth light-mode phase tint transitions. This matches the existing theme toggle transition duration.

### Claude's Discretion

- Exact hex values for darkened `accent` and `textSoft` — researcher/implementer calculates the minimum-change value that passes 4.5:1 on `#f5f1ed`.
- Exact hex values for `bgLight` and `accentLight` per phase — implementer chooses WCAG-compliant warm tints in the sage/lavender/sky-blue family that pass 4.5:1 text contrast.
- Whether `--card` needs adjustment in light mode (the card background `#ebe7e1` may need auditing against text colors used inside cards).

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Requirements & Roadmap

- `.planning/ROADMAP.md` §"Phase 4: Layout & Light Theme" — Goal, success criteria (SC-1 through SC-4), requirements (UX-04, THEME-03)
- `.planning/REQUIREMENTS.md` §"Layout & Responsive Design" (UX-04) and §"Theming & Accessibility" (THEME-03) — exact acceptance conditions

### Codebase

- `index.html` lines ~923–1000 — existing LANDSCAPE LAYOUT (50/50 SPLIT) and LANDSCAPE MOBILE CSS sections being replaced/modified
- `index.html` lines ~2075–2115 — `LIGHT_THEME` / `DARK_THEME` frozen objects and `applyThemeColors()` / `toggleTheme()` functions
- `index.html` lines ~1225–1250 — preset phase objects (Relax, Box, 4-7-8) where `bgLight` and `accentLight` fields will be added
- `index.html` line ~1425 — `applyThemeForCurrentPhase()` function that reads `getPhase().theme` — needs `isDarkMode` branching

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets

- `ringSection` / `controlsSection` CSS classes: already divide the landscape layout into left (ring) and right (controls+history). No HTML changes needed — only the parent container's CSS changes from flex to grid.
- `LIGHT_THEME` frozen object (line ~2075): `bg`, `card`, `accent`, `accentSoft`, `text`, `textSoft` — the fix target. Keep the object structure; update values.
- `applyThemeColors(theme)` (line ~2093): sets all 6 CSS variables via `setProperty`. No change needed to this function itself.
- `applyThemeForCurrentPhase()` (line ~1425): already sets `--bg` and `--accent` from `getPhase().theme`. Needs `isDarkMode` check to select `bgLight`/`accentLight` vs `bg`/`accent`.

### Established Patterns

- CSS `transition` at 220ms ease is the standard for all theme/color transitions in the app. Use the same duration for the new phase-bg transition on `body`.
- `Object.freeze()` for all config objects — apply to any new constants introduced.
- `clamp()` for sizing — ring at `clamp(200px, 25vw, 400px)` already handles column-width variance without extra media queries.
- CSS variable approach (`--bg`, `--accent`, etc.) via `setProperty` — all theme changes go through this mechanism, no direct element styling.

### Integration Points

- The landscape CSS lives in a clearly labeled section `/* ====== LANDSCAPE LAYOUT (50/50 SPLIT) ====== */` — in-place replacement.
- `LIGHT_THEME` object is the single source of truth for light theme colors. Update it; no other values need changing for the idle/static state.
- Phase preset objects (`PRESETS.relax[0]`, etc.) in the CONFIG section — add `bgLight` and `accentLight` per phase entry.
- `isDarkMode` global boolean is accessible from `applyThemeForCurrentPhase()` — use it directly for the branch.

</code_context>

<specifics>
## Specific Ideas

- Light-mode phase tint examples (approximate directions, implementer confirms WCAG): Inhale → soft sage (`~#eef4ee`), Hold → soft lavender (`~#f0eef5`), Exhale → soft sky (`~#eef1f5`), Hold2 → warm cream neutral (`~#f2eeea`)
- Accent darkening direction: move from golden `#d4a574` toward a darker warm brown (e.g., `#a0662e` range) while retaining the golden character
- textSoft darkening: from `#a89e8c` toward `#7a6e61` or darker — test against 4.5:1 on `#f5f1ed`

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 4-Layout & Light Theme*
*Context gathered: 2026-06-30*
