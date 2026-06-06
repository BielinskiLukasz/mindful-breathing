---
phase: 3
phase_name: Critical Bugs & UX Polish
phase_slug: critical-bugs-ux-polish
created_at: 2026-06-05
discussion_date: 2026-06-05
---

# Phase 3 Context: Critical Bugs & UX Polish

**Goal:** Fix Phase 2 blockers and solidify reliability with targeted bug fixes, vibration validation on Android, and delightful micro-interactions that strengthen the breathing experience.

---

## Domain

Stability and delight — closing gaps left by Phase 2's theming work, validating vibration reliability across Android devices, and adding subtle visual feedback that makes the app feel responsive and intentional.

---

## Decisions Captured

### Bug Fixes (3 prioritized)

**Selected scope:** Countdown timer cleanup, Duration input validation, Wake Lock & localStorage robustness.

**Rationale:** These three have high impact relative to effort (2–10 lines each) and address session reliability:
- **Countdown timer cleanup** — Stop accumulating timers if user interrupts 3s countdown. Prevents memory creep from repeated Start clicks during countdown.
- **Duration input validation** — Clamp slider values to valid range (1–300s). Prevents 0-second phases that break session flow and create invalid history entries.
- **Wake Lock & localStorage robustness** — Detect if wake lock is lost mid-session (add release event listener); catch `QuotaExceededError` and show user warning. Improves session reliability and graceful degradation.

**Deferred (lower priority):**
- Audio context error guards (defensive checks, moderate effort)
- Silent error logging (nice-to-have, debugging aid)
- Session truncation UX (cosmetic label)

### Vibration Validation on Android

**Decision:** Real device testing. No code changes; hands-on QA.

**What this means:**
- Test on actual Android devices (phones, tablets) running Chrome/Edge
- Confirm vibration fires reliably at each phase transition (Inhale, Hold, Exhale, Hold2)
- Phase 1 research confirmed Vibration API is reliable post-user-tap; this validates the implementation in practice
- Document any device-specific quirks or limitations (e.g., Some Android devices don't vibrate in silent mode)

**Not included (deferred):**
- iOS fallback (Vibration API not supported; not a blocker for v0.5)
- Audio fallback if vibration fails (enhancement; can be Phase 4)

### Cross-Browser Testing Strategy

**Decision:** Chrome/Edge (Chromium) as blockers.

**What this means:**
- Phase 3 validation focuses on Chrome/Edge desktop and mobile
- Test audio autoplay policy: does first beep play without additional user interaction?
- Test wake lock: does screen stay on during session?
- Test light theme visual clarity on both desktop and mobile viewports
- Firefox and Safari are "nice-to-have" but not blockers for Phase 3 release

**Rationale:** Chromium (Chrome/Edge) dominates market share (70%+); validates the core audience. Other browsers can be tested post-release.

### Light Theme Contrast & Accessibility

**Decision:** Manual review (visual inspection).

**What this means:**
- Verify light theme text/background pairs are readable on desktop and mobile
- Eye test: does cream/gold palette feel pleasant and clear?
- Check both light and dark themes side-by-side for parity
- No WCAG AA ratio testing; accept visual comfort as standard

**Acceptance:** Phase 2's palette shipped without complaints; if manual review shows no glaring contrast issues, palette is approved.

### Micro-Interactions & Visual Polish

**Selected interactions (4 areas):**

1. **Button press feedback** — Click Start/Stop/Reset → visual press-down or subtle highlight. Strengthen tactile feel of interaction.

2. **Phase transition cues** — When advancing from one phase to next → ring flashes or breathing label emphasis. Reinforce the moment and make progress visible.

3. **Gesture hints** — On first app load (no saved preference or first visit) → fade-in tooltip or hint text: "Press Start or Space to begin." Onboarding cue for new users.

4. **Error/invalid input feedback** — Duration slider out of range → red flash or shake animation. Clearer feedback when input validation catches invalid value.

**Implementation approach:**
- Button press: CSS `:active` state or JS-triggered animation
- Phase cues: CSS `@keyframes` (e.g., ring flash at phase transition)
- Gesture hints: DOM element with fade-in animation; hide after first interaction
- Error feedback: brief red background flash or small shake on input validation

**Coherence:** All micro-interactions use existing animation mechanisms (@keyframes, transitions) to avoid janky performance. No new JS libraries.

---

## Code Context

### Relevant Files & Sections

**Primary:** `index.html` (~1,350 lines)
- **DURATION INPUTS** (lines ~1277–1310) — Duration slider validation lives here
- **CONTROLS** (lines ~1820–1890) — Event handlers for Start/Stop/Reset; add press feedback here
- **PHASE ADVANCE** (lines ~1540–1573) — Phase transition point; add ring flash cue here
- **RENDER** (lines ~1479–1537) — SVG updates; phase transition animation hook
- **MAIN LOOP** (lines ~1576–1613) — requestAnimationFrame loop; performance-critical

### Reusable Patterns

From prior phases:
- **CSS animations** — @keyframes already used for idle pulse, countdown pop, phase fade-in (see lines ~670–720)
- **Event listeners** — Event delegation pattern in place for button clicks (lines ~1843–1865)
- **localStorage error handling** — Try/catch pattern already used for settings save (lines ~1316–1352)
- **Browser API guards** — Feature checks for Vibration, Wake Lock, Fullscreen in place (lines ~1399–1450)

---

## Canonical References

(All files relative to repo root)

- `.planning/codebase/CONCERNS.md` — Technical concerns analysis; details 7 bug candidates and other fragile areas
- `.planning/codebase/STACK.md` — Stack and API reference; CSS animations, JS event patterns
- `.planning/ROADMAP.md` — Phase 3 scope and dependencies
- `.planning/PROJECT.md` — Core Value and Design Principles
- `CLAUDE.md` — Architecture constraints (single-file vanilla JS, no frameworks)

---

## Assumptions & Risks

### Assumptions

1. **Vibration API reliability** — Phase 1 research confirmed Android vibration works post-user-tap. This phase validates on real devices.
2. **localStorage is available** — Try/catch pattern already handles quota errors; we improve the feedback, not the fallback.
3. **Animation performance** — CSS @keyframes don't block main thread. New micro-interactions don't cause frame drops (measure on real devices).
4. **Manual contrast review is sufficient** — Team agreement that WCAG AA testing not required for v0.5.

### Risks

- **Real device testing may reveal platform-specific quirks** — Some Android devices mute vibration in silent mode (expected, document it)
- **New CSS animations may interact with existing animations** — Test phase transition cue doesn't conflict with phase fade-in
- **First-load hint may clutter UI** — Keep it subtle; hide after interaction to avoid annoyance

---

## Scope Guardrails

**In scope (Phase 3):**
- 3 targeted bug fixes (low-to-medium effort each)
- Real device validation of vibration on Android
- Manual review of light/dark contrast
- 4 micro-interactions (CSS-based, no new libraries)
- Chrome/Edge cross-browser validation

**Out of scope (defer to later phases):**
- WCAG AA compliance audit (Phase 4+)
- iOS vibration fallback (Phase 4+)
- Firefox/Safari full validation (Phase 4+)
- Audio fallback for vibration (Phase 4+)
- Advanced analytics or data export (Phase 5+)

---

## Success Criteria

✓ All 3 bug fixes implemented and verified (timer cleanup, input validation, wake lock)
✓ Vibration tested on at least 2 Android devices; no regressions noted
✓ Manual review of light/dark contrast completed; no glaring issues
✓ 4 micro-interactions implemented: button press, phase transition cue, gesture hint, error feedback
✓ Chrome/Edge validation complete (desktop + mobile)
✓ No performance regressions (animations maintain 60 FPS on mobile)
✓ PHASE ADVANCEMENT: Ready for Phase 4 (Layout Redesign)

---

## Next Steps

1. `/gsd-plan-phase 3` — Create detailed plans for each work stream
2. Plans will cover: Bug fixes (1 plan), Micro-interactions (1–2 plans), Testing & Validation (1 plan)
3. Post-plan execution via `/gsd-execute-phase 3`

---

*Context gathered: 2026-06-05 with developer input*
