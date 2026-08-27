---
quick_id: 20260704-fix-incomplete-session-save-rename-pause
status: complete
created: 2026-07-04
completed: 2026-07-04
commit: 49dbe4a
---

# Fix: Incomplete Session Save & Rename Stop → Pause

## What was done

**Bug:** Stop button (which is actually Pause) saved an `incomplete: true` history entry on every pause. Resuming and pausing again created multiple duplicate entries for the same session.

**Root cause:** The incomplete save was placed in the `toggleBtn` click handler's `if (isRunning)` branch — which fires on every pause, not just on session abandonment.

**Fix:**
1. Removed the `saveHistory(incomplete: true)` block from the `toggleBtn` handler → handler reverts to `stop(); return;`
2. Added incomplete save to the top of `reset()`, guarded by:
   - `cycleCount >= 2` — at least 1 full cycle completed (keeps D-01 threshold)
   - `!doneOverlay.classList.contains("visible")` — prevents double-save when resetting after a normally completed session (done overlay shows on completion)
3. Renamed `UI.btnStop` from `"Stop"` to `"Pause"` — both usages in `updateStatusUI()` automatically pick up the new label

## Behavior after fix

| Action | Entry saved? |
|--------|-------------|
| Click Pause (was Stop) | No — session stays alive |
| Click Pause → Start (resume) → Pause again | No — still same session |
| Click Pause → Reset (1+ cycles done) | Yes — 1 `incomplete: true` entry |
| Click Pause → Reset (0 cycles done) | No — threshold not met |
| Complete all cycles → done overlay → Reset | No — done overlay guard fires |
| Complete all cycles | Yes — existing complete entry via finishSession() |

## Files changed

- `index.html`: 3 targeted changes (net -8 lines: removed 20, added 12)
