# Phase 1: Session History & Vibration — Context

**Date:** 2026-06-03  
**Phase:** 1 of 2  
**Status:** Context captured, ready for planning

---

## Domain

Phase 1 delivers two interconnected capabilities:

1. **Vibration Reliability** — Fix haptic feedback to trigger reliably at each phase transition during breathing sessions
2. **History Management** — Enable pagination, configuration, and improved visibility of past sessions

---

## Requirements (Locked by ROADMAP.md)

1. Vibration haptics trigger reliably at each phase transition during active breathing session
2. User can configure maximum session logs to store (numeric input or "unlimited" option, persisted to localStorage)
3. User can set pagination size (number of logs visible per page) and see changes apply immediately
4. User can navigate between pages of session history (Page 1, 2, 3...) with next/previous controls
5. Session history cards are visually scannable, displaying duration, preset name, and date in clear format

---

## Implementation Decisions

### Vibration Reliability

- **Priority:** Fix reliability first — ensure haptic feedback works consistently across browsers/devices
- **Validation approach:** Minimal — fix the bug without heavy validation checks; graceful degradation if unsupported (current pattern)
- **Trigger frequency:** Every phase transition (four pulses per breathing cycle: Inhale, Hold, Exhale, Hold2)
- **Testing strategy:** Claude to recommend approach based on code analysis (likely: test on localhost first, then device testing after deploy)

**Rationale:** Vibration is a secondary feedback channel; fixing the core implementation is priority one. Users on unsupported devices already rely on visual/audio cues.

### History Storage Configuration

- **Max logs setting:** Numeric input field — flexible, allows any value, simple UX
- **Default cap:** TBD (researcher to propose based on localStorage quota analysis)
- **Apply behavior:** Changes apply immediately (no Save button required)

**Rationale:** Numeric input gives power users flexibility while remaining straightforward. Immediate apply aligns with modern UX patterns (like themes).

### History Pagination

- **Items per page:** Fixed at 5 items per page (prevents inconsistent pagination logic)
- **Navigation controls:** Previous / Next buttons with page indicator (e.g., "Page 2 of 5")
- **Apply behavior:** Changes visible immediately when users adjust pagination or max storage setting

**Rationale:** Fixed pagination keeps logic simple and predictable. Previous/Next is accessible and doesn't require clicking through many page buttons.

### History Card Content & Layout

**Content to display:**
- Duration (current) — e.g., "18 min"
- Preset name (current) — e.g., "Relax"
- Cycle count (new) — e.g., "10 cycles"
- Date (current) — e.g., "Jun 3"
- Visual preset indicator (new) — color or icon badge showing which preset

**Layout:**
- Single-line summary format: `Relax [icon] • 18 min • 10 cycles • Jun 3`
- Standard spacing (current density — ~5 cards per page on mobile)
- No per-card interactions (read-only); history size controlled only by global storage cap

**Rationale:** Single-line keeps cards compact and scannable while adding useful cycle count and visual differentiation. Read-only simplifies UX and avoids accidental deletions.

### Responsive Design

- **Card width:** Full width with edge padding (12–16px mobile, 20px desktop) — consistent with current layout
- **Font sizing:** Fixed breakpoints (mobile 320px, tablet 480px, desktop 1024px) — matches existing CSS structure
- **Landscape orientation:** Stay single-column — no layout shift between portrait/landscape
- **Text handling:** Abbreviate labels when needed (e.g., "18 m" / "6 cy") — preserves all info, improves mobile density
- **Existing breakpoints:** Reuse current breakpoints in style block (already present)

**Rationale:** Fixed breakpoints align with existing codebase patterns and avoid CSS complexity (no calc/clamp). Abbreviation keeps info visible on constrained screens.

---

## Code Context (Reusable Assets & Patterns)

### Existing Vibration Code
- **Location:** `index.html` lines 1419–1476 (SOUND/VIBRATION section)
- **Key function:** `vibrate()` — calls `navigator.vibrate(50)` for haptic pulse
- **Integration point:** Called from `advanceToNextPhase()` when phase transitions occur
- **Issue:** Likely timing or permission issue preventing reliable trigger; needs investigation

### Existing History Rendering
- **Location:** `index.html` lines 1356–1393 (SESSION HISTORY section)
- **Key function:** `renderHistory()` — formats and displays past sessions
- **Current output:** Max 14 entries; single-line display with duration, preset, date
- **Storage:** `localStorage[HISTORY_KEY]` (JSON array of session objects)
- **Session object schema:** `{ date: string, durationMs: number, cycles: number }` — note: cycles field already present in saved data

### Configuration & Constants
- **Location:** `index.html` lines 1044–1128 (CONFIG section)
- **Key objects:**
  - `SESSION` — controls max history entries, date format, storage behavior
  - `UI` — all user-facing label strings
  - `PRESETS` — breathing presets with theme colors (reuse for visual indicators)
- **Pattern:** Object.freeze() used throughout for immutable config

### Settings Persistence
- **Pattern:** `saveSettings()` (line ~1316) and `loadSettings()` — already handle toggles
- **Approach:** New settings (max logs cap, pagination size) integrate into existing `saveSettings()` / `loadSettings()` flow
- **Storage keys:** Use existing `STORAGE_KEY` pattern

### Responsive Design System
- **CSS media queries:** Already present at ~@media (max-width: 480px) and (max-width: 360px)
- **Theme colors:** CSS custom properties `--bg`, `--accent`, etc. — preset colors available for visual indicators
- **Pattern:** Hand-written CSS; no framework. Extend existing breakpoints and variable system.

---

## Canonical Refs

**Required reading for researcher & planner:**

1. `.planning/ROADMAP.md` — Phase 1 success criteria locked here
2. `.planning/PROJECT.md` — Core value: "Reliable, uninterrupted breathing guidance with verifiable history"
3. `CLAUDE.md` (project guidelines) — Vanilla JS only, single-file architecture, localStorage patterns
4. `.planning/codebase/STRUCTURE.md` — Detailed line-by-line reference for index.html sections (vibration at 1419–1476, history at 1356–1393)
5. `.planning/codebase/CONVENTIONS.md` — Naming patterns, code style, error handling (silent fail pattern for Web APIs)
6. `.planning/codebase/STACK.md` — Browser APIs in use, graceful degradation patterns, localStorage quota context

---

## Assumptions & Unknowns

1. **Vibration API support:** What percentage of target users (mobile, desktop, PWA) can access Vibration API? (Researcher to investigate)
2. **localStorage quota:** How much space remains for expanding history beyond 14 entries? (Researcher to check on different browsers)
3. **Cycles field:** Current code saves `cycles` in session objects — confirm this is persisted reliably and accessible for display
4. **Preset visual indicators:** Should color match the preset's theme color (from PRESETS config) or use custom color scheme? (Decision deferred to designer/planner)

---

## Notes for Downstream Agents

**Researcher:**
- Investigate why vibration is not triggering reliably (likely: timing, browser permission, or API call issue)
- Confirm localStorage quota for history expansion
- Check if cycles field is already being saved in session data

**Planner:**
- History card layout is single-line; update renderHistory() to include cycle count + visual preset badge
- Add storage cap and pagination size settings to PERSISTENCE section
- Extend saveSettings() / loadSettings() to handle new config keys
- Update CSS breakpoints to match responsive decisions (existing framework, no new complexity)

**No new frameworks, no external libraries — stay vanilla JS and single file.**

---

## Deferred Ideas

None at this time. All Phase 1 scope is captured above.

---

*Context captured by interactive discussion on 2026-06-03*
