# Phase 10 Discussion Context: Streak Tracking

**Phase:** 10 — Streak Tracking
**Discussed:** 2026-08-25
**Requirements:** STREAK-01, STREAK-02, STREAK-03, STREAK-04, STREAK-05, STREAK-06

---

## Decisions

### 1. History cap / streak accuracy — LOCKED

History is capped at 14 entries. Rather than expanding the cap, persist computed streak values separately.

- Add `longestStreak` (Number, default 0) to the settings object under `STORAGE_KEY`
- Load it in `loadSettings()`, save it in `saveSettings()`
- After every `saveHistory()` call, recompute current streak from history, compare against `longestStreak`, and call `saveSettings()` if a new high is reached
- `longestStreak` survives as history ages off — it is a high-water mark, never decremented

**Why:** A 30-day consistent user would see a 14-day ceiling if we computed solely from history. Persisting the high-water mark is low-complexity (two extra fields) and accurate.

### 2. Zero-state badge design — LOCKED

Badge always shows `🔥 N` (flame + number). When `N = 0`, render at ~0.45 opacity (visually dimmed/muted). When `N ≥ 1`, full opacity.

**Why:** Consistent format — user always sees a count. Dimming signals inactivity without removing the element.

### 3. Streak button position — LOCKED

The `cornerControls` column order (top to bottom):
1. Settings button (existing)
2. **Streak button** (new — inserted between Settings and Info)
3. Info button (existing)

HTML: insert the new streak button element between `settingsBtn` and `infoBtn` in the DOM.

**Why:** Settings stays at top (most-used during setup), Info stays at bottom (least-used), Streak in the middle — naturally grouped.

### 4. Panel stat layout — LOCKED

A 2×2 equal-weight grid:

```
[ Current 🔥 N  ] [ Longest 🏆 N ]
[ Today  ✓/–    ] [ Total: N     ]
```

All four tiles carry equal visual weight. Labels above, value below (or inline for the today/total tiles).

**Why:** Uniform grid — no primary/secondary hierarchy. Matches the glanceable goal of STREAK-06.

---

## Architecture / Implementation Notes

### Date normalization

History entries store `date` as `new Date().toISOString()` (UTC ISO string). To compute streaks by local calendar day, normalize with:

```js
new Date(entry.date).toLocaleDateString('en-CA')  // → "YYYY-MM-DD" in local time
```

This gives a sortable, comparable local date string. Do NOT compare raw ISO strings — UTC date boundaries differ from local date boundaries.

### Streak computation logic

```js
function computeStreak(history) {
  // history: array of { date: ISOString, ... }, newest first
  // Returns { current, longest (from history only — caller merges with persisted longestStreak) }
}
```

- Extract unique local dates from history, sorted descending
- Walk consecutive days backward from today
- If today has a session: count starts at 1, walk back
- If today has no session but yesterday does: streak is still alive (count from yesterday back)
- If neither today nor yesterday: current streak = 0

"Missing a day" = no entry for a calendar day between today and the most recent session day.

### "Not yet today" logic (STREAK-03)

- Session exists for today → "Done today (N sessions)"
- No session today, but streak ≥ 1 (yesterday was done) → "Not yet — keep the streak alive"
- No session today, streak = 0 → "No sessions yet" (or same "Not yet" message)

### New localStorage fields

Add to settings object (under `STORAGE_KEY`):
- `longestStreak` — Number, default 0

Update after each `saveHistory()` call (in the two call sites on lines 2764 and 2951). After saving, recompute current streak, compare with loaded `longestStreak`, save if higher.

### Panel pattern

Follow info panel exactly:
- HTML: `streakBtn` in `cornerControls` + `streakOverlay` div (role="dialog") + `streakPanel` div inside
- CSS: reuse `.infoOverlay` / `.infoPanel` pattern — same overlay grid-place-center, same panel sizing
- JS: `openStreak()` / `closeStreak()` functions; `streakOverlay.addEventListener('click', e => { if (e.target === streakOverlay) closeStreak(); })`
- Escape key: add `streakOverlay` branch alongside existing `infoOverlay` / `settingsOverlay` branches in the keyboard handler
- Track open panel via `openPanelElement = streakOverlay`

### `computeStreak()` placement

Add to the **HELPERS** section (after `formatDuration()`). It is a pure function of a history array — no DOM or state dependencies.

### Badge update timing

Update badge (button label text) at:
1. Page load (after `loadSettings()` + `loadCustomPresets()`)
2. After every `saveHistory()` call (session complete)

### UI strings

Add to the frozen `UI` object in CONFIG:
- `streakBtnTitle` — tooltip/aria-label for the streak button
- `streakPanelTitle` — panel heading
- `streakCurrentLabel`, `streakLongestLabel`, `streakTodayLabel`, `streakTotalLabel` — grid tile labels
- `streakDoneToday`, `streakNotYet` — today-status text variants

---

## Prior Context Applied

From Phase 09 context:
- Single `<dialog>` modal pattern → streak uses the overlay pattern (not `<dialog>`), consistent with info/settings panels which both use overlay divs
- Silent `try/catch` for localStorage reads
- Section dividers `/* ====== SECTION NAME ====== */` preserved
- `@media (hover: hover)` for any hover-gated button styles on the streak button

---

## Out of Scope (Phase 10)

- Calendar heatmap visualization (deferred to Future Requirements / STREAK-calendar)
- Per-day session count chart
- Streak notifications or reminders
- Cross-device sync
