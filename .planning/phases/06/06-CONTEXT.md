# Phase 6: Bug Fixes & Visual Polish - Context

**Gathered:** 2026-07-03
**Status:** Ready for planning

<domain>
## Phase Boundary

Four targeted improvements to the existing single-file PWA:
1. Fix landscape compact layout display on tall phones (height ≤ 500px in landscape)
2. Add smooth 220ms background color transitions when phase changes
3. Track and display incomplete (stopped mid-session) sessions in history
4. Display app version in the info panel footer

No new features or structural changes beyond these four requirements.

</domain>

<decisions>
## Implementation Decisions

### Incomplete Session Tracking (TRACK-01)

- **D-01:** Save threshold is **at least 1 full cycle completed**. Sessions stopped before completing a single cycle (including countdown stops) are discarded — not saved.
- **D-02:** Only the **Stop button** triggers saving an incomplete session. Reset discards current progress without saving. Page close is also not tracked.
- **D-03:** For incomplete session entries, `durationMs` = **elapsed time at stop** (`elapsedMs` state variable) — consistent with how complete sessions work.
- **D-04:** Sessions stopped during the **3-second countdown** are never saved, regardless of prior state.
- **D-05:** Schema addition: `incomplete: true` flag on the session entry object. Existing entries without the flag are treated as complete (backward compatible).

### Incomplete Session Display (TRACK-01)

- **D-06:** Incomplete entries render with **muted opacity (0.6)** on the full row.
- **D-07:** The "Incomplete" label appears as an **inline italic suffix** on the same line: `● Relax • 2 min 10 sec • 1 cycle • Today • Incomplete`
- **D-08:** Incomplete sessions sort **chronologically inline** with complete sessions — no separate grouping or divider.
- **D-09:** The **preset color dot** remains unchanged for incomplete entries — muted opacity already signals incompleteness without altering the dot.

### Background Phase Transitions (VISUAL-01)

- **D-10:** Implement as a **CSS-only `transition: background 220ms ease`** on `body` — no JS animation loop needed.
- **D-11:** The transition **applies in both light and dark modes** (not dark-only as stated in requirement). One CSS rule fires for all `--bg` changes regardless of mode.
- **D-12:** The transition also fires on **theme toggle** (dark/light switch) — consistent behavior, no suppression needed.
- **D-13:** **Unify with the existing `transition` rule on `body`** — extend the existing declaration rather than adding a second `transition` property. Prevents specificity conflicts.

### Landscape Layout Fix (LAYOUT-01)

- **D-14:** The media query condition `(max-height: 500px) and (orientation: landscape)` already exists in the code — the **researcher agent must diagnose the exact Chrome DevTools regression** before assuming a condition change is needed.
- **D-15:** Compact landscape layout priority: **ring + controls always fully visible**. History panel is accessible via scroll (below the fold) but not immediately visible. The existing `max-height: 180px; overflow-y: auto` on `.history` in the compact query is the right approach.

### App Version Display (INFO-01)

- **D-16:** Hardcode `APP_VERSION: "1.0"` as a constant in the CONFIG section (frozen with `Object.freeze()`). Display in the info panel footer. Format: `v1.0`.

### Claude's Discretion

- Exact opacity value for muted incomplete rows — `0.6` is the starting point; adjust for legibility in both themes if needed during implementation.
- Exact CSS `ease` curve for background transition — `ease` is the default; researcher/planner may choose `ease-in-out` if it feels smoother during testing.
- Exact placement of version string within the info panel footer — any footer position is acceptable.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Requirements & Roadmap
- `.planning/REQUIREMENTS.md` — Active requirements LAYOUT-01, VISUAL-01, TRACK-01, INFO-01 with acceptance criteria
- `.planning/ROADMAP.md` — Phase 6 success criteria (5 criteria including regression check)

### Project Constraints
- `.planning/PROJECT.md` — Stack constraints (vanilla JS only, single-file, localStorage only)

### Codebase (read before planning)
- `index.html` — The entire application; all changes land here
  - Line 887: `@media (max-height: 500px) and (orientation: landscape)` — compact landscape layout
  - Line 947: `@media (orientation: landscape) and (min-height: 501px)` — 2-column landscape layout
  - Line 1461: `applyThemeForCurrentPhase()` — sets `--bg` CSS variable directly
  - Line 1593: `saveHistory(entry)` — session persistence function
  - Line 1612: `renderHistory()` — history rendering, uses `innerHTML` template string
  - Line 1957: current `saveHistory()` call site on session completion
  - CSS `body` transition rule — locate and unify for bg transition fix

No external specs — requirements fully captured in decisions above.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `saveHistory(entry)` (line 1593): accepts any object — just add `incomplete: true` to the entry passed at Stop time
- `renderHistory()` (line 1612): uses `innerHTML` template string with `.map()` — extend the template to check `s.incomplete` flag for styling
- `elapsedMs` global state variable: already tracks total elapsed time; available at Stop time for `durationMs` on incomplete saves
- `cycleCount` global state variable: tracks full cycles completed; use as the threshold check (≥ 1)
- `isRunning` / `isCountingDown` globals: guards to detect Stop-during-countdown case

### Established Patterns
- Session schema: `{ date: ISO string, durationMs: number, cycles: number, preset: string }` — extend with `incomplete: boolean`
- Config constants use `Object.freeze()` — `APP_VERSION` follows this pattern
- CSS custom properties: `--bg`, `--accent`, `--accentSoft` drive all theming — background transition targets `background: var(--bg)` on `body`
- Silent try/catch in all localStorage operations — incomplete save follows same pattern
- `stop()` function in CONTROLS section is the hook for incomplete session save logic

### Integration Points
- `stop()` handler: add incomplete session save here (check `cycleCount >= 1 && !isCountingDown`)
- `body` CSS rule: add/unify `transition: background 220ms ease`
- `renderHistory()`: add `opacity` style + italic "Incomplete" suffix when `s.incomplete === true`
- CONFIG section: add `APP_VERSION` constant
- Info panel HTML: add version display element in footer

</code_context>

<specifics>
## Specific Ideas

- **Incomplete entry format:** `● Relax • 2 min 10 sec • 1 cycle • Today • Incomplete` — "Incomplete" in italic, full row at 0.6 opacity
- **Background transition:** `transition: background 220ms ease` on `body` — unify with existing transition declaration (find and extend, don't duplicate)
- **Version string:** `v1.0` — hardcoded in CONFIG, displayed in info panel footer

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 6 — Bug Fixes & Visual Polish*
*Context gathered: 2026-07-03*
