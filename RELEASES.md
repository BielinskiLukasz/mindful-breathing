# Release Notes

## 🟩 **v0.6.0**  
📅 *Release date: 2026‑07‑01*  
🔖 Commit: `2cdbcb3`  
👤 Author: @BielinskiLukasz  

### **What's Changed**
- CSS Grid landscape layout, WCAG AA light theme, and session history data management (export, import, clear)

### **Added**
- CSS Grid `1fr 1fr` landscape layout — history panel visible alongside the breathing ring on 600px+ screens  
- Per-phase atmospheric light-mode colors (sage/lavender/sky/cream tints for Inhale/Hold/Exhale/Hold2)  
- JSON export — downloads `mb-sessions-YYYY-MM-DD.json` with all sessions  
- CSV export — downloads with date, duration (M:SS), cycles, and preset columns  
- JSON import — merges with existing history, skips exact-date duplicates, shows import feedback  
- Clear history with native `<dialog>` confirmation — "Delete all sessions" required; ESC/backdrop dismiss handled natively  

### **Changed**
- WCAG AA light theme contrast: accent `#d4a574` → `#a0662e` (5.2:1), textSoft `#a89e8c` → `#6b6058` (5.3:1)  
- `applyThemeForCurrentPhase()` branches on `isDarkMode` for per-phase light and dark color application  
- Body `transition: background-color` (not shorthand) for smooth phase-tint animation in light mode  
- Compact scrollable layout retained for small-phone landscape (height ≤500px)  

### **Fixed**
- Startup crash and icon regression introduced during history data management implementation  
- Light theme CSS gaps: info/settings panels and ring had hardcoded dark-only rgba values  

### **Full Changelog**
`v0.5.0...v0.6.0`

---

## 🟧 **v0.5.0**  
📅 *Release date: 2026‑06‑30*  
🔖 Commit: `051bbe2`  
👤 Author: @BielinskiLukasz  

### **What's Changed**
- Critical bug fixes, CSS micro-interactions, countdown audio, and cross-browser validation

### **Added**
- Countdown audio beeps (350 Hz) at 3s/2s/1s marks, distinct from the 520 Hz phase-transition beep  
- Skip button and ESC key handler to dismiss the countdown overlay  
- Button press feedback via CSS `:active` inset shadow and semi-transparent overlay  
- Phase transition ring flash — SVG ring brightens briefly (200ms) on each phase advance  
- First-visit gesture hint shown once, dismissed on any user interaction, persisted via localStorage  
- Duration input error flash — 400ms red highlight on out-of-range or non-numeric values  
- `localStorage` `QuotaExceededError` surfaced as an inline warning banner (5-second display)  

### **Changed**
- `DURATION_RANGE = { min: 1, max: 300 }` constant as single source of truth for all phase duration clamping  
- Vibration toggle UI hidden (`display:none`) as known limitation — Samsung/Android OS restriction  

### **Fixed**
- Countdown timer orphan accumulation on rapid Start/Stop — `cancelCountdown()` now called in `stop()`  
- Duration inputs accepted out-of-range values — clamped to 1–300s via `DURATION_RANGE` constant  
- localStorage quota exceeded silently — `QuotaExceededError` now caught and shown to user  

### **Full Changelog**
`v0.4.1...v0.5.0`

---

## 🟦 **v0.4.1**  
📅 *Release date: 2026-06-05*  
🔖 Commit: `8015112`  
👤 Author: @BielinskiLukasz  

### **What's Changed**
- Responsive typography with viewport-aware scaling (clamp-based)
- Full landscape layout redesign with 2-column adaptive grid
- Theming enhancements for dark and light modes
- Expanded session history visibility and responsiveness

### **Added**
- Responsive font sizing that adapts to viewport width (clamp-based scaling)
- Full landscape layout redesign with 2-column adaptive grid
- Icon row horizontal reflow in landscape mode
- Session history visibility in landscape view
- Improved dark mode default colors for better contrast
- Light theme color refinements
- Enhanced touch targets for mobile users
- Smooth 220ms transitions on theme changes

### **Changed**
- Ring and controls centered on both portrait and landscape layouts
- Status and goal rows repositioned for responsiveness across orientations
- Cycle counter and phase labels responsive to layout changes
- Icon sizing and button positioning optimized across orientations
- Consistent 8px spacing rhythm across all orientations

### **Fixed**
- Icon sizing inconsistencies across orientations
- Button positioning in landscape mode
- Session history panel positioning in various layouts

### **Full Changelog**
`v0.4.0...v0.4.1`

---

## 🟦 **v0.4.0**  
📅 *Release date: 2026‑06‑04*  
🔖 Commit: `140948f`  
👤 Author: @BielinskiLukasz  

### **What's Changed**
- History pagination, storage cap, session preset tracking (#10)

### **Added**
- Session history now displays with pagination navigation (Previous/Next buttons)  
- Configurable storage cap for session history (default: 14 entries)  
- Preset badge shown in session history cards (displays which pattern was used)  
- Session history cards fully responsive for landscape and mobile layouts  
- Settings initialization on app load ensures all custom durations and toggles persist  
- Improved responsive CSS for session history rendering across all breakpoints

### **Changed**
- Session history rendering completely rewritten to support pagination  
- History card styling improved for consistency with app theme  
- Landscape mode CSS extended to accommodate larger history panel

### **Fixed**
- —

### **Full Changelog**
`v0.3.3...v0.4.0`

---

## 🟨 **v0.3.3**  
📅 *Release date: 2026‑06‑03*  
🔖 Commit: `1e78b88`  
👤 Author: @BielinskiLukasz  

### **What's Changed**
- Bugfix release (#7)

### **Added**
- —

### **Changed**
- —

### **Fixed**
- Focus handling corrected on done overlay for improved keyboard accessibility

### **Full Changelog**
`v0.3.2...v0.3.3`

---

## 🟧 **v0.3.2**  
📅 *Release date: 2026‑05‑11*  
🔖 Commit: `aa86631`  
👤 Author: @BielinskiLukasz  

### **What's Changed**
- Settings panel and mode indicator (#5)

### **Added**
- Settings panel (gear icon) provides centralized control for app customization  
- Editable cycle count in settings (separate from the main goal input)  
- Per‑phase breathing duration editing moved to settings panel  
- Breathing preset selection integrated into settings panel  
- Mode indicator showing currently active breathing pattern  
- Info panel (ⓘ icon) consolidates shortcuts and informational content  
- Improved landscape orientation layout with centered UI and icon alignment

### **Changed**
- Settings icon upgraded from placeholder to proper cogwheel symbol  
- Keyboard shortcuts and breathing guidance moved from always‑visible UI to collapsible info panel  
- Icon spacing refined for better visual balance  
- Text and button alignment centered in landscape orientation

### **Fixed**
- Landscape orientation layout no longer allows icon overlap  
- Main UI properly centered in landscape mode

### **Full Changelog**
`v0.3.1...v0.3.2`

---

## 🟦 **v0.3.1**  
📅 *Release date: 2026‑05‑07*  
🔖 Commit: `a376179`  
👤 Author: @BielinskiLukasz  

### **What's Changed**
- Icon buttons: vibration replaces text checkbox (#3)  
- Input validation: goal cycle clamped to 1–50 (#4)

### **Added**
- Vibration icon button replaces the vibration text checkbox (matches the sound toggle style)  
- Sound and vibration icon buttons grouped with the ⓘ info button in a top‑left corner column

### **Changed**
- Goal cycle input clamped to 1–50 in both HTML (max attribute) and JS  
- Info button icon replaced from Unicode ⓘ to inline SVG

### **Fixed**
- —

### **Full Changelog**
`v0.3.0...v0.3.1`

---

## 🟩 **v0.3.0**  
📅 *Release date: 2026‑05‑07*  
🔖 Commit: `1354a22`  
👤 Author: @BielinskiLukasz  

### **What's Changed**
- Icon buttons, PWA features, landscape layout, and info panel (#2)

### **Added**
- Speaker icon button replaces the sound text checkbox  
- Two‑column landscape layout for mobile (ring left, controls right)  
- Collapsible info panel behind an ⓘ icon button  
- "Clear" button in the session history panel  
- Favicon, PWA manifest, service worker (offline caching, Add to Home Screen)  
- Inspirational quote on the 3‑2‑1 countdown overlay  
- Session progress bar filling continuously within each cycle

### **Changed**
- Status text shows "Ready" or "Paused" instead of "Stopped"; elapsed time hidden at zero  
- Start/Stop styled as primary action; Reset as secondary  
- Past Sessions redesigned as card‑style items  
- Responsive breakpoints at 480px and 360px  
- Session history dates forced to en‑US locale

### **Fixed**
- Vibration pattern simplified to a single 50ms pulse  
- Priming no longer fires when vibration is disabled

### **Full Changelog**
`v0.2.1...v0.3.0`

---

## 🟨 **v0.2.1**  
📅 *Release date: 2026‑05‑03*  
🔖 Commit: `c6955fe`  
👤 Author: @BielinskiLukasz  

### **What's Changed**
- Documentation and file organization

### **Added**
- README for Mindful Breathing application

### **Changed**
- Updated durations for breath presets  
- Renamed `Mindful-Breathing.html` → `index.html`

### **Fixed**
- —

### **Full Changelog**
`v0.2.0...v0.2.1`

---

## 🟧 **v0.2.0 – Initial release**  
📅 *Release date: 2026‑04‑30*  
🔖 Commit: `38c874d`  
👤 Author: @BielinskiLukasz  

### **What's Changed**
- Core breathing app with presets, animations, and session history

### **Added**
- Preset breathing patterns (Relax, Box, 4‑7‑8)  
- Breathing animation circle  
- Session goal with cycle counter  
- Screen wake lock  
- Per‑phase duration editing  
- Elapsed session timer  
- Idle breathing pulse animation  
- 3‑2‑1 countdown overlay  
- Next phase label  
- Keyboard shortcuts (Space, R, F)  
- Per‑phase breathing guidance text  
- Ambient brightness animation  
- Collapsible session history  
- Estimated session duration next to goal input

### **Changed**
- Relax pattern updated (Pause → Hold)  
- Polish done overlay improvements

### **Fixed**
- Sound and vibration not working on mobile browsers

### **Full Changelog**
`v0.1.0...v0.2.0`

---

## 🟫 **v0.1.0 – Initial release**  
📅 *Release date: 2026‑04‑02*  
🔖 Commit: `857ac6e`  
👤 Author: @BielinskiLukasz  

### **What's Changed**
- Project initialization with breathing timer and UI foundations

### **Added**
- Initial project structure  
- Simple breathing timer  
- Start/stop button  
- Reset button  
- Color scheme  
- Animated progress ring  
- Sound and vibration support  
- English UI  
- Configuration panel

### **Changed**
- —

### **Fixed**
- —
