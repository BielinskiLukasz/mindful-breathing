# Project Retrospective

*A living document updated after each milestone. Lessons feed forward into future planning.*

---

## Milestone: v0.6 — Layout & History

**Shipped:** 2026-07-01
**Phases:** 2 (4–5) | **Plans:** 3 | **Sessions:** ~5

### What Was Built

- CSS Grid 2-column landscape layout — ring and history panel side-by-side on 600px+ landscape screens
- WCAG AA light theme contrast fixes (accent and textSoft darkened to 5.2:1 and 5.3:1 on cream)
- Per-phase atmospheric bgLight/accentLight colors across all PRESETS — sage/lavender/sky/cream tints during light-mode sessions
- JSON and CSV session history export via Blob downloads (no server, offline-safe)
- JSON import with FileReader, schema validation, exact-date dedup, merge, and feedback flash
- Native `<dialog>` clear-history confirmation with destructive language and safe dismiss

### What Worked

- Phase 4 executed in ~21 min total (15 min + 6 min across 2 plans) — tight execution driven by well-scoped plans
- Phase 5 executed in ~3 min — by far the fastest plan in the project; single-file, clean requirements
- Collocating bgLight/accentLight alongside bg/accent in PRESETS objects made the isDarkMode branch trivially simple to implement
- Reusing the existing `#storageWarning` flash element for import feedback avoided new DOM surface area
- Native `<dialog>` for clear-history removed all focus-trap and ESC-dismiss JS that would otherwise be needed
- `DURATION_RANGE` pattern from v0.5 continued to pay dividends — no scattered bounds throughout Phase 5 data handling

### What Was Inefficient

- Three bugs emerged shortly after Phase 5 shipped (startup crash, icon regression, history init) — required a dedicated debug session to fix; suggests the 3-min plan execution skipped some structural verification
- Manual UAT items (15) were captured in a checklist but never worked through before milestone close; carried as override_closeout debt
- Light-theme CSS had hardcoded dark-only rgba values in panels and ring elements — required a quick fix pass after Phase 4 that wasn't anticipated in the plan

### Patterns Established

- **Preset theme object pattern:** `{ bg, bgLight, accent, accentLight }` — light/dark pairs collocated per phase; `applyThemeForCurrentPhase()` selects at runtime via `isDarkMode`; portable to future per-phase theming needs
- **5-step theming pattern** (established v0.4, confirmed v0.6): CSS var → CSS replace → LIGHT_THEME → DARK_THEME → `applyThemeColors()` → add `bgLight`/`accentLight` fields for phase-level tints
- **Native dialog pattern:** `<dialog>.showModal()` for any destructive confirmation — free ESC, backdrop, focus trap; no JS wiring for dismiss
- **Blob download pattern:** `Blob + URL.createObjectURL + <a download> + URL.revokeObjectURL` — no server, works offline

### Key Lessons

1. **Post-shipping bugs cluster:** The 3 bugs that appeared after Phase 5 (startup crash, icon regression, history init) all traced to interactions with existing code that wasn't covered by the plan's scope. For single-file apps, a quick smoke-test pass immediately after implementation saves a separate debug session later.
2. **Manual UAT debt accumulates quickly:** 15 unchecked items at milestone close is meaningful debt. For v0.7, consider treating manual UAT as a blocking gate rather than a deferred checklist — especially for mobile/device-dependent features that can't be covered by code review.
3. **3-min plans can hide integration risk:** Phase 5 executed in 3 minutes but generated 3 post-ship bugs. Speed of implementation doesn't track with correctness of integration. The plan was correct; the missing piece was verifying initialization order with the rest of the app.
4. **CSS custom properties need a sync check:** After each JS LIGHT_THEME update, `:root` CSS variable declarations become stale documentation. The auto-fix in 04-01 caught this — it's worth making this a standard checklist item when updating theme constants.

### Cost Observations

- Model mix: primarily Sonnet 4.6 (all phases on this project)
- Sessions: ~5 (Phase 4 context, Phase 4 execution, Phase 5 execution, debug session, milestone close)
- Notable: Phase 5 at 3 min is the fastest plan execution in this project's history — well-scoped requirements translate directly to fast execution

---

## Milestone: v1.0 — Bug Fixes & Visual Polish

**Shipped:** 2026-07-04  
**Phases:** 1 (Phase 6) | **Plans:** 2 | **Sessions:** ~2

### What Was Built

- Smooth 220ms body background transition — `background` shorthand catches CSS custom property `--bg` changes that `background-color` misses
- Compact landscape height fix — `height: auto; min-height: 100vh` in `@media (max-height: 500px) and (orientation: landscape)` body block
- App version v1.0 in info panel footer — `APP_VERSION` constant JS-injected via `#appVersionEl`
- Incomplete session tracking — Pause saves `{incomplete: true}` when `cycleCount >= 2`; rendered at opacity 0.6 with italic "Incomplete" suffix; backward compatible
- Stop renamed to Pause — correct UX label for a pause action with Resume/Start following

### What Worked

- Tightly scoped plans (8 min and 6 min) — all 4 requirements fit into 2 plans with zero deviation from plan
- Human UAT was a blocking gate, not deferred debt — 5 browser tests run and confirmed before milestone close (contrast to v0.6 which carried 15 unchecked UAT items)
- HANDOFF.json proved valuable for clean session resume — structured data made it immediately clear what task was next
- All 5 success criteria verifiable by grep + human observation — no integration ambiguity

### What Was Inefficient

- A quick fix pass (move incomplete save to `reset()` → Pause rename) was needed after the initial plan was complete — suggests the plan assumptions about where the save should fire were slightly off
- The `transition: background-color` bug existed in the codebase since v0.4; it wasn't caught until a user-visible issue surfaced in light mode

### Patterns Established

- **Human UAT as milestone gate (not tech debt):** v1.0 proved that 5 targeted browser tests done before milestone close take ~10 min and eliminate the override_closeout debt that accumulated in v0.5/v0.6
- **CSS custom property transition trap:** When a CSS value is set via `--custom-property`, transitioning the sub-property (e.g., `background-color`) doesn't animate it; must use the shorthand (`background`)

### Key Lessons

1. **Quick fix passes happen when plan assumptions are slightly wrong** — the incomplete save moved from toggleBtn to `reset()` after implementation. This is low cost on a single-file app but worth noting: the plan should explicitly state WHERE a behavior lives (not just that it exists) to catch this earlier.
2. **Human UAT blocking = cleaner milestone history** — no deferred UAT items for v1.0 (vs 15 for v0.6). The 10-minute investment is worth it.
3. **Backward compatibility is a first-class design decision for storage schema changes** — `s.incomplete` being falsy for older entries (not `false`, not absent — just `undefined`) was an explicit decision (D-05). Worth surfacing early in any plan that modifies the persistence schema.

### Cost Observations

- Model mix: Sonnet 4.6 (all sessions)
- Sessions: ~2 (Phase 6 execution, milestone close)
- Notable: smallest milestone in the project — 1 phase, 2 plans, 1 file. Demonstrates that well-scoped v1.x milestones can ship in a single day

---

## Cross-Milestone Trends

### Process Evolution

| Milestone | Phases | Plans | Key Change |
|-----------|--------|-------|------------|
| v0.4 | 2 | 5 | First milestone — established single-file architecture and GSD workflow |
| v0.5 | 1 | 8 | Bug-heavy milestone; introduced DURATION_RANGE and micro-interaction patterns |
| v0.6 | 2 | 3 | Fastest execution; introduced CSS Grid pattern and native dialog pattern |
| v1.0 | 1 | 2 | Smallest milestone; first verified_closeout with blocking UAT gate |

### Cumulative Quality

| Milestone | UAT | Deferred UAT | Post-ship Bugs |
|-----------|-----|--------------|----------------|
| v0.4 | — | — | — |
| v0.5 | override_closeout | 1 (vibration) | 0 |
| v0.6 | override_closeout | 15 manual items | 3 (startup, icon, history) |
| v1.0 | verified_closeout | 0 | 0 |

### Top Lessons (Verified Across Milestones)

1. **Single-file apps need smoke-test passes after each plan, not just at milestone close** — v0.5 caught nothing post-ship; v0.6 had 3. v1.0 had 0 with blocking UAT.
2. **Well-scoped requirements produce fast execution** — v0.5 averaged ~8 plans; v0.6 averaged 1.5; v1.0 averaged 1 plan per 2 requirements. Fewer, more focused plans win.
3. **Reusing existing patterns beats inventing new ones** — `#storageWarning` flash reused for import feedback, `<dialog>` native behavior reused for clear confirmation. Zero new surface area.
4. **Human UAT as a blocking gate eliminates deferred debt** — v0.6 carried 15 unchecked items into milestone close; v1.0 ran 5 targeted tests before close and had zero debt.
