# Phase 8: Accessibility & CSV Import - Context

**Gathered:** 2026-07-20
**Status:** Ready for planning

<domain>
## Phase Boundary

Add full keyboard accessibility, screen reader support, visible focus indicators, and CSV session history import to the existing single-file app.

**In scope:** A11Y-01 (keyboard navigation), A11Y-02 (ARIA labels and aria-live), A11Y-03 (visible focus indicators), HIST-12 (CSV import)
**Out of scope:** Custom presets (Phase 9), streak tracking (Phase 10), WCAG AAA compliance (deferred), vibration API re-enable (deferred)

</domain>

<decisions>
## Implementation Decisions

### Focus Indicators (A11Y-03)

- **D-01:** Focus ring style: `outline: 2px solid var(--accent)` with `outline-offset: 3px`. Uses the app's warm theme color — gold in light mode, teal in dark mode. Must verify 3:1 contrast on both themes; no hardcoded color.
- **D-02:** Apply focus styles on `:focus-visible` only (not `:focus`). Keyboard navigation shows the ring; mouse/touch clicks do not. Use the `:focus-visible` pseudo-class throughout.

### ARIA and Screen Reader Support (A11Y-02)

- **D-03:** Add `aria-live="polite"` directly to the existing `#phaseEl` element. Phase label already updates on every phase transition — aria-live makes those updates announced automatically. No new element needed.
- **D-04:** Polite politeness only — phase changes happen every 4–8 seconds, plenty of time between announcements. `aria-live="assertive"` is not appropriate here.
- **D-05:** Announced events: phase name changes (Inhale, Hold, Exhale, Hold 2) + session start/stop state. Do NOT announce cycle count or elapsed time on every second — that would be unusable.

### Keyboard Navigation (A11Y-01)

- **D-06:** Tab order follows DOM order — no explicit `tabindex` values unless a specific element needs to be included that isn't naturally focusable. Researcher to confirm which elements are currently skipped by keyboard.
- **D-07:** All interactive controls must be reachable: preset buttons, duration sliders, Start/Pause button, Reset button, corner icon buttons (info, settings, sound, theme, fullscreen), history action buttons (export, import, clear), pagination controls.

### Panel Focus Management (A11Y-01)

- **D-08:** When a panel (info, settings, history) opens, move focus to that panel's close button immediately.
- **D-09:** Trap focus inside the open panel — Tab cycles only within the panel's focusable elements. When panel closes, return focus to the button that opened it.
- **D-10:** Disable global keyboard shortcuts (Space, R, F) while any panel is open. ESC still closes the open panel. Add a panel-open guard at the top of the keyboard shortcut handler.

### CSV Import (HIST-12)

- **D-11:** Upgrade the existing import button to accept both JSON and CSV files: change `accept` attribute to `".json,.csv"`. Handler detects file extension and routes to JSON or CSV parser. No second import button.
- **D-12:** Strict format only — parse exactly the app's own CSV export format. Expected: header row `Date,Duration,Cycles`, duration in M:SS format, one row per session. Reject files that don't match this structure.
- **D-13:** Parse M:SS duration back to milliseconds for the `durationMs` field in the history object.
- **D-14:** Import incomplete sessions if they parse correctly. The incomplete flag should be inferred or preserved — researcher to check the current CSV export format to confirm whether `Incomplete` is currently written to the CSV.
- **D-15:** Import feedback: flash banner with counts — e.g., "Imported 5 sessions, 2 skipped". Matches existing JSON import feedback pattern. No new dialog needed.
- **D-16:** Dedup strategy: same as JSON import — exact date string match. Do not overwrite existing sessions.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Requirements
- `.planning/REQUIREMENTS.md` — Full v1.1 requirements; Phase 8 items: A11Y-01, A11Y-02, A11Y-03, HIST-12 with acceptance criteria
- `.planning/ROADMAP.md` — Phase 8 goal and success criteria

### Project Constraints
- `CLAUDE.md` — Vanilla JS only, single file, no frameworks, no npm, graceful API degradation, no `throw` anywhere

### Codebase Maps
- `.planning/codebase/CONVENTIONS.md` — Naming patterns, CSS class conventions, error handling patterns, section divider format
- `.planning/codebase/STRUCTURE.md` — index.html section map (KEYBOARD SHORTCUTS: lines 1886–1896, PANELS: 1829–1883, SESSION HISTORY: 1356–1393, CSS sections: 46–1040)

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `#phaseEl` (index.html): existing phase label element — add `aria-live="polite"` attribute directly (D-03)
- Existing import button + hidden file `<input>` in history panel — change `accept` attribute to `.json,.csv` (D-11)
- `flashMsg()` or equivalent flash feedback pattern — reuse for CSV import count feedback (D-15)
- KEYBOARD SHORTCUTS section (lines 1886–1896): existing Space/R/F handler — add panel-open guard at top (D-10)
- Existing close buttons on panels (info, settings, history) — focus target on panel open (D-08)

### Established Patterns
- CSS custom properties (`--accent`, `--bg`) drive all theming — focus outline must use `var(--accent)`, not hardcoded color
- Silent `try/catch` for all API calls — CSV parsing errors should be caught silently; invalid rows skipped
- `Object.freeze()` for config constants — do not mutate
- Section dividers `/* ====== SECTION NAME ====== */` must be preserved
- Import dedup by exact date string (set in Phase 5/v0.6) — CSV import uses same strategy (D-16)
- `@media (hover: hover) and (pointer: fine)` hover guard — focus styles are keyboard-specific and should NOT be inside this guard; they live in a bare `:focus-visible` rule

### Integration Points
- `:focus-visible` CSS rules: add to button/control styles section — applies globally across all interactive elements
- Panel open handlers (in PANELS section, lines 1829–1883): add `focus()` call to close button after panel shows (D-08) and focus trap logic
- Keyboard handler (KEYBOARD SHORTCUTS, lines 1886–1896): add early-return guard when any panel is open (D-10)
- CSV parser: add to PERSISTENCE section or as a standalone function near the existing JSON import handler in SESSION HISTORY
- `accept` attribute on file `<input>`: one-line change in history panel HTML

</code_context>

<specifics>
## Specific Ideas

- Focus ring: `outline: 2px solid var(--accent); outline-offset: 3px` — warm color, small gap so it floats visually above the element
- ARIA live: literal attribute addition `aria-live="polite"` on `#phaseEl` — minimal change, high impact
- CSV import: route on file extension (`.endsWith('.csv')` check) in the import handler — one handler, two branches
- Panel guard: `if (anyPanelOpen()) return;` at top of keyboard shortcut switch — simple boolean check

</specifics>

<deferred>
## Deferred Ideas

- None — discussion stayed within phase scope

</deferred>

---

*Phase: 8-Accessibility & CSV Import*
*Context gathered: 2026-07-20*
