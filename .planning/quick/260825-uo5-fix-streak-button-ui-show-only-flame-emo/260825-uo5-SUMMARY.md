---
quick_id: 260825-uo5
status: complete
---

# Summary: Fix streak button UI + timezone-safe date arithmetic

## Changes

**Task 1 — Streak button flame-only:**
- `index.html` line ~1329: initial badge text changed from `🔥 0` to `🔥`
- `updateStreakBadge()` line ~3367: hardcoded `"🔥"` instead of template `🔥 ${current}`
- Opacity logic (0.45 when streak = 0) preserved unchanged

**Task 2 — Timezone-safe date arithmetic:**
- Added `parseLocal(str)` helper inside `computeStreak()` that constructs local-midnight Date via `new Date(y, m-1, d)` instead of `new Date(dateStr)` (which parses as UTC midnight)
- Replaced `new Date(today) - new Date(date)` with `parseLocal(today) - parseLocal(date)` for dayDiff
- Replaced `new Date(checkDate)` with `parseLocal(checkDate)` for prevDate arithmetic
- Fixes off-by-one errors in UTC- timezones (e.g. US Eastern) where `new Date('YYYY-MM-DD').getDate()` returns the previous local day
