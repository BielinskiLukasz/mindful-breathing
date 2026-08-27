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

## Milestone: v1.1 — Landscape Polish, Custom Presets & Streaks

**Shipped:** 2026-08-27  
**Phases:** 5 (7–10.1) | **Plans:** 14 | **Sessions:** ~20 | **Commits:** 174 | **Duration:** 54 days

### What Was Built

- 2-column landscape grid: fullscreen button below ring; session info in left column; controls at 50% column height; height-based compact breakpoint (LAYOUT-02–06)
- Full keyboard accessibility: :focus-visible indicators, logical tab order, ARIA labels, aria-live screen reader announcements (A11Y-01–03)
- CSV session history import alongside existing JSON import; focus trap and keyboard-shortcut-guard pattern shared across all 4 panel types (HIST-12)
- Custom preset builder: create, name, edit, delete custom presets; persist in localStorage; appear in selector alongside built-ins (PRESET-01–05)
- Daily streak tracking: flame badge in corner controls; streak panel with current/longest/today status/total tiles; two-tier longest-streak persistence across history rotation (STREAK-01–06)

### What Worked

- Phase 9 gap-closure workflow (09-04, 09-05, 09-06) systematically resolved 23 cross-phase integration gaps — having an enumerated gap list made execution straightforward rather than exploratory
- `toLocaleDateString('en-CA')` for streak dates solved UTC boundary mismatch cleanly; the locale returns YYYY-MM-DD in the user's local timezone without any date library
- Two-tier longest streak (compute from capped history + persist high-water mark separately) correctly handles the history rotation problem — elegant split of concerns
- streakOverlay reusing `.infoOverlay/.infoPanel` CSS required zero new CSS and got focus-trap + ARIA for free
- Phase 10.1 inserted urgently as a gap-closure phase after Phase 10 UAT — the GSD workflow's phase-insert pattern handled the unplanned work cleanly without disrupting the plan history
- Milestone audit (`/gsd-audit-milestone`) before close caught open debug sessions and unarchived quick tasks — clean-up was targeted rather than a sweep

### What Was Inefficient

- Two debug sessions (g09-5, g09-21) had their fixes already in code but session `.md` files still showed non-resolved statuses — required audit to surface the mismatch; sessions should be closed immediately when the fix is committed
- Two v0.6 quick tasks (light-mode-panels-ring, light-theme-css-fix) remained unarchived as "incomplete" in the audit; they were `status: done` in SUMMARY.md but the scanner sees unarchived = incomplete regardless of status field; required `audit-open acknowledge` to clear
- Phases 7+8 VERIFICATION.md used pre-v1.1 schema (`status: complete`/`verified` vs `passed`) — init.manager misreported them as unverified; caused an override_closeout that wasn't warranted by actual gaps; schema migration on pre-existing VERIFICATIONs would have prevented this
- 54 days for the milestone suggests scope growth mid-stream: Phase 9 (custom presets) grew to 6 plans with gap-closure passes; Phase 10.1 was entirely unplanned. Better scoping of custom presets upfront would have compressed the timeline.

### Patterns Established

- **Debug session lifecycle:** Close (`status: resolved`) the session the moment the fix is committed — not at UAT, not at milestone close. If the bug is verified in code, the session is resolved.
- **Quick task archive status:** `status: done` in SUMMARY.md does NOT clear the artifact scanner — it reads "unarchived" for all dirs in `.planning/quick/`. Either pass `--archive-quick` to `milestone.complete` or use `audit-open acknowledge` for done-but-unarchived tasks.
- **Streak date locale pattern:** `new Date().toLocaleDateString('en-CA')` returns `YYYY-MM-DD` in user's local timezone — use this for any daily-boundary comparison in localStorage.
- **Two-tier stat persistence:** When a stat uses capped/rolling storage, persist the high-water mark separately (e.g., `mb_longest_streak`) so history rotation doesn't erase the all-time record.
- **Gap-closure phase insert:** When Phase N UAT reveals integration gaps, insert Phase N.1 as a gap-closure phase. The GSD workflow tracks it cleanly as a sub-phase rather than polluting Phase N with scope growth.

### Key Lessons

1. **Close debug sessions when the fix lands, not at milestone close** — two sessions spent the entire milestone in limbo with `status: awaiting_human_verify` after their bugs were fixed in code. If the fix is in and verified by grep/read, update `status: resolved` in the frontmatter immediately.
2. **`status: done` in quick task SUMMARY.md ≠ cleared from artifact scanner** — the scanner flags all unarchived `.planning/quick/` dirs regardless of content. Done quick tasks accumulate silently; they surface only at the next audit. Either archive them at completion (via milestone.complete `--archive-quick`) or acknowledge at close.
3. **VERIFICATION.md schema evolves — old phases need migration** — Phases 7+8 used an older schema (`status: complete`) that init.manager didn't recognize, causing false unverified reports. When the schema changes, update existing VERIFICATION.md files or add a fallback in the checker; stale schemas create misleading closeout friction.
4. **Phase 9 scope grew 3x in gap closure** — the original 3 plans grew to 6 when UAT surfaced 23 integration gaps. For complex feature phases (multi-state UI like the preset builder), plan an explicit integration/gap-closure plan as Plan N upfront rather than treating gap closure as a surprise.

### Cost Observations

- Model mix: Sonnet 4.6 (all sessions)
- Sessions: ~20 across 54 days (including Phase 9 extended gap closure)
- Notable: Phase 9 is the most complex phase in the project's history — 6 plans, 23 gaps closed, multi-state dialog UI. Streak tracking (Phase 10 + 10.1) delivered STREAK-01–06 in 3 plans total, demonstrating that well-bounded features with clear edge cases execute efficiently.

---

## Cross-Milestone Trends

### Process Evolution

| Milestone | Phases | Plans | Key Change |
|-----------|--------|-------|------------|
| v0.4 | 2 | 5 | First milestone — established single-file architecture and GSD workflow |
| v0.5 | 1 | 8 | Bug-heavy milestone; introduced DURATION_RANGE and micro-interaction patterns |
| v0.6 | 2 | 3 | Fastest execution; introduced CSS Grid pattern and native dialog pattern |
| v1.0 | 1 | 2 | Smallest milestone; first verified_closeout with blocking UAT gate |
| v1.1 | 5 | 14 | Largest milestone; complex custom preset builder; streak tracking; gap-closure phase pattern |

### Cumulative Quality

| Milestone | UAT | Deferred UAT | Post-ship Bugs |
|-----------|-----|--------------|----------------|
| v0.4 | — | — | — |
| v0.5 | override_closeout | 1 (vibration) | 0 |
| v0.6 | override_closeout | 15 manual items | 3 (startup, icon, history) |
| v1.0 | verified_closeout | 0 | 0 |
| v1.1 | override_closeout | 2 acknowledged quick tasks (done, unarchived) | 0 |

### Top Lessons (Verified Across Milestones)

1. **Single-file apps need smoke-test passes after each plan, not just at milestone close** — v0.5 caught nothing post-ship; v0.6 had 3. v1.0 had 0 with blocking UAT.
2. **Well-scoped requirements produce fast execution** — v0.5 averaged ~8 plans; v0.6 averaged 1.5; v1.0 averaged 1 plan per 2 requirements. Fewer, more focused plans win.
3. **Reusing existing patterns beats inventing new ones** — `#storageWarning` flash reused for import feedback, `<dialog>` native behavior reused for clear confirmation. Zero new surface area.
4. **Human UAT as a blocking gate eliminates deferred debt** — v0.6 carried 15 unchecked items into milestone close; v1.0 ran 5 targeted tests before close and had zero debt.
5. **Close debug sessions when the fix commits, not at milestone close** — v1.1 had 2 sessions linger for the entire milestone after their bugs were fixed; the milestone audit had to surface them. Session resolution is a commit-time action.
6. **`status: done` in a quick task SUMMARY.md does not clear the artifact scanner** — done ≠ archived. Unarchived quick tasks accumulate and surface at close; archive them at completion or acknowledge at close.
