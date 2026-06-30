# Milestones — Mindful Breathing

Historical record of shipped versions.

---

## v0.4 — Session History, Vibration & Theming

**Shipped:** 2026-06-05  
**Phases:** 1–2 | **Plans:** 5 total | **Requirements:** 10 v1

**Key Accomplishments:**

1. Paginated session history with preset badge and configurable storage cap (default 1,000 entries)
2. Vibration reliability verified correct per W3C spec — no code changes needed
3. Light/dark theme toggle with CSS custom properties and 220ms smooth transitions
4. Responsive typography with CSS clamp() providing fluid scaling from 320px mobile to 4K desktop
5. Landscape 50/50 flexbox layout with scrollable history and fixed controls, optimized for tablets and desktops

**Archive:** `.planning/milestones/v0.4-ROADMAP.md`  
**Requirements:** `.planning/milestones/v0.4-REQUIREMENTS.md`

**Git tag:** v0.4

---

*Historical record created 2026-06-05*

---

## v0.5 — Critical Bugs & UX Polish

**Shipped:** 2026-06-29
**Phases:** 3 | **Plans:** 8 total
**Closeout type:** override_closeout (no formal audit; all gaps addressed inline)

**Key Accomplishments:**

1. Countdown timer orphan prevention — `stop()` clears countdown timers when interrupted mid-countdown
2. `DURATION_RANGE` constant (1–300s) as single source of truth for all duration input clamping and validation
3. localStorage `QuotaExceededError` surfaced with inline warning banner inside history panel (non-blocking)
4. Button `:active` state enhanced with inset shadow + semi-transparent overlay — CSS-only, GPU-accelerated
5. Phase transition ring flash animation (200ms brightness pulse) + first-visit gesture hint with localStorage flag
6. Duration input error flash (400ms red highlight) for invalid/out-of-range values
7. Countdown audio beeps (350 Hz, distinct from 520 Hz phase beep), Skip button, and ESC key handler added
8. Vibration UI hidden as pragmatic resolution to Samsung/Android OS restriction — code kept intact for future re-enable

**Known Deferred Items:** 1 (TEST-01 — vibration deferred as known_limitation; see v0.5-REQUIREMENTS.md)

**Archive:** `.planning/milestones/v0.5-ROADMAP.md`
**Requirements:** `.planning/milestones/v0.5-REQUIREMENTS.md`

**Git tag:** v0.5.0

---

*Historical record updated 2026-06-29 after v0.5 milestone*
