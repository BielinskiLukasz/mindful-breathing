---
quick_id: 20260704-fix-incomplete-session-save-rename-pause
status: in-progress
created: 2026-07-04
---

# Fix: Incomplete Session Save & Rename Stop → Pause

## Problem

"Stop" is actually "Pause" — the session is paused, not ended. After clicking Stop (pause), clicking Start resumes the same session. The current implementation saves an `incomplete: true` history entry on every pause, creating multiple entries for one session.

Additionally, the button label "Stop" is misleading — it should say "Pause" to match actual behavior.

## Fix

**Task 1: Rename Stop → Pause in UI constant**
- Line 1265: `btnStop: "Stop"` → `btnStop: "Pause"`
- Both `toggleBtn.textContent = UI.btnStop` usages at lines 2055 and 2091 automatically pick up the change

**Task 2: Move incomplete save from toggleBtn handler to reset()**
- Remove the `if (cycleCount >= 2) { saveHistory(...) }` block from the `if (isRunning)` branch in toggleBtn handler (~line 2197)
- Revert that branch to: `if (isRunning) { stop(); return; }`
- Add incomplete save to top of `reset()` function, guarded by:
  - `cycleCount >= 2` — at least 1 full cycle completed
  - `!doneOverlay.classList.contains("visible")` — not resetting after a completed session (done overlay shows after completion; resetting from there should NOT save another entry)
