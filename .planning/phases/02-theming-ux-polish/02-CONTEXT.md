# Phase 2: Theming & UX Polish — Context

**Date:** 2026-06-04  
**Phase:** 2 of 2  
**Status:** Context captured, ready for planning

---

## Domain

Phase 2 delivers two interconnected capabilities:

1. **Theme Toggle** — Users can choose between light and dark themes, with preference persisted across sessions
2. **Responsive Visual Design** — Font sizing scales fluidly across all devices (mobile to desktop), and layout adapts intelligently to landscape orientation

---

## Requirements (Locked by ROADMAP.md)

1. User can toggle light/dark theme and preference persists across browser sessions via localStorage
2. Font sizes are consistent and readable from mobile (320px) to desktop (4K) widths
3. Landscape orientation displays breathing interface optimally without horizontal scroll or cramped text
4. All interactive elements have clear visual hierarchy and focus states for keyboard navigation

---

## Implementation Decisions

### Theme Toggle Mechanism

- **Implementation:** CSS custom properties (--bg, --accent, --text, etc.) — update values on toggle, not class-based switching
- **Default state:** Dark theme on load; user can toggle manually to light theme
- **System preference:** No auto-detect of `prefers-color-scheme` — user starts with dark, chooses manually
- **Ring color adaptation:** SVG ring color adapts to theme (light ring on dark bg, warm ring on light bg)
- **Persistence:** Theme preference saved to localStorage under existing `STORAGE_KEY` pattern
- **UI placement:** Keep theme toggle in settings panel with sound/vibration toggles (icon only: ☀️/🌙)
- **Transition:** Smooth fade animation (220ms, using existing CSS transition pattern) when toggling between themes
- **Session behavior:** Theme can be toggled at any time, even during active breathing session
- **Toggle label:** Icon-only (☀️ for light, 🌙 for dark) — minimal, intuitive

**Rationale:** CSS custom properties provide clean integration with existing theme system. Dark default preserves current user experience. Smooth transition matches Phase 1's animation patterns.

### Light Theme Color Palette

- **Approach:** Create balanced light palette designed independently (not inverted from dark theme)
- **Accessibility target:** WCAG AA contrast (4.5:1 minimum for text on background)
- **Overall mood:** Calm & warm aesthetic (creams, beige, soft tones)
- **Background:** Light cream/beige (TBD exact hex during implementation, ~#f5f1ed or similar)
- **Primary text:** Warm dark brown (TBD exact hex, ~#5c4033 or similar) — matches calm mood while maintaining readability
- **Secondary/inactive text:** Warm gray/beige (TBD exact hex, ~#a89e8c or similar) — reduces visual weight while staying warm
- **Accent color (ring, buttons):** Warm accent — gold/orange rather than vibrant primary color, softer and more cohesive with calm mood
- **Ring-specific color:** Warm gold/orange in light theme (adapts from dark theme's vibrant accent)

**Rationale:** Balanced design ensures light theme doesn't feel like an inversion; warm palette maintains the meditative, calm feeling. WCAG AA ensures accessibility for users with low vision.

### Responsive Font Sizing

- **Scaling strategy:** CSS `clamp()` function for fluid, responsive sizing (not fixed breakpoints)
- **Body text & labels:** `clamp(12px, vw-based-expression, 16px)` — readable on mobile, doesn't grow too large on desktop
- **Headings (phase label, cycle count):** `clamp(18px, vw-based-expression, 28px)` — moderate prominence without overwhelming
- **Countdown timer:** `clamp(40px, vw-based-expression, 64px)` — the focal point, scales larger than headings
- **Button/control text:** `clamp(13px, vw-based-expression, 17px)` — slightly larger than body for better mobile touch targets
- **History card text:** `clamp(11px, vw-based-expression, 14px)` — slightly smaller for compact display, shows more entries per page
- **Line height & letter spacing:** Scale responsively with font sizes using `clamp()` or proportional scaling — improves readability across sizes
- **Transition:** Smooth scaling, no jarring breakpoints between device sizes

**Rationale:** `clamp()` provides smooth, device-aware scaling without fixed breakpoints. Phase 1 used fixed breakpoints; Phase 2 adopts modern CSS for better UX across all screen widths.

### Landscape Orientation Layout

- **Layout change:** Reflow to horizontal/side-by-side layout when width > height (any landscape orientation)
- **Primary layout:** Ring (SVG) on LEFT (50% width) | Controls/History on RIGHT (50% width)
- **Ring scaling:** Ring scales larger in landscape to fill available vertical space (not fixed size like portrait)
- **Layout toggle:** Add configurable setting in phase 2 to let users swap ring/controls positions if preferred
- **History visibility:** Visible by default in landscape (not hidden); displays below controls on right side
- **Controls position:** Fixed at bottom of right column; history scrolls above (keeps breathing controls always accessible)
- **Horizontal split:** Even 50/50 split between left (ring) and right (controls/history)
- **Max-width cap:** Landscape layout maxes out at ~1400px total width on very wide screens (4K desktop) — centered with padding beyond that
- **Landscape trigger:** Activates at any orientation change (width > height), no specific width threshold

**Rationale:** Side-by-side leverages tablet/wide-screen space efficiently. Larger ring in landscape enhances immersion. History visible by default allows users to see session data while breathing.

---

## Code Context (Reusable Assets & Patterns)

### Existing Theme System
- **CSS custom properties:** Already defined in `index.html` style block (~line 1060–1080)
  - `--bg` (background), `--accent` (ring color), `--accentSoft`, `--text`, `--textSoft`
  - Already used throughout CSS and inline SVG styling
- **Current state:** Dark theme only; light theme colors need definition
- **Integration point:** Update CSS properties on toggle event; persist to localStorage

### Existing CSS Media Queries
- **Current breakpoints:** @media (max-width: 480px) and (max-width: 360px)
- **Phase 1 pattern:** Fixed breakpoints for responsive design
- **Phase 2 change:** Introduce `clamp()` for fluid scaling alongside existing breakpoints
- **Location:** `index.html` style block (~1100+ lines of CSS)

### Settings & Persistence
- **Existing pattern:** `saveSettings()` and `loadSettings()` already handle toggles (sound, vibration, dark mode)
- **Storage:** `localStorage[STORAGE_KEY]` with JSON parsing
- **Integration:** Add `themePreference` (light/dark) to settings object; extend saveSettings/loadSettings to persist

### SVG Ring & Animation
- **Ring SVG:** Uses `--accent` custom property for stroke color
- **Animation:** requestAnimationFrame loop updates ring progress based on phase timing
- **Light theme requirement:** Ring stroke color must adapt (warm accent in light theme)
- **Implementation:** Define separate color values for light/dark; CSS applies based on theme

### Responsive CSS Patterns
- **Existing flexbox/grid:** `display: grid; place-items: center` used throughout for centering
- **Landscape breakpoint:** Add new media query `@media (orientation: landscape) { ... }` or width-based breakpoint
- **Layout containers:** Wrap ring and controls in flex containers for side-by-side layout in landscape

---

## Canonical Refs

**Required reading for researcher & planner:**

1. `.planning/ROADMAP.md` — Phase 2 success criteria and requirements
2. `.planning/REQUIREMENTS.md` — THEME-01, THEME-02, UX-01, UX-02, UX-03 requirements
3. `CLAUDE.md` (project guidelines) — Vanilla JS only, single-file architecture, CSS patterns
4. `.planning/codebase/STRUCTURE.md` — CSS block structure, theme color locations, SVG integration
5. `.planning/codebase/CONVENTIONS.md` — CSS variable naming, responsive design patterns
6. `.planning/phases/01-session-history-vibration/01-CONTEXT.md` — Prior phase decisions on responsive design, localStorage patterns, CSS breakpoints

---

## Assumptions & Unknowns

1. **Exact light theme colors:** Warm palette defined conceptually (cream bg, brown text, gold accent). Precise hex values TBD during implementation (designer may refine based on WCAG AA validation)
2. **clamp() formula:** CSS clamp() requires specific vw-based calculation (e.g., `clamp(12px, 1vw + 10px, 16px)`). Exact formula TBD during implementation to match designer intent
3. **Landscape breakpoint edge cases:** Small landscape phones (e.g., 480px wide in landscape) may need special handling. Threshold of "width > height" may need refinement based on testing
4. **Controls layout on right:** No current CSS for side-by-side layout; new flexbox/grid rules needed. Ensure history cards reflow naturally in constrained width
5. **Theme toggle animation:** 220ms fade transition may apply to all color changes (bg, text, ring). Confirm no flashing or lag on toggle

---

## Notes for Downstream Agents

**Researcher:**
- Validate that CSS `clamp()` is supported in target browsers (modern evergreen browsers: Chrome, Edge, Firefox, Safari 2023+)
- Research warm color palettes for light theme; suggest specific hex values that meet WCAG AA contrast
- Check if SVG stroke colors respond correctly to CSS custom property changes during animation
- Verify localStorage quota impact with new theme preference key

**Planner:**
- Define CSS custom properties for light theme (--bg-light, --text-light, --accent-light, --textSoft-light, etc.) or toggle existing props
- Implement theme toggle button in settings (icon only: ☀️/🌙); add click handler to swap custom property values and save to localStorage
- Replace fixed breakpoint media queries with CSS `clamp()` for body, heading, timer, button, history text sizes
- Add landscape media query (`@media (orientation: landscape)` or `(min-width: ...)`) for side-by-side layout
- Implement flex/grid wrapper for ring (left 50%) and controls/history (right 50%) in landscape
- Extend saveSettings() / loadSettings() to persist themePreference key
- Ensure smooth 220ms transition when theme toggles (CSS transition on custom properties)
- Add layout toggle in settings for landscape ring/controls swap (if time permits; can defer to Phase 3)

**No new frameworks, no external libraries — stay vanilla JS and single file.**

---

## Deferred Ideas

- **Accessibility focus states:** Phase 2 focuses on visual design; keyboard focus states (Tab navigation, ARIA labels) deferred to Phase 3 or separate accessibility audit
- **System preference auto-detect:** `prefers-color-scheme` media query integration deferred; users start with dark theme and toggle manually
- **Theme persistence across devices/cloud:** Cloud sync of theme preference out of scope (localStorage single-device only)
- **SVG icon optimization for theme:** Consider using SVG filters or mask-image for dynamic icon theming; currently using font emoji (☀️/🌙) for simplicity

---

*Context captured by interactive discussion on 2026-06-04*
