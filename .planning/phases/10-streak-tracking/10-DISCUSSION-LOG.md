# Phase 10 Discussion Log: Streak Tracking

**Date:** 2026-08-25
**Format:** Gray-area selection → targeted Q&A per area

---

## Gray Areas Identified

1. History cap vs. streak accuracy
2. Zero-state badge design
3. Streak button position
4. Panel stat layout

---

## Q&A Transcript

### Area 1: History cap vs. streak accuracy

**Q:** History is capped at 14 entries. STREAK-02 says longest streak is "computed from full session history" — a 30-day streak user would only see 14. Do we persist computed streak values separately in localStorage, or live with the 14-entry window?

**Options presented:**
- Persist computed values (`longestStreak` + `currentStreak` in settings) — recompute each session end, update if higher
- Expand history cap (e.g. 90 or 365 entries) — simpler logic, but unbounded growth

**Decision:** Persist computed values. Add `longestStreak` to settings object under `STORAGE_KEY`. Recompute after each `saveHistory()` call; update if new value is higher.

---

### Area 2: Zero-state badge design

**Q:** STREAK-06 says badge shows "0" OR is visually muted when streak is 0. Which: show "🔥 0" (always a number), or show a grey/dimmed flame with no count?

**Options presented:**
- Flame + 0: always show number; when 0, display dimmed (~0.45 opacity)
- Dimmed flame, no number: absence of number signals no streak; show count only when ≥1

**Decision:** Flame + 0. Badge always shows `🔥 N`. When N=0, render at ~0.45 opacity. Consistent format — user always sees a count.

---

### Area 3: Streak button position

**Q:** The cornerControls column currently has Settings (top) and Info (bottom). Where does the streak button go?

**Options presented:**
- Between Settings and Info (Settings → Streak → Info) — Recommended
- Above Settings (Streak → Settings → Info)
- Below Info (Settings → Info → Streak)

**Decision:** Between Settings and Info. Settings stays most prominent (used most during setup), Info stays at bottom, Streak in the middle.

---

### Area 4: Panel stat layout

**Q:** The panel needs to show 4 values: current streak, longest streak, today's status, and total sessions. How should they be arranged?

**Options presented:**
- Two hero numbers + two secondary rows (current + longest large, today/total smaller)
- 2×2 equal grid — all four tiles with equal weight
- Plain vertical list — four labelled rows stacked

**Decision:** 2×2 equal grid. All four stats carry equal visual weight. Glanceable and uniform.

---

## Deferred / Out of Scope

- Calendar heatmap visualization (STREAK-calendar) — Future Requirements
- Streak notifications / reminders — not in REQUIREMENTS.md
- Per-day session count breakdown in panel — not required
