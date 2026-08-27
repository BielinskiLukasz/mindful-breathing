---
slug: light-mode-panels-ring
date: 2026-06-30
status: planned
---

# Light Mode: Panels + Ring Fix

## Goal
Fix two light-mode visual issues:
1. Info and Settings pop-up panels are dark (#1a1a1a) in light mode — unreadable
2. SVG ring background (.ringBg) is white (rgba(255,255,255,0.12)) — invisible on light background

## Approach
Add CSS variables for all panel and ring colors. Replace hardcoded dark-only rgba values with variables. Extend LIGHT_THEME / DARK_THEME objects and applyThemeColors() to drive the new variables.

## New CSS Variables (in :root, dark defaults)
| Variable              | Dark default                  | Light value                  |
|-----------------------|-------------------------------|------------------------------|
| --ringBg              | rgba(255,255,255,0.12)        | rgba(92,64,51,0.15)          |
| --overlayBg           | rgba(0,0,0,0.85)              | rgba(0,0,0,0.55)             |
| --panelBg             | #1a1a1a                       | #ebe7e1                      |
| --panelBorder         | rgba(255,255,255,0.12)        | rgba(92,64,51,0.20)          |
| --panelTitle          | rgba(255,255,255,0.75)        | #5c4033                      |
| --panelSubtext        | rgba(255,255,255,0.45)        | rgba(92,64,51,0.60)          |
| --panelText           | rgba(255,255,255,0.70)        | rgba(92,64,51,0.80)          |
| --panelCloseColor     | rgba(255,255,255,0.55)        | rgba(92,64,51,0.55)          |
| --panelCloseHoverBg   | rgba(255,255,255,0.06)        | rgba(92,64,51,0.08)          |
| --panelCloseHoverColor| rgba(255,255,255,0.9)         | #5c4033                      |
| --panelKbdBg          | rgba(255,255,255,0.08)        | rgba(92,64,51,0.08)          |
| --panelKbdBorder      | rgba(255,255,255,0.15)        | rgba(92,64,51,0.20)          |
| --panelKbdColor       | rgba(255,255,255,0.85)        | #5c4033                      |

## CSS Replacements
- .ringBg stroke → var(--ringBg)
- .settingsOverlay / .infoOverlay background → var(--overlayBg)
- .settingsPanel / .infoPanel background → var(--panelBg), border → var(--panelBorder)
- .settingsTitle / .infoTitle color → var(--panelTitle)
- .settingsCloseBtn / .infoCloseBtn border/color → var(--panelBorder) / var(--panelCloseColor)
- :hover states → var(--panelCloseHoverBg) / var(--panelCloseHoverColor)
- .settingsPlaceholder / .infoSectionLabel / .settingsLabel color → var(--panelSubtext)
- .infoList color → var(--panelText)
- .infoList kbd bg/border/color → var(--panelKbdBg) / var(--panelKbdBorder) / var(--panelKbdColor)

## Files Changed
- index.html (CSS :root, panel selectors, JS LIGHT_THEME, DARK_THEME, applyThemeColors)
