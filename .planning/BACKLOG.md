# Backlog

## 🚧 Phase 3: Critical Bugs & UX Polish

High-priority items from Phase 2 testing + micro-interactions polish.

### Bug Fixes (2 items)
1. **Icon Size Inconsistencies** — Standardize info/fullscreen icons to 32x32 across all views
2. **Vibration API Not Working** — Debug navigator.vibrate() on Android/Chrome; verify pattern arrays

### UX Polish (1 item)
4. **Micro-Interactions & Visual Polish** — Smooth theme transitions, button hover states, countdown timing

**Scope estimate:** 1–2 plans  
**Priority:** High (fixes Phase 2 blockers)

---

## 📋 Phase 4: 2-Column Landscape Layout Redesign & Light Theme

Substantial visual redesign for horizontal view (landscape orientation) + light theme readability improvements.

### Layout Fixes (1 major item)
1. **2-Column Landscape Layout Complete Overhaul**
   - History panel visibility — currently hidden; add scrollable area
   - Fullscreen button placement — move from top-right to left panel (below ring)
   - Start/Reset buttons — proper vertical centering in right column
   - Session row positioning — reposition below ring in left column
   - Overall grid layout — refinement to accommodate all elements without overlap

### Theme Improvements (1 item)
2. **Light Theme Readability & Contrast** — Increase text/background contrast and improve color palette per LIGHT-THEME-READABILITY-REPORT.md (WCAG 4.5:1 compliance for normal text, 3:1 for large text)

**Scope estimate:** 3–4 plans  
**Priority:** High (Phase 2 regression; major UX gap in landscape mode; accessibility/usability issue in light mode)  
**Dependencies:** Phase 3 (icon sizing must be resolved first)
**Reference:** See LIGHT-THEME-READABILITY-REPORT.md for detailed contrast analysis

---

## 🎯 Phase 5: Advanced History Features

User-facing feature additions for history management and data portability.

### Features (2 items)
1. **Import/Export History** — Export sessions to JSON/CSV; import previously exported data
2. **Clear History with Confirmation** — Safe delete with modal confirmation to prevent accidental loss

**Scope estimate:** 1–2 plans  
**Priority:** Medium (nice-to-have; enables data portability)  
**Dependencies:** Phase 3 (UI polish groundwork)

---

## Backlog: Deferred for v0.6+

### Accessibility (full keyboard + screen reader support)
- Keyboard Focus States & Tab Navigation — Add visible focus outlines, logical tab order
- ARIA Labels & Accessibility Attributes — Screen reader support for buttons, inputs, sliders
- Settings Panel Layout Toggle (Optional) — Allow ring-left vs ring-right choice in landscape

### Data & Analytics
- Log Unfinished Sessions — Track incomplete sessions (cycle count, elapsed time)
- Enhance History Data — Add preset mode, phase-level timing per session
- Version Number in Info Popup — Display app version (v0.4.0, etc.)

### Visual
- Background tint transitions in dark mode
