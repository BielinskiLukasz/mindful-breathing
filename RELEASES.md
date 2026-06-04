# Release Notes

## v0.4.0 — Responsive Design & Landscape Layout (Phase 2)

**Status:** Released  
**Date:** 2026-06-05

### What's New

#### Responsive Typography & Layout
- Responsive font sizing that adapts to viewport width (clamp-based scaling)
- Improved readability on small screens and tablets
- Maintained visual hierarchy across all device sizes

#### Landscape Orientation Support
- Full landscape layout redesign with 2-column adaptive grid
- Icon row reflows horizontally in landscape mode
- Ring and controls centered on both portrait and landscape
- Session history visibility in landscape view
- Consistent 8px spacing rhythm across all orientations

#### Theming Enhancements
- Improved dark mode default colors for better contrast
- Light theme color refinements
- Smooth 220ms transitions on theme changes

#### Session Management
- Session history expanded with better visibility
- Status and goal rows properly positioned in all orientations
- Cycle counter and phase labels responsive to layout changes

### Bug Fixes

- Fixed icon sizing inconsistencies across orientations
- Improved button positioning in landscape mode
- Enhanced touch targets for mobile users

### Known Issues (Phase 3 Backlog)

- Light theme contrast issues on some devices
- Icon size inconsistencies between vertical and horizontal views need refinement
- History panel visibility in 2-column horizontal layout needs improvement
- Fullscreen button placement conflicts in horizontal 2-column view
- Vibration API reliability issues on some Android devices
- Clear history button lacks confirmation dialog

---

## v0.3.1 — Settings Panel & Phase Duration Customization

**Status:** Released  
**Date:** 2026-05-15

### Features

- Settings panel with preset selection
- Per-preset phase duration customization (1–30 seconds)
- Cycles per session configuration (1–20 cycles)
- Mode indicator badge showing active preset
- Dedicated info panel with keyboard shortcuts
- Landscape layout awareness

---

## v0.3.0 — Initial Release

**Status:** Released

### Core Features

- Three scientifically-backed breathing modes (Relax, Box, 4-7-8)
- Animated progress ring with phase synchronization
- Sound and vibration cues
- Session history tracking
- Fullscreen mode
- PWA installable
- Offline support
