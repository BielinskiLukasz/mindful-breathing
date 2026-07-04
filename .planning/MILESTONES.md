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

---

## v0.6 — Layout & History

**Shipped:** 2026-07-01
**Phases:** 4–5 | **Plans:** 3 total | **Requirements:** 6 v1
**Closeout type:** override_closeout

**Key Accomplishments:**

1. CSS Grid 2-column landscape layout (`1fr 1fr`) replacing flex — history panel visible alongside the breathing ring in landscape on 600px+ screens
2. WCAG AA light theme contrast: accent #a0662e (5.2:1) and textSoft #6b6058 (5.3:1) on cream #f5f1ed — warm brown/gold family maintained
3. Per-phase atmospheric bgLight/accentLight colors across all 11 PRESETS entries — sage/lavender/sky/cream for Inhale/Hold/Exhale/Hold2 in light mode
4. JSON and CSV session history export via Blob + URL.createObjectURL (no server required)
5. JSON import with FileReader, schema validation, exact-date dedup, merge, and `#storageWarning` flash feedback
6. Native `<dialog>.showModal()` clear-history confirmation with "Delete all sessions" — ESC and backdrop dismiss without data loss

**Known Deferred Items:** 15 manual UAT items (sound, haptics, Wake Lock, mobile Safari, landscape on device, browser-verified import/export, PWA install — see STATE.md Deferred Items)

**Archive:** `.planning/milestones/v0.6-ROADMAP.md`
**Requirements:** `.planning/milestones/v0.6-REQUIREMENTS.md`

**Git tag:** v0.6

---

*Historical record updated 2026-07-01 after v0.6 milestone*

---

## v1.0 — Bug Fixes & Visual Polish

**Shipped:** 2026-07-04  
**Phases:** 6 | **Plans:** 2 | **Tasks:** 5  
**Closeout type:** verified_closeout (all 4 requirements shipped + 5/5 UAT browser tests passed)

**Key Accomplishments:**

1. Smooth 220ms background phase transition — `background` shorthand in body CSS transition animates `--bg` CSS custom property reliably; `background-color` sub-property fails to track it
2. Compact landscape layout fixed for tall phones — `height: auto; min-height: 100vh` overrides `height: 100vh`, letting content scroll past the viewport while preserving full-height layout when short (UAT: iPhone 14 Pro Max 932×430px in Chrome DevTools)
3. App version v1.0 in info panel footer — `APP_VERSION` constant in CONFIG, JS-injected into `#appVersionEl` at init; HTML element starts empty (single source of truth)
4. Incomplete session tracking — Pause/Stop saves `{incomplete: true}` when `cycleCount >= 2` (at least 1 full cycle); rendered at opacity 0.6 with italic "Incomplete" suffix; backward compatible via falsy `undefined` for older entries
5. Stop button renamed to Pause — reflects actual behavior; Resume/Start follows

**Archive:** `.planning/milestones/v1.0-ROADMAP.md`  
**Requirements:** `.planning/milestones/v1.0-REQUIREMENTS.md`

---

*Historical record updated 2026-07-04 after v1.0 milestone*
