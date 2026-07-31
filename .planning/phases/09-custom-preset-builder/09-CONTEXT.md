# Phase 9: Custom Preset Builder - Context

**Gathered:** 2026-07-31
**Status:** Ready for planning

<domain>
## Phase Boundary

Add a custom preset builder to the app: users can create, name, edit, and delete their own breathing presets with arbitrary phase/duration combinations. Custom presets persist in localStorage under a dedicated key and appear alongside the three built-in presets (Relax, Box, 4-7-8) in the preset selector row.

**In scope:** PRESET-01, PRESET-02, PRESET-03, PRESET-04, PRESET-05
**Out of scope:** Custom phase labels (arbitrary text beyond Inhale/Hold/Exhale/Hold2 — deferred), color picker for users, duration sliders for custom presets (editing goes through builder dialog), streak tracking (Phase 10)

</domain>

<decisions>
## Implementation Decisions

### Builder UI Entry Point & Form

- **D-01:** A "+" (or "New") button appears in the preset selector row, after the three built-in preset buttons (Relax, Box, 4-7-8). Tapping it opens the builder. Discoverable at the point of use — user is already looking at presets. The "+" button is not gated on hover; it is always visible.
- **D-02:** The builder form opens as a native `<dialog>` modal. ESC and backdrop dismiss come free from the browser; focus trap is handled natively. Matches the existing clear-history `<dialog>` pattern established in Phase 5/v0.6.
- **D-03:** Form fields: (1) a text input for the preset name; (2) four phase rows — Inhale, Hold, Exhale, Hold2 — each with a checkbox (active/inactive) and a duration input. At least 2 phases must be checked to enable Save.
- **D-04:** Validation uses an inline error flash on invalid fields (empty name or fewer than 2 active phases) — matches the duration-input error flash pattern from Phase 3/v0.5. The Save button is NOT disabled proactively; validation fires on submit.

### Edit/Delete Access Pattern

- **D-05:** On pointer devices (`@media (hover: hover) and (pointer: fine)`): hovering a custom preset button reveals a small pencil/edit icon on the button. Clicking the icon opens the builder dialog pre-filled with the preset's current name, phase toggles, and durations.
- **D-06:** On touch/mobile: long-press (tap-and-hold) on a custom preset button opens the Edit dialog. A brief CSS press-scale feedback confirms the press.
- **D-07:** The Edit dialog includes a Delete button at the bottom of the form (below Save/Cancel). Delete requires confirmation via the same inline confirm step or a second click — researcher to determine cleanest pattern (inline confirmation within the dialog, not a second native dialog).
- **D-08:** If the user deletes the currently active preset, the app immediately falls back to Relax (the first built-in preset) and resets the session. No extra warning step before delete; the confirmation step within the Edit dialog is sufficient.

### Phase Color & Ring Behavior

- **D-09:** Theme colors (bg, bgLight, accent, accentLight) are inherited by phase type from the corresponding built-in phase definitions in PRESETS: Inhale borrows green (from PRESETS.relax[0].theme), Hold borrows purple (from PRESETS.relax[1].theme), Exhale borrows blue (from PRESETS.relax[2].theme), Hold2 borrows cream (from PRESETS.relax[3].theme). No color input from the user. Colors are resolved at runtime from PRESETS — not stored in localStorage.
- **D-10:** `breathR` is fixed by phase type and assigned at runtime: Inhale=[0,1] (expand), Hold=[1,1] (hold full), Exhale=[1,0] (contract), Hold2=[0,0] (hold empty). Same values as built-ins. Not stored in localStorage; resolved from a lookup table by type.
- **D-11:** Audio cue frequencies are inherited by type: Inhale=660Hz, Hold=520Hz, Exhale=330Hz, Hold2=440Hz. Resolved at runtime from PRESETS or a constants table. Not stored.

### Data Structure & Storage

- **D-12:** A dedicated `CUSTOM_PRESETS_KEY` constant is added to CONFIG (alongside `STORAGE_KEY` and `HISTORY_KEY`). Stored value is a JSON array of custom preset objects:
  ```
  [{ id: "custom-<timestamp>", name: "My Preset", phases: [
    { type: "Inhale", durationSec: 5, active: true },
    { type: "Hold",   durationSec: 3, active: false },
    { type: "Exhale", durationSec: 7, active: true },
    { type: "Hold2",  durationSec: 0, active: false }
  ]}]
  ```
  `type` drives theme/breathR/cue lookups at runtime. `id` is the stable preset key used in `activePresetKey`. Inactive phases (`active: false`) are excluded from `activePhases` when the preset is selected.
- **D-13:** No hard limit on the number of custom presets. The preset row wraps naturally.
- **D-14:** Session history records the preset name as a plain string (same as existing built-in presets). If a user later renames or deletes the preset, history retains the name from when the session was completed. No ID stored in history.
- **D-15:** Durations are intrinsic to the custom preset definition — the builder dialog is the only way to set or change them. The duration-input slider section below the ring is hidden (or disabled) when a custom preset is active. The `savedDurations` mechanism continues to serve built-in presets only and is not extended to custom presets.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Requirements
- `.planning/REQUIREMENTS.md` — Full v1.1 requirements; Phase 9 items: PRESET-01, PRESET-02, PRESET-03, PRESET-04, PRESET-05 with acceptance criteria
- `.planning/ROADMAP.md` — Phase 9 goal and success criteria (5 truths)

### Project Constraints
- `CLAUDE.md` — Vanilla JS only, single file, no frameworks, no npm, graceful API degradation, no `throw` anywhere, `Object.freeze()` for config constants

### Codebase Maps
- `.planning/codebase/CONVENTIONS.md` — Naming patterns, CSS class conventions, error handling patterns, section divider format
- `.planning/codebase/STRUCTURE.md` — index.html section map (CONFIG: 1044–1128, STATE: 1131–1164, PERSISTENCE: 1313–1353, CONTROLS/preset handler: ~2395–2418, PANELS: 1829–1883)

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `PRESETS` const (index.html ~line 1378): frozen object with per-phase theme, breathR, cue — custom phase type→color/breathR/cue lookups resolve from here at runtime (D-09, D-10, D-11)
- `.presetBtn` CSS class and preset row HTML (index.html ~line 1250–1252): built-in preset buttons — custom preset buttons adopt the same class and DOM pattern
- `presetsEl` click handler (index.html ~line 2395–2418): existing preset selection logic — extend to handle custom preset keys (currently guards `key in PRESETS`; needs to also check custom presets)
- Native `<dialog>` pattern from clear-history confirmation (Phase 5/v0.6): ESC, backdrop, focus trap all free — builder form reuses this pattern (D-02)
- Duration input error flash (Phase 3/v0.5): CSS flash animation on invalid input — reused for builder form validation (D-04)
- `flashMsg()` function or equivalent flash banner: reuse for save-success feedback
- `STORAGE_KEY`, `HISTORY_KEY` constants in CONFIG: `CUSTOM_PRESETS_KEY` follows the same pattern
- `saveSettings()` / `loadSettings()` / `saveHistory()` in PERSISTENCE section: new `saveCustomPresets()` / `loadCustomPresets()` functions follow the same try/catch wrapper pattern
- Panel focus management (Phase 8): `<dialog>` focus is browser-native, but ensure builder dialog open/close follows the same focus-return pattern (open → trap in dialog; close → return to "+" button)

### Established Patterns
- `Object.freeze()` for CONFIG constants — PRESETS is frozen; custom presets are mutable user data loaded from localStorage (NOT frozen)
- Silent `try/catch(_) {}` for all localStorage calls — customPresets load/save follows this
- Section dividers `/* ====== SECTION NAME ====== */` — new CUSTOM PRESETS section (or subsection of CONFIG/PERSISTENCE)
- `:focus-visible` focus ring (Phase 8/A11Y-03) applies automatically to all buttons including new preset buttons and dialog controls
- `@media (hover: hover) and (pointer: fine)` for hover-gated styles — pencil edit icon hover appearance uses this guard (D-05)
- `activePresetKey` global state: custom preset IDs become valid activePresetKey values (e.g. `"custom-1722456789123"`)
- `savedDurations` object: not extended to custom presets (D-15)

### Integration Points
- Preset row HTML: insert "+" button after the last `.presetBtn`; add custom preset buttons dynamically via `renderCustomPresets()` function
- `activePhases` initialization: when `activePresetKey` is a custom preset ID, build `activePhases` from custom preset phases (applying type→breathR/theme/cue lookup), NOT from PRESETS
- PERSISTENCE section: add `loadCustomPresets()` called at init (alongside `loadSettings()`); add `saveCustomPresets()` called after create/edit/delete
- History save (`saveHistory()`): no change needed — preset name is already stored as a string field
- Duration inputs section (lines 1256–1310): hide or disable `#durationInputs` when active preset is custom (D-15)
- Keyboard shortcut guard (Phase 8/D-10): builder `<dialog>` focus trap is native; confirm it integrates cleanly with the `openPanelElement` state variable from Phase 8

</code_context>

<specifics>
## Specific Ideas

- Custom preset button `data-preset` attribute: use the preset's `id` field (e.g., `data-preset="custom-1722456789123"`) so the existing click handler pattern `btn.dataset.preset` continues to work
- "+" button label: either `+` or `New` — researcher to check which fits the preset row style better
- Edit icon visibility: CSS `opacity: 0; transition: opacity 80ms` on the icon; `:hover` on the `.presetBtn` sets `opacity: 1` — pure CSS, no JS needed for the hover reveal
- Long-press implementation: `touchstart` + `setTimeout(300ms)` pattern; `touchmove` or `touchend` before 300ms cancels it; no library needed
- Inline delete confirmation in Edit dialog: after first click on Delete, button changes label to "Confirm Delete" (or turns red) for a second click. No second native dialog — keeps the UX lightweight
- Builder dialog structure: `<dialog id="presetBuilderDialog">` with `<form method="dialog">` for native submit handling; `returnValue` signals save vs. cancel

</specifics>

<deferred>
## Deferred Ideas

- **Custom phase labels (arbitrary text):** Allowing users to name phases beyond Inhale/Hold/Exhale/Hold2 is explicitly out of scope for v1.1 per REQUIREMENTS.md. If requested, add as PRESET-custom-labels in a future milestone.
- **Color picker for custom presets:** User suggested this as a possibility during discussion but chose to inherit type colors for simplicity. Worth revisiting in a polish/personalization phase.

</deferred>

---

*Phase: 9-Custom Preset Builder*
*Context gathered: 2026-07-31*
