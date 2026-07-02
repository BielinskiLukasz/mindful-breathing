# Project Research Summary

**Project:** Mindful Breathing v0.6 — Layout & History
**Domain:** Responsive web app (PWA, breathing/wellness)
**Researched:** 2026-06-30
**Confidence:** HIGH

## Executive Summary

Mindful Breathing v0.6 adds four features to a mature single-file vanilla JS app: a 2-column landscape layout for tablets/desktops, WCAG AA contrast fixes for the light theme, import/export session history (JSON + CSV), and a confirmation dialog for clearing history. All four features use only native browser APIs (CSS Grid, File API, FileReader, Blob) — no npm dependencies required, and the single-file architecture remains intact.

The recommended approach is **CSS-first**: Phase 4 handles the layout and light theme (pure CSS changes, low risk), then Phase 5 extends the persistence layer for data portability (medium complexity, data validation required). This ordering avoids coupling layout changes to data logic and gives users an export path before the destructive clear-history operation is available.

Primary risks: media query cascade silently breaking portrait on real devices, light theme contrast failing on secondary elements that aren't visually tested, and import/export data corruption from missing schema validation or FileReader error handling.

## Key Findings

### Recommended Stack

No new dependencies needed. All v0.6 features are covered by browser-native APIs with 98–99% modern browser support. CSS Grid + `@media (orientation: landscape)` handles the layout redesign. File API (Blob, URL.createObjectURL, `<a download>`) plus FileReader handles export and import. CSV generation uses ~20 lines of RFC 4180–compliant JS (enclose fields with commas/quotes/newlines in double quotes, double inner quotes).

**Core technologies:**
- **CSS Grid** (`grid-template-columns: 1fr 1fr`): 2-column landscape layout — replaces flexbox 50/50 split for cleaner responsive semantics
- **CSS Custom Properties** (`--text-light`, `--accent-light`): light theme contrast — already in use; only values change
- **File API** (Blob, URL.createObjectURL, `<a download>`): JSON + CSV export — 98%+ browser support; iOS Safari requires click within touch handler
- **FileReader API**: JSON + CSV import — same browser support envelope as File API
- **Native `<dialog>` or confirm()**: clear history confirmation — built-in focus-trapping and ESC for free

### Expected Features

**Must have (table stakes):**
- 2-column landscape layout — history panel always visible alongside ring in landscape
- Light theme WCAG AA contrast (4.5:1 normal text, 3:1 large text) — accessibility baseline
- JSON export — users need a backup path before clear-history exists
- JSON import — round-trip data portability
- Clear history with confirmation dialog — destructive action needs guard

**Should have (differentiators):**
- CSV export — spreadsheet-friendly format for power users
- Export-before-delete offer in confirmation dialog — prevents irreversible loss
- Import merge strategy (skip duplicates by date) — safer than replace

**Defer (v0.7+):**
- CSV import — RFC 4180 edge cases (quoted newlines, mismatched columns) are complex without a parser library; JSON-only import is sufficient for v0.6
- Soft-delete / 7-day recovery window — over-engineered for current user base
- Adjustable column ratio — 50/50 is sufficient; tuning deferred

### Architecture Approach

All four features integrate cleanly into existing sections of the single `index.html`. No structural refactoring needed. Layout changes live entirely in the `<style>` block (new `@media (orientation: landscape) and (min-width: 600px)` rule). Light theme contrast is a CSS variable value change only. Import/export extends the existing PERSISTENCE section with ~150 new lines. The confirmation dialog reuses existing modal CSS patterns (settingsOverlay, doneOverlay).

**Integration points:**
1. **CSS media query block** — add/replace landscape rule; preserve portrait constraints
2. **PERSISTENCE section** — add `exportHistory()`, `importHistory()`, `clearHistory()` functions
3. **DOM section** — cache new button elements (export, import file input, clear, confirm/cancel)
4. **CONTROLS / event listeners** — wire up new button handlers at startup

### Critical Pitfalls

1. **Media query cascade breaks portrait** — Adding landscape rules can inadvertently override portrait or tablet breakpoints. Prevent: test real devices (iPhone, iPad, Android tablet, desktop), not only DevTools. Isolate landscape rules in a named block.
2. **Light theme contrast fails on secondary elements** — Developers audit primary text and miss secondary text, SVG ring stroke, disabled state colors, and button labels. Prevent: run all color pairs through WebAIM Contrast Checker, not just `--text` on `--bg`.
3. **Import corrupts history** — Missing JSON schema validation, no duplicate detection, FileReader error handling absent. Prevent: validate `{ date, durationMs, cycles }` shape on every record before writing to localStorage; use atomic write (read → merge → write once).
4. **Clear history reflexive dismiss** — Simple "OK/Cancel" prompt is dismissed by muscle memory before reading. Prevent: use destructive-language button ("Delete all sessions"), offer "Export first" link, consider hold-to-confirm or 2-step flow.
5. **localStorage quota exceeded on import** — Large imported files hit the 5–10 MB browser quota. Prevent: pre-check available quota before writing; surface inline warning (already exists in v0.5).

## Implications for Roadmap

Based on research, suggested phase structure (continuing from Phase 3):

### Phase 4: Layout & Light Theme Foundation
**Rationale:** CSS-only changes, zero risk to data logic, low effort (10–20 lines CSS + color audit). Prerequisite: good landscape layout makes history panel usable — which builds user confidence before data portability is added.
**Delivers:** 2-column landscape layout (50/50 CSS Grid), light theme WCAG AA contrast verified on all color pairs, tested on phone + tablet + desktop.
**Addresses:** 2-column landscape layout, light theme contrast improvements
**Avoids:** Layout/contrast changes entangled with data logic (pitfall: harder to debug regressions)

### Phase 5: Data Portability & History Control
**Rationale:** Depends on Phase 4 layout (history panel scrolling), medium complexity (validation required), safer when export exists before clear-history is available.
**Delivers:** JSON export + import, CSV export, clear history with confirmation dialog, schema validation, duplicate detection.
**Uses:** File API, FileReader, Blob, native dialog pattern
**Implements:** Extended PERSISTENCE section + new CONTROLS wiring

### Phase Ordering Rationale

- Phase 4 is pure CSS — no risk to JavaScript or stored data; fast to ship and easy to verify
- Phase 5 builds on stable layout and ensures users can back up data before the destructive delete feature lands
- CSV import deferred to v0.7 — edge case complexity without a parser library is not worth the risk

### Research Flags

Phases with standard patterns (skip research-phase):
- **Phase 4:** CSS Grid + media queries are fully documented; WCAG contrast ratios are specified by W3C
- **Phase 5:** File API and FileReader patterns are well-established; no novel technical challenges

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | All features use vanilla JS + native APIs; 98–99% browser support verified; no npm deps needed |
| Features | HIGH | Four features have clear domain scope; table stakes vs. differentiators explicitly identified |
| Architecture | HIGH | Single-file pattern understood; integration points explicit; no structural refactoring needed |
| Pitfalls | HIGH | Five critical pitfalls identified with specific prevention strategies; actionable per phase |

**Overall confidence:** HIGH

### Gaps to Address

- **Exact light theme color values:** STACK.md proposes `#3d2817` (text) and `#6d5d4b` (soft text) as starting points — verify with WebAIM Contrast Checker during Phase 4 planning and document final values in PLAN.md.
- **CSV import scope:** Confirm in Phase 5 planning whether v0.6 ships JSON-only import (recommended) or attempts simple CSV import; document decision in PLAN.md.
- **Import merge strategy:** Decide during Phase 5 planning: skip duplicates by date (recommended), replace all, or prompt user. Document in PLAN.md.
- **Landscape breakpoint threshold:** ARCHITECTURE.md uses `min-width: 600px` — verify across iPad (1024px), Android tablets (600–1024px), and phone landscape (667px) during Phase 4 planning.

## Sources

### Primary (HIGH confidence)
- MDN Web Docs — CSS Grid, `@media orientation`, File API, FileReader API, Blob, URL.createObjectURL
- W3C WCAG 2.1 specification — contrast ratio requirements (4.5:1 normal text, 3:1 large text)
- RFC 4180 — CSV escaping rules

### Secondary (MEDIUM confidence)
- WebAIM Contrast Checker — contrast ratio tool and guidance
- CSS-Tricks — accessible SVGs, CSS Grid patterns
- web.dev — media queries best practices

---
*Research completed: 2026-06-30*
*Ready for roadmap: yes*
