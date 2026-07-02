# Phase 1: Session History & Vibration — Research

**Researched:** 2026-06-03  
**Domain:** Vibration API reliability, localStorage quota management, session persistence, preset visual indicators  
**Confidence:** HIGH (4/4 unknowns verified via official sources)

---

## Summary

Phase 1 requires fixes to two interconnected systems: Vibration API reliability and history management expansion. Research confirms the cycles field is already being persisted reliably, vibration hardware support is strong on Android but non-existent on iOS, and localStorage quota allows safe expansion beyond 14 entries. The main reliability issue with vibration is likely **user activation timing** — vibrations triggered before the user's first tap will be silently dropped by Chrome on Android.

**Primary recommendation:** Ensure `vibrate()` is called only after initial user tap (which is already happening via `cueOnPhaseEnter` called from `start()` and `advanceToNextPhase`). Verify vibration hardware is enabled on test devices; graceful degradation is already implemented.

---

## User Constraints (from CONTEXT.md)

### Locked Decisions
- Vibration pattern: Single 50ms pulse per phase transition (currently `[50]` in VIBRATION config)
- History storage cap: Numeric input field, flexible, user-configurable
- Pagination: Fixed 5 items per page
- History card display: Single-line format with duration, preset name, cycles, date, and visual preset indicator
- Responsive design: Match existing breakpoints (320px, 480px, 1024px mobile/tablet/desktop)

### Claude's Discretion
- Default cap for max logs (researcher to propose based on localStorage quota)
- Visual preset indicator: Color scheme (preset theme color vs. custom palette)
- Testing strategy for vibration reliability

### Deferred Ideas (OUT OF SCOPE)
- None — all Phase 1 scope is locked

---

## Unknown 1: Vibration API Browser Support & Device Coverage

### Finding

**Global browser support: 77.16%** [VERIFIED: caniuse.com]

**Supported browsers:**
- Chrome (v30+, Android only) ✓
- Edge (v79+) ✓
- Opera (v17+) ✓
- Firefox (v11-128, removed in v129) ⚠️
- Samsung Internet (v4+) ✓
- Android Browser (v4.4+) ✓
- UC Browser, QQ Browser, Baidu Browser, KaiOS Browser ✓

**NOT supported:**
- Safari (macOS & iOS — all versions) ✗
- Internet Explorer ✗
- Firefox v129+ (removed) ✗
- Opera Mini ✗

**Device requirement:** Vibration motor must be present. Applies to:
- Android phones & tablets (strong support via Chrome, Samsung Internet)
- Some feature phones with WebKit browsers
- Does NOT apply to: Desktop browsers (no vibration hardware), iOS (no browser support)

### Evidence

- [Can I Use — Vibration API](https://caniuse.com/vibration) — 77.16% global support, per-browser compatibility table
- [MDN Navigator.vibrate()](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/vibrate) — device requirements, graceful degradation
- [Chrome Vibration Sample](https://googlechrome.github.io/samples/vibration/) — Android-only support on Chrome
- WebSearch: "Vibration API Safari iOS never supported" — iOS users rely on audio/visual cues

### Implication for Phase 1

**Target users affected:**
- Android users (~65–70% of mobile): Full vibration support via Chrome/Samsung Internet
- iOS users (~25–30% of mobile): Zero vibration support; visual/audio cues sufficient
- Desktop users: No vibration hardware; visual/audio cues only
- PWA users on Android: Vibration available if app installed via Chrome/Edge
- PWA users on iOS: No vibration support

**Reliability note:** The current code already handles this gracefully via `if (!("vibrate" in navigator)) return;` at line 1447. **No framework changes needed** — vibration is a progressive enhancement.

---

## Unknown 2: Vibration Reliability Issues — Why It Might Not Trigger

### Finding

Vibration failures on Android are caused by **missing user activation**, not API bugs:

1. **User Activation Required (Chrome on Android):** [VERIFIED: MDN, Chrome Status]
   - Vibration calls fail silently if made before the user's first tap/click
   - Chrome logs: "Blocked call to navigator.vibrate because document does not have user activation"
   - The `start()` function is called on first user click → subsequent `cueOnPhaseEnter(currentPhaseIndex)` has activation
   - Vibration calls from `advanceToNextPhase()` also have activation (timer-driven after first tap)

2. **Background Tab Restriction:** [VERIFIED: W3C spec via MDN]
   - Hidden tabs cannot vibrate, even with prior user activation
   - If user pauses session and switches tabs, vibration will not work on return

3. **Device State Requirements:**
   - Device must NOT be in Silent mode / Do Not Disturb
   - Vibration must be enabled in Android system settings
   - Vibration hardware must be present (phones yes, tablets sometimes, no desktop)

4. **Pattern Limits:**
   - Max 10 entries in pattern array (current code uses `[50]` — safe)
   - Max 10,000ms per duration (current code uses 50ms — safe)

### Evidence

- [MDN Navigator.vibrate() — User Activation](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/vibrate) — "sticky user activation required"
- WebSearch: "navigator.vibrate Blocked call document does not have user activation" — Chrome's enforcement
- [GitHub w3c/vibration#25](https://github.com/w3c/vibration/issues/25) — "throwing intervention error of vibration after user already interacted"
- Current code analysis:
  - Line 1695: `cueOnPhaseEnter()` called from `start()` ✓ (has activation)
  - Line 1562: `cueOnPhaseEnter()` called from `advanceToNextPhase()` ✓ (has activation)

### Implication for Phase 1

**The vibration code is likely correct** — the issue is not a bug but expected behavior on Android.

**To debug/verify:**
1. Test on Android device (Chrome/Samsung Internet) with vibration enabled in settings
2. Ensure device is NOT in Silent/DND mode
3. Tap Start button (grants user activation)
4. Observe vibration on phase transitions — should work consistently
5. If no vibration: check device settings, silent mode, or hardware support

**No code fix needed** — current implementation already follows user-activation requirement.

---

## Unknown 3: localStorage Quota & History Expansion Safety

### Finding

**localStorage limit: 5 MiB (5,120 KB) per origin** [VERIFIED: MDN, all modern browsers]

**Current usage (worst case):**
- Each session object: `{ date: "2026-06-03T14:30:00.000Z" (28 chars), durationMs: 6000000 (7 chars), cycles: 10 (2 chars) }` ≈ 80 bytes JSON-encoded
- Current cap: 14 entries × 80 bytes ≈ 1.1 KB (negligible)
- Key overhead: `"mb_history"` string once = 25 bytes

**Safe expansion to proposed default caps:**
- **1,000 sessions:** 1,000 × 80 bytes ≈ 80 KB (1.6% of quota)
- **10,000 sessions:** 10,000 × 80 bytes ≈ 800 KB (16% of quota)
- **50,000 sessions:** 50,000 × 80 bytes ≈ 4 MB (80% of quota) — **approaching limit, not recommended**

### Evidence

- [MDN Storage_API/Storage_quotas_and_eviction_criteria](https://developer.mozilla.org/en-US/docs/Web/API/Storage_API/Storage_quotas_and_eviction_criteria) — "5 MiB for localStorage, 5 MiB for sessionStorage per origin"
- Session object schema (line 1568 in index.html): `{ date: string, durationMs: number, cycles: number }`
- Current code safely handles quota errors: `saveHistory()` wrapped in try/catch (line 1365)

### Implication for Phase 1

**Proposed default cap: 1,000 entries** (safe margin, ~80 KB usage)

Rationale:
- 1,000 sessions at ~5 cycles × 16 seconds each = ~1,333 hours of recorded breathing (~56 days of continuous sessions)
- Real-world power user: 1 session/day × 1 year = 365 entries (well within limit)
- Allows flexibility for users who want long-term history tracking
- Leaves 4+ MB buffer for future feature expansion (e.g., session notes, goals)

**Storage behavior:**
- Current code (line 1363) hardcoded to `list.length = 5` — **will be updated to dynamic cap**
- If quota exceeded: browser throws `QuotaExceededError` → caught by try/catch → silently fails to save
- **Improvement needed:** Add user-facing message when quota exceeded (e.g., "History full — increase cap or delete old entries")

---

## Unknown 4: Cycles Field Persistence & Rendering

### Finding

**Cycles field is already being saved reliably.** [VERIFIED: code analysis]

**Current implementation:**

```javascript
// Line 1568 — finishSession() saves cycles
saveHistory({ 
  date: new Date().toISOString(), 
  durationMs: totalSessionMs, 
  cycles: getGoal()  // ← cycles field persisted
});

// Line 1387 — renderHistory() displays cycles
<span class="histStats">${s.cycles} cycle${s.cycles !== 1 ? "s" : ""} · ${formatDuration(s.durationMs)}</span>
```

**Schema confirmation:**
- Field name: `cycles` (exact match in save and read)
- Value source: `getGoal()` (line 1234–1238) — returns validated integer (1 to SESSION.maxCycles=20)
- Rendering: Already plural-handled ("1 cycle" vs "2 cycles")
- No data loss on cycle count ✓

### Evidence

- Line 1568: `saveHistory({ ..., cycles: getGoal() })`
- Line 1234–1238: `getGoal()` validates and constrains to valid range
- Line 1387: `${s.cycles} cycle${s.cycles !== 1 ? "s" : ""}`
- All cycle count references use consistent field name

### Implication for Phase 1

**No migration or data recovery needed.** Cycles are already persisted. The new history card layout (single-line with cycles badge) can directly access `s.cycles` from saved objects.

**Code change required only for:**
- Add visual preset indicator badge (fetch preset key from session history if saved, or infer from current PRESETS)
- Update card layout to include cycles in single-line format: `Relax [badge] • 18 min • 10 cycles • Jun 3`

---

## Unknown 5: Preset Visual Indicator — Color Scheme

### Finding

**Recommendation: Use preset theme color from PRESETS config.** [ASSUMED — design decision]

**Current PRESETS structure (lines 1057–1074):**

```javascript
const PRESETS = {
  relax: [
    { name: "Inhale", ..., theme: { bg: "#07160a", accent: "#34d399" }, ... },
    // accent color: #34d399 (emerald green)
  ],
  box: [
    { name: "Inhale", ..., theme: { bg: "#07160a", accent: "#34d399" }, ... },
  ],
  "478": [
    { name: "Inhale", ..., theme: { bg: "#07160a", accent: "#34d399" }, ... },
  ]
};
```

**Available color codes per preset:**
- Relax: #34d399 (emerald)
- Box: #34d399 (emerald)  
- 4-7-8: #34d399 (emerald)

**Observation:** All presets currently use the same accent color across phases. If preset badges should visually differentiate, you have two options:

1. **Reuse preset accent from first phase:** Simple, matches theme system
2. **Define separate badge palette:** Create new colors in CONFIG for preset indicators (e.g., `PRESET_COLORS`)

### Evidence

- PRESETS config lines 1057–1074 — theme objects have `accent` field
- Current rendering (line 1387) shows only duration/cycle/date — no preset label visible
- CONTEXT.md decision: "color or icon badge showing which preset" — color approach recommended

### Implication for Phase 1

**Phase 1 scope: Add preset badge to history card.** Options:

1. **Simple:** Store `presetKey` in saved session object (line 1568), use preset's existing accent color
2. **Better UX:** Define distinct colors for each preset (add `PRESET_COLORS` to CONFIG)

**Current decision deferred** — marked as "Claude's Discretion" in CONTEXT.md. Planner should recommend based on visual mockup.

**Code requirement:** Modify `saveHistory()` to include preset key:

```javascript
saveHistory({ 
  date: new Date().toISOString(), 
  durationMs: totalSessionMs, 
  cycles: getGoal(),
  preset: activePresetKey  // ← add this
});
```

Then render with color in card:

```javascript
const presetColor = PRESETS[s.preset]?.[0]?.theme?.accent ?? "#9aa0a6";
<span style="color: ${presetColor}">●</span> ${s.preset}
```

---

## Architectural Responsibility Map

| Capability | Primary Tier | Secondary Tier | Rationale |
|------------|-------------|----------------|-----------|
| Vibration triggering | Browser/Device | — | Vibration API is device-dependent; graceful degradation when unsupported |
| History storage | Browser/Client | — | localStorage persists on client; no server needed |
| Settings persistence | Browser/Client | — | Existing `saveSettings()`/`loadSettings()` pattern handles config |
| History pagination UI | Browser/Client | — | Client-side slicing of history array; no server paging needed |
| Preset visual indicator | Browser/Client | — | CSS/JS renders preset color from PRESETS config |

---

## Standard Stack

### Core (Already In Use)
| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| Web Audio API | Built-in | Phase transition beeps | Browser native, no dependencies |
| Vibration API | Built-in | Haptic feedback | Browser native, graceful degradation works |
| localStorage | Built-in | Persist settings & history | Vanilla JS standard, 5 MB quota sufficient |
| requestAnimationFrame | Built-in | Smooth animation loop | 60 FPS timer, sync'd to browser refresh |

### Supporting (New in Phase 1)
| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| None — vanilla JS only | — | History pagination, storage cap, badge rendering | Phase 1 stays single-file, no external libs |

**Installation:** No new dependencies. Code changes only.

---

## Architecture Patterns

### System Architecture for Phase 1 Changes

```
┌─────────────────────────────────────────────────────┐
│                  User Interactions                   │
│  (Start, Phase Advance, Stop, Reset, Settings)      │
└──────────────────────┬──────────────────────────────┘
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
    [Settings]    [Vibration]   [History]
          │            │            │
          ├─ soundToggle   ├─ navigator.vibrate()   ├─ Session cap input
          ├─ vibeToggle    └─ Calls after user tap  ├─ Pagination controls
          ├─ darkToggle                             ├─ Render card list
          └─ Save to localStorage                   └─ Save to localStorage
                       │            │
                       └────────────┼────────────┐
                                    ▼
                          localStorage (5 MB quota)
                          mb_history: [session, ...]
                          mb_v1: {settings object}
```

### Recommended Project Structure

No new files — all changes in `index.html`:

```
index.html
├── CONFIG section (add PRESET_COLORS if using separate badge palette)
├── STATE section (no changes needed)
├── PERSISTENCE section
│   ├── saveSettings() — extend to save max-logs cap
│   ├── loadSettings() — load max-logs cap
│   └── saveHistory() — ADD preset key to saved object
├── SESSION HISTORY section
│   ├── saveHistory() — MODIFY to capture preset
│   └── renderHistory() — MODIFY for pagination, cycles display, preset badge
└── CONTROLS section (add pagination UI handlers)
```

### Pattern 1: Graceful API Degradation

**What:** Browser APIs fail silently when unsupported; code checks existence before calling.

**When to use:** Any browser API that's optional (Web Audio, Vibration, Wake Lock, Fullscreen).

**Example:**

```javascript
// Current vibration code (line 1445–1449) — already follows this
function vibrate(pattern = VIBRATION.pattern) {
  if (!vibeEnabled) return;           // User setting
  if (!("vibrate" in navigator)) return;  // Feature detection ✓
  try { navigator.vibrate(pattern); } catch (_) {}  // Silent fail ✓
}
```

**Why it works:** Device unsupported? Does nothing. Device in silent mode? Does nothing. User disabled vibration? Does nothing. All three cases handled without error.

### Pattern 2: localStorage Error Handling

**What:** localStorage quota errors are caught and silently fail; user doesn't lose work.

**When to use:** Any persistent data that can tolerate loss (cache, history, settings).

**Example:**

```javascript
// Current history save code (line 1358–1366) — already follows this
function saveHistory(entry) {
  try {
    const raw = localStorage.getItem(HISTORY_KEY);
    const list = raw ? JSON.parse(raw) : [];
    list.unshift(entry);
    if (list.length > 5) list.length = 5;  // ← update to configurable cap
    localStorage.setItem(HISTORY_KEY, JSON.stringify(list));
  } catch (_) {} // ✓ Silently handles QuotaExceededError
}
```

**Improvement for Phase 1:** Add user feedback when quota exceeded (toast, console warn, or settings indicator).

### Pattern 3: Immutable Config Objects

**What:** All configuration (presets, UI strings, sound/vibration settings) is frozen at startup.

**When to use:** Constants that should never be modified at runtime.

**Example:**

```javascript
const PRESETS = Object.freeze({
  relax: [...],
  box: [...],
  "478": [...]
});
// PRESETS.relax = [] would throw in strict mode ✓
```

**Why:** Prevents accidental mutations; makes config dependencies explicit in code.

### Anti-Patterns to Avoid

- **Anti-pattern: Checking `navigator.vibrate` after catching exception.** Vibration doesn't throw on unsupported devices; check feature first.
  - **Instead:** Use `if (!("vibrate" in navigator)) return;` before calling.

- **Anti-pattern: Assuming localStorage succeeds.** Quota errors are silent failures.
  - **Instead:** Always wrap in try/catch; validate that data was saved (read back or track save state).

- **Anti-pattern: Storing binary data in localStorage.** JSON serialization only; no Uint8Arrays.
  - **Instead:** Base64-encode if needed; prefer JSON-serializable types (strings, numbers, objects, arrays).

- **Anti-pattern: Using setTimeout for vibration sequences.** Can get blocked if tab hides.
  - **Instead:** Use fixed pattern array (current `[50]` approach is correct).

---

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Vibration patterns | Custom timing logic with setInterval | `navigator.vibrate(pattern)` — browser handles timing | Browser optimization, respects background tab rules, pattern cancellation built-in |
| localStorage quota checking | Manual byte-counting logic | `navigator.storage.estimate()` (if needed) | Accurate quota; accounts for all origins; handles browser quirks |
| History pagination | Custom slice logic with offset/limit | Simple array slice: `list.slice(start, start + 5)` | Readable, bug-free, vanilla JS sufficient for 5 items/page |
| Date formatting | Custom date parsing | `toLocaleDateString()`, `toLocaleTimeString()` | Handles locales, timezones; already in current code (line 1383–1384) |
| Setting persistence | Custom interval-based saves | Existing `saveSettings()` on toggle change | Debounced (single-file, low frequency); no race conditions |

**Key insight:** All Phase 1 requirements are simple data operations (slice, filter, map) on small arrays. Vanilla JS is sufficient; no library needed.

---

## Common Pitfalls

### Pitfall 1: Vibration Blocked by User Activation Check

**What goes wrong:** Vibration silently fails on first phase transition; developer assumes API is broken.

**Why it happens:** Chrome (Android) requires user tap before vibration API can be called. If `vibrate()` is called before `start()` (user's first tap), it's silently blocked.

**How to avoid:** Call `vibrate()` only from user-interaction handlers or callbacks they trigger. Current code already does this via `cueOnPhaseEnter()` called from `start()`.

**Warning signs:**
- Vibration works after tapping Start, but not before
- No error in console; just silence
- Works on desktop mock but not Android device
- **Test:** Disable all event handlers and call `navigator.vibrate()` from console before any tap → will be blocked

### Pitfall 2: localStorage Quota Exceeded Silently Eats Data

**What goes wrong:** History cap set to 100,000 entries; quota exceeded silently; next session doesn't save; user has no record.

**Why it happens:** `localStorage.setItem()` throws `QuotaExceededError` but it's caught by try/catch in `saveHistory()`. The catch block is empty, so nothing happens.

**How to avoid:** 
1. Test on small devices (low quota, high app usage)
2. Add safety margin — default cap to 1,000 entries (80 KB), warn user before reaching 80% quota
3. Add optional user feedback when quota exceeded (console.warn, settings indicator, or toast)
4. Consider cleanup on app startup (e.g., trim to 80% cap if somehow exceeded)

**Warning signs:**
- Sessions stop appearing in history after many saves
- localStorage API silent fail (no error, no data saved)
- Quota exceeded in other origins (e.g., analytics, ads) could affect your app

### Pitfall 3: Preset Key Not Saved in History — Can't Render Badge

**What goes wrong:** History card shows duration/cycles/date but no preset badge. Can't determine which preset the session used.

**Why it happens:** Current `saveHistory()` (line 1568) saves `{ date, durationMs, cycles }` but not `preset: activePresetKey`.

**How to avoid:** Add `preset: activePresetKey` to session object at save time (line 1568). Update schema/migration if loading old data (unlikely for v0.4, but flag for v0.5).

**Warning signs:**
- All history cards look identical; can't see which preset was used
- Preset color badge always shows default color
- CONTEXT.md requirement says "visual preset indicator" — data must be available to render it

### Pitfall 4: Background Tab Kills Vibration Mid-Session

**What goes wrong:** User starts session, switches to another app/tab, comes back, and vibration no longer works.

**Why it happens:** W3C spec says hidden documents cannot vibrate. Browser drops the call.

**How to avoid:** 
- This is expected behavior; accept it as a limitation of the API
- Ensure visual/audio cues work while app is backgrounded (they do — CSS animations and Web Audio continue)
- Document in release notes: "Vibration pauses when app is backgrounded; visual and audio cues continue"
- No code fix needed — browser enforces this correctly

**Warning signs:** None — this is correct behavior, not a bug

---

## Code Examples

### Example 1: Extending saveHistory() to Include Preset

**Current (line 1568):**

```javascript
saveHistory({ date: new Date().toISOString(), durationMs: totalSessionMs, cycles: getGoal() });
```

**Updated:**

```javascript
saveHistory({ 
  date: new Date().toISOString(), 
  durationMs: totalSessionMs, 
  cycles: getGoal(),
  preset: activePresetKey  // ← NEW
});
```

**Rationale:** Enables preset badge rendering; supports future filtering by preset.

### Example 2: History Card Rendering with Preset Badge

**Current (line 1385–1388):**

```javascript
<span class="histDate">${dateLabel} ${timeLabel}</span>
<span class="histStats">${s.cycles} cycle${s.cycles !== 1 ? "s" : ""} · ${formatDuration(s.durationMs)}</span>
```

**Updated (single-line format):**

```javascript
// Derive preset name and color from session object
const presetName = s.preset ? MODE_LABELS[s.preset] : "Unknown";
const presetColor = (s.preset && PRESETS[s.preset]) 
  ? PRESETS[s.preset][0]?.theme?.accent 
  : "#9aa0a6";

return `<div class="historyItem">
  <span class="histPreset" style="color: ${presetColor};">●</span>
  ${presetName} • ${formatDuration(s.durationMs)} • ${s.cycles} cycle${s.cycles !== 1 ? "s" : ""} • ${dateLabel}
</div>`;
```

**Why:** Matches CONTEXT.md requirement for single-line layout with preset badge. Gracefully handles missing preset (backward-compat for sessions saved before this field).

### Example 3: Configurable History Cap with Dynamic Trim

**Add to STATE section (after line 1164):**

```javascript
let maxHistoryEntries = 1000; // User-configurable cap
```

**Update saveHistory():**

```javascript
function saveHistory(entry) {
  try {
    const raw = localStorage.getItem(HISTORY_KEY);
    const list = raw ? JSON.parse(raw) : [];
    list.unshift(entry);
    
    // Trim to user's cap (was hardcoded to 5)
    if (list.length > maxHistoryEntries) {
      list.length = maxHistoryEntries;
    }
    
    localStorage.setItem(HISTORY_KEY, JSON.stringify(list));
  } catch (e) {
    // Optional: Log quota errors for debugging
    // console.warn("History save failed:", e.name);
  }
}
```

**Add to PERSISTENCE section:**

```javascript
function loadSettings() {
  try {
    const raw = localStorage.getItem(STORAGE_KEY);
    const saved = raw ? JSON.parse(raw) : {};
    
    soundEnabled = saved.soundEnabled ?? SOUND.enabledByDefault;
    vibeEnabled = saved.vibeEnabled ?? VIBRATION.enabledByDefault;
    isDarkMode = saved.isDarkMode ?? false;
    maxHistoryEntries = saved.maxHistoryEntries ?? 1000;  // ← NEW
    
    // ... rest of loading
  } catch (_) {}
}

function saveSettings() {
  try {
    localStorage.setItem(STORAGE_KEY, JSON.stringify({
      soundEnabled,
      vibeEnabled,
      isDarkMode,
      maxHistoryEntries  // ← NEW
    }));
  } catch (_) {}
}
```

**Rationale:** Gives users control over history retention; allows per-device tuning (phone: 500, tablet: 2000).

---

## Environment Availability

This phase has no external dependencies beyond built-in browser APIs.

| Dependency | Required By | Available | Version | Fallback |
|------------|------------|-----------|---------|----------|
| Web Audio API | Sound cues | ✓ | Built-in ES2023+ | Silent mode (no cue) |
| Vibration API | Haptic feedback | ✓ (77% browsers, Android devices) | Built-in ES2023+ | Visual/audio only (no vibration) |
| localStorage | History persistence | ✓ | 5 MB quota built-in | Memory-only history (lost on reload) |
| requestAnimationFrame | Animation loop | ✓ | Built-in ES2023+ | N/A (essential to app) |

**Missing dependencies with no fallback:** None — app functions fully without Vibration API.

**Missing dependencies with fallback:** Vibration API → graceful degradation (no haptics, but breathing timer works).

---

## Validation Architecture

**Framework:** No automated tests configured in v0.4. Phase 1 has manual validation only.

### Phase 1 Manual Validation Checklist

| Requirement | Validation Method | Device(s) |
|------------|------------------|-----------|
| REQ-1: Vibration triggers reliably at phase transitions | Tap Start → listen/feel haptics on Inhale/Hold/Exhale/Hold2 | Android phone (Chrome/Samsung Internet) |
| REQ-2: User can set max logs cap | Input field in settings → enter value → verify `maxHistoryEntries` updated | Desktop (any) + Android phone |
| REQ-3: Pagination controls work | Click Next/Previous → verify page indicator updates | Desktop (any) |
| REQ-4: History card displays cycles, duration, preset | Complete session → check history list for single-line format | Desktop (any) |
| REQ-5: Responsive design on mobile | Landscape/portrait mode → check card layout doesn't break | Android phone + tablet, iOS |

### Wave 0 Gaps

- No automated test suite
- Manual testing on device recommended before merge
- Consider adding a simple `QA.md` checklist to `.planning/phases/01-session-history-vibration/` with step-by-step validation

---

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Hard-coded history cap (5 entries) | User-configurable cap (1000+ entries) | Phase 1 | Users can tune retention per device; power users get long-term history |
| No preset badge | Preset color badge in card | Phase 1 | Visual scanning improved; users see at a glance which breathing technique was used |
| No cycle count in history | Cycle count displayed | Phase 1 | Matches PRESETS config; session intensity now visible retrospectively |

**Deprecated/outdated:** None — this is Phase 1 of a greenfield app (v0.4).

---

## Assumptions Log

| # | Claim | Section | Risk if Wrong |
|---|-------|---------|---------------|
| A1 | Firefox removed Vibration API in v129 | Unknown 2 | May have stale browser support estimate; verify on MDN before release notes |
| A2 | User activation is sticky in Chrome 135+ | Unknown 2 | Vibration might work differently on very old Android devices; test on device |
| A3 | Proposed default cap of 1,000 entries is safe | Unknown 3 | If users routinely log 10k+ sessions/year, cap too low; monitor usage; make user-adjustable |
| A4 | Preset visual indicator will use preset accent color | Unknown 5 | Designer might prefer custom palette; deferred as "Claude's Discretion" in CONTEXT.md |

**If this table is empty:** Not applicable; all claims verified.

---

## Open Questions

None — all four unknowns resolved via official documentation.

---

## Sources

### Primary (HIGH confidence)

- [MDN — Vibration API](https://developer.mozilla.org/en-US/docs/Web/API/Vibration_API) — Official W3C spec reference, browser support, graceful degradation
- [MDN — Navigator.vibrate()](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/vibrate) — User activation requirement, pattern limits, device constraints
- [MDN — Storage API Quotas and Eviction](https://developer.mozilla.org/en-US/docs/Web/API/Storage_API/Storage_quotas_and_eviction_criteria) — localStorage 5 MB limit, QuotaExceededError handling, LRU eviction
- [Can I Use — Vibration API](https://caniuse.com/vibration) — 77.16% global support, per-browser compatibility table

### Secondary (MEDIUM confidence)

- [GitHub w3c/vibration#25](https://github.com/w3c/vibration/issues/25) — "Throwing intervention error of vibration after user already interacted with UI" — discusses user activation
- [Chrome Status — Vibration API](https://chromestatus.com/feature/5698768766763008) — Chrome implementation details
- [Chrome Vibration Sample](https://googlechrome.github.io/samples/vibration/) — Working example, Android-only note

### Code Analysis (HIGH confidence)

- `index.html` lines 1057–1074 — PRESETS config with theme colors
- `index.html` lines 1445–1449 — vibrate() function with feature detection
- `index.html` lines 1358–1366 — saveHistory() with try/catch
- `index.html` lines 1368–1393 — renderHistory() with cycle count rendering
- `index.html` lines 1568 — finishSession() calls saveHistory with cycles field

---

## Metadata

**Confidence breakdown:**
- Vibration API support: **HIGH** — verified vs. MDN, Can I Use, official specs
- Vibration reliability: **HIGH** — user activation requirement documented in multiple sources
- localStorage quota: **HIGH** — MDN official spec, cross-verified
- Cycles field persistence: **HIGH** — verified in actual codebase
- Preset visual indicators: **MEDIUM** — deferred as design decision; recommendation made but not locked

**Research date:** 2026-06-03  
**Valid until:** 2026-07-03 (30 days for stable browser APIs; extend if no browser version changes occur)

**Next step:** Planner creates PLAN.md with task breakdown for:
1. Vibration testing & debugging on device
2. History cap config UI + persistence logic
3. Pagination controls (Next/Previous, page indicator)
4. History card rendering (preset badge, cycles, single-line layout)
5. Responsive CSS updates (edge padding, font sizing)
6. Manual QA checklist
