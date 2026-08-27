# Phase 9: Custom Preset Builder - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-07-31
**Phase:** 9-Custom Preset Builder
**Areas discussed:** Builder entry point & form, Edit/delete access pattern, Phase color & ring behavior, Data structure & storage key

---

## Builder Entry Point & Form

### Where does 'Create preset' live?

| Option | Description | Selected |
|--------|-------------|----------|
| "+" button in preset row | Appears after built-in presets; tapping opens builder at point of use | ✓ |
| Button inside settings panel | 'Create custom preset' button hidden behind settings panel | |
| Dedicated 'Manage Presets' button | New corner icon opens a full-height preset management panel | |

**User's choice:** "+" button in preset row

---

### What does the builder form look like?

| Option | Description | Selected |
|--------|-------------|----------|
| Native `<dialog>` modal | ESC/backdrop dismiss free; matches existing clear-history dialog; focus trap native | ✓ |
| Overlay panel (like info/settings) | Panel open/close pattern; Phase 8 focus management applies | |
| Inline expansion below preset row | Expands inline; no overlay; layout shift may be jarring | |

**User's choice:** Native `<dialog>` modal

---

### What inputs does the form contain?

| Option | Description | Selected |
|--------|-------------|----------|
| Name field + 4 phase rows with checkbox + duration | Checkbox per phase (active/inactive) + duration input; min 2 active | ✓ |
| Name field + duration inputs only (always 4 phases) | No phase toggles; can't create 3-phase patterns | |
| Name + phase checkboxes only (durations via sliders) | Durations edited separately via existing duration-input UI | |

**User's choice:** Name field + 4 phase rows with checkbox + duration

---

### Validation on invalid input

| Option | Description | Selected |
|--------|-------------|----------|
| Inline error flash | Invalid fields flash red; matches Phase 3 duration-input error flash | ✓ |
| Flash banner message | Error message at top of dialog using flashMsg() | |
| Disable Save button proactively | Save disabled until form valid; no error message | |

**User's choice:** Inline error flash

---

## Edit/Delete Access Pattern

### How does user reach Edit/Delete on pointer devices?

| Option | Description | Selected |
|--------|-------------|----------|
| Hover reveals pencil icon on preset button | Behind @media (hover:hover) guard; click opens pre-filled builder | ✓ |
| Long-press or context menu | Mobile: long-press; Desktop: right-click; less discoverable | |
| Manage list in settings panel | All custom presets listed with Edit/Delete; extra navigation step | |

**User's choice:** Hover reveals pencil/edit icon

---

### How does Delete work on touch/mobile?

| Option | Description | Selected |
|--------|-------------|----------|
| Edit dialog includes a Delete button | Opens via long-press; Delete button at bottom of same dialog | ✓ |
| Long-press → action sheet (Edit/Delete) | Standard mobile sheet with two options | |
| Always-visible × badge on custom buttons | Small × always visible; tap triggers confirmation | |

**User's choice:** Edit dialog includes Delete button at the bottom

---

### How is Edit triggered on mobile?

| Option | Description | Selected |
|--------|-------------|----------|
| Tap-and-hold (long-press) on preset button | Brief CSS press-scale feedback confirms; opens Edit dialog | ✓ |
| Always-visible edit icon (pencil badge) | Persistent icon regardless of hover state | |
| Select preset first, then edit button appears elsewhere | Two-step; no persistent icon | |

**User's choice:** Tap-and-hold (long-press)

---

### Deleting the currently active preset

| Option | Description | Selected |
|--------|-------------|----------|
| Fall back to Relax + reset immediately | Matches ROADMAP.md success criteria; simple and predictable | ✓ |
| Fall back to Relax but don't interrupt running session | Gentler but adds complexity | |
| Show extra warning before allowing delete of active preset | Extra confirmation step | |

**User's choice:** Fall back to Relax (first built-in preset) and reset

---

## Phase Color & Ring Behavior

### Color strategy for custom phases

| Option | Description | Selected |
|--------|-------------|----------|
| Inherit type colors from built-in phases | Inhale=green, Hold=purple, Exhale=blue, Hold2=cream; resolved from PRESETS | ✓ |
| Single neutral color for all custom phases | Muted grey/slate; simpler implementation | |
| User picks color from a small palette | 6–8 swatches in builder form; more complex | |

**User's choice:** Inherit type colors from built-in phases

---

### breathR assignment for ring animation

| Option | Description | Selected |
|--------|-------------|----------|
| Fixed by phase type | Inhale=[0,1], Hold=[1,1], Exhale=[1,0], Hold2=[0,0] | ✓ |
| User picks Expand/Hold/Contract per phase | Dropdown in form; complex for most users | |

**User's choice:** Fixed by phase type

---

### Audio cue frequencies

| Option | Description | Selected |
|--------|-------------|----------|
| Inherit cue frequency by type | Inhale=660Hz, Hold=520Hz, Exhale=330Hz, Hold2=440Hz | ✓ |
| Silent (no beep) for custom phases | Simpler but worse experience | |
| User picks frequency per phase | High/medium/low selector; complex for marginal benefit | |

**User's choice:** Inherit cue frequency by type

---

## Data Structure & Storage Key

### localStorage structure

| Option | Description | Selected |
|--------|-------------|----------|
| Array of {id, name, phases[]} under CUSTOM_PRESETS_KEY | type field drives runtime lookups; forward-compatible with Phase 10 | ✓ |
| Same full phase object as PRESETS (pre-baked theme/cue) | Larger storage; coupled to color scheme; hard to update later | |
| Extend existing STORAGE_KEY settings blob | Mixes settings with user content | |

**User's choice:** Array of {id, name, phases[]} under dedicated CUSTOM_PRESETS_KEY

---

### Limit on custom preset count

| Option | Description | Selected |
|--------|-------------|----------|
| No hard limit | localStorage easily handles this; preset row wraps naturally | ✓ |
| Cap at 5 | Arbitrary; disables "+" after 5 | |
| Configurable in settings | Extra UI for marginal scenario | |

**User's choice:** No hard limit

---

### Preset name in history

| Option | Description | Selected |
|--------|-------------|----------|
| Store preset name as string | Consistent with existing history; name captured at session time | ✓ |
| Store preset ID + resolve at render | Deleted presets show as 'Deleted preset'; extra lookup logic | |
| Store both ID and name | More data per entry; readable + traceable | |

**User's choice:** Store preset name as string

---

### Duration slider vs builder form

| Option | Description | Selected |
|--------|-------------|----------|
| Durations intrinsic to custom preset; sliders hidden when custom active | Builder is the single point of edit; savedDurations unchanged | ✓ |
| Custom presets also use duration sliders + savedDurations | More flexible but potentially confusing | |
| Duration sliders hidden; edit via builder | Same as 'intrinsic' — restated for clarity | |

**User's choice:** Durations intrinsic to the custom preset definition; duration sliders hidden when custom preset is active

---

## Claude's Discretion

None — all questions had clear user-selected answers.

## Deferred Ideas

- **Custom phase labels (arbitrary text):** Explicitly out of scope for v1.1 per REQUIREMENTS.md. Future milestone.
- **Color picker for custom presets:** Considered but chose to inherit type colors for simplicity. Worth revisiting in a personalization phase.
