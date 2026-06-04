# Backlog

## Phase 3 Known Issues & UX Bugs (Discovered in Phase 2)

High-priority issues identified during Phase 2 testing that need immediate attention in Phase 3.

### 1. Icon Size Inconsistencies
- Info icon (ⓘ) is 34x34 in vertical view, others are 32x32
- Fullscreen button is also 34x34, should be 32x32 for consistency
- Create single sizing standard across all corner/header icons
- Affects visual alignment and consistency

### 2. 2-Column Landscape Layout Issues
Critical layout problems in horizontal 2-column view:
- **History panel visibility** — history is completely hidden in 2-column mode, should be scrollable or visible
- **Fullscreen button placement** — conflicts with clear button placement; should be moved to buttons panel (left side) at the top
- **Start/Reset buttons alignment** — not properly centered in right column, need vertical centering
- **Session row positioning** — should appear below the ring in left column for better visual flow
- **Overall grid layout** — needs refinement to accommodate all elements without overlap

### 3. Vibration API Not Working
- Vibration cues are not triggering on Android/Chrome devices
- Check `navigator.vibrate()` implementation and timing
- Verify pattern arrays are correct: `[duration]` or `[on, off, on, ...]`
- Test on multiple Android devices
- May need to add fallback or graceful degradation message

### 4. Light Theme Visibility Issue
- Light theme is unusable — insufficient contrast or visibility
- Colors may be too similar to white background
- Need to review and improve light mode color palette
- Ensure text, buttons, and icons are visible in light mode
- Consider adding dedicated light mode testing
- See `LIGHT-THEME-FAILURE.md` for detailed analysis and screenshots

---

## Phase 3 Deferred (v0.4 Accessibility & UX Polish)

Deferred from Phase 2 to focus on v1 requirements. Ready to implement after v0.4 release.

### 1. Keyboard Focus States & Tab Navigation
- Add visible focus outlines to all interactive elements (buttons, inputs, toggles)
- Ensure Tab key navigates through all controls in logical order
- Support keyboard-only operation (no mouse required)
- Test with keyboard accessibility scanners (axe, WAVE)

### 2. ARIA Labels & Accessibility Attributes
- Add `aria-label` to icon-only buttons: theme toggle (☀️/🌙), sound toggle, vibration toggle
- Add `title` attributes as tooltips
- Label form inputs (duration sliders, preset buttons)
- Verify with screen reader testing (NVDA, JAWS, VoiceOver)

### 3. Micro-Interactions & Visual Polish
- Add smooth transitions to theme toggle (refine 220ms timing)
- Enhance button hover/active states with subtle animations
- Polish countdown overlay animation timing
- Smooth phase transition color shifts

### 4. Settings Panel Layout Toggle (Optional)
- Add optional toggle to swap ring and controls layout in landscape mode
- Allow users to choose: ring-left (current) or ring-right
- Persist preference to localStorage
- Deferred due to time constraints; low priority

---

## Feature Requests for v0.5+

### 1. Import/Export History
- Allow users to export their session history to a file format (JSON or CSV)
- Allow users to import previously exported history
- Export should include: date, durationMs, cycles, and preset mode if available
- Import should validate file format and warn on conflicts
- Add buttons to settings panel for easy access
- Useful for backup, data portability, and cross-device sync
- User feedback: This is a high-value feature for data portability

### 2. Log Unfinished Sessions
- Track incomplete sessions (sessions that were stopped before completion)
- Store: cycle count reached, seconds elapsed
- Display in history alongside completed sessions
- Helps users track partial progress and trends

### 3. Safe Clear Button with Confirmation
- Add a "Clear History" button in settings panel
- Display confirmation modal popup before clearing (matching settings/info panel style)
- Prevent accidental data loss
- Show warning about irreversibility
- Include option to cancel the operation
- User feedback: This is critical — users should be prompted before data deletion

### 4. Enhance History Data
Current history: `date`, `cycles`, `durationMs`

Add:
- `mode` — which preset was used (Relax / Box / 4-7-8)
- Phase-level timing — duration for each phase (Inhale, Hold, Exhale, Hold2) per cycle
- Optional: notes or tags per session

### 5. Version Number in Info Popup
- Display app version in bottom of info panel
- Format: "v0.4.0" or similar
- Update on each release
- Helps users verify they have the latest version
