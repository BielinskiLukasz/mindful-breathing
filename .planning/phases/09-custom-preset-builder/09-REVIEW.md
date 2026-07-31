---
phase: 09-custom-preset-builder
reviewed: 2026-07-31T00:00:00Z
depth: standard
files_reviewed: 1
files_reviewed_list:
  - index.html
findings:
  critical: 2
  warning: 2
  info: 1
  total: 5
status: issues_found
---

# Phase 09: Code Review Report

**Reviewed:** 2026-07-31
**Depth:** Standard
**Files Reviewed:** 1
**Status:** Issues Found

## Summary

The custom preset builder implementation includes several critical bugs that will cause runtime failures and data loss. The most severe issues are:

1. **CSV import parsing breaks due to unquoted fields** — all history imports will silently fail
2. **Custom preset phase lookup crashes when "Hold2" phase is used** — any custom preset with the final hold phase will cause an immediate error
3. **XSS vulnerability in history display** — user-supplied preset names are not escaped before HTML insertion
4. **Custom preset selection lost on page reload** — users cannot persist custom preset choices across sessions
5. **CSV import incomplete flag detection is incorrect** — checks wrong column

These issues must be resolved before shipping this feature.

---

## Critical Issues

### CR-01: CSV Import Parsing Fails – Quoted Fields Not Unquoted

**File:** `index.html:2470`

**Issue:** 

The CSV export function (lines 2352–2359) quotes all fields using the `escape` function:
```javascript
const escape = v => '"' + String(v).replace(/"/g, '""') + '"';
const rows = [["date", "duration", "cycles", "preset"].map(escape).join(",")];
```

This produces CSV like:
```
"2024-01-15T10:30:45.123Z","5:30","5","relax"
```

However, the CSV import at line 2470 does NOT unquote fields:
```javascript
const cols = lines[i].split(',').map(c => c.trim());
```

This leaves `cols[0]` as `'"2024-01-15T10:30:45.123Z"'` (including literal quote characters). When parsed:
- Line 2478–2479: `durationStr.split(':')` on `'"5:30"'` produces `['"5', '30"']`
- `parseInt('"5', 10)` returns `NaN`
- Line 2485: `!Number.isFinite(durationMs)` is true, so entry is skipped
- **Result:** All imported history entries are silently discarded

**Fix:**
```javascript
const cols = lines[i].split(',').map(c => {
  c = c.trim();
  // Unquote CSV-escaped fields
  if (c.startsWith('"') && c.endsWith('"')) {
    c = c.slice(1, -1).replace(/""/g, '"');  // Remove outer quotes, unescape doubled quotes
  }
  return c;
});
```

---

### CR-02: Undefined Phase Crash When "Hold2" Phase Used in Custom Preset

**File:** `index.html:1747–1758`

**Issue:**

Custom presets use four phase types: `["Inhale", "Hold", "Exhale", "Hold2"]` (line 1992).

When a custom preset is loaded, `buildActivePhases()` attempts to find a matching base phase in `PRESETS.relax`:

```javascript
const basePhase = PRESETS.relax.find(bp => bp.name === p.type);
return {
  name: p.type,
  durationSec: p.durationSec,
  breathR: basePhase.breathR,        // ← CRASH HERE if basePhase is undefined
  theme: basePhase.theme,
  cue: basePhase.cue,
  hint: basePhase.hint
};
```

However, `PRESETS.relax` (lines 1527–1532) defines phases with these names:
```javascript
{ name: "Inhale", ... },
{ name: "Hold", ... },
{ name: "Exhale", ... },
{ name: "Hold", ... }  // ← No "Hold2" phase
```

**Crash Scenario:**
1. User creates custom preset with "Hold2" phase enabled
2. User saves and selects the custom preset
3. Calls to `buildActivePhases()` search for phase named "Hold2" in `PRESETS.relax`
4. `.find()` returns `undefined`
5. Accessing `undefined.breathR` throws: `TypeError: Cannot read property 'breathR' of undefined`

**Fix:**

Update phase lookup to handle the second hold phase correctly:

```javascript
const basePhase = PRESETS.relax.find(bp => {
  // "Hold2" should map to the second "Hold" phase
  const searchName = p.type === "Hold2" ? "Hold" : p.type;
  return bp.name === searchName;
}) || PRESETS.relax.find(bp => bp.name === "Hold");  // Fallback

if (!basePhase) {
  console.error(`Phase type "${p.type}" not found in base presets`);
  return null;  // Skip this phase or use safe default
}
```

Alternatively, update `PRESETS` to rename the second hold phase to "Hold2" for consistency.

---

## Warnings

### WR-01: XSS Vulnerability – Unescaped Custom Preset Names in History Display

**File:** `index.html:2306`

**Issue:**

Custom preset names are user-supplied input (via `#presetNameInput` at line 1493). The preset name is not sanitized before being stored (saved at line 2091).

When rendering history (line 2306), the preset name is interpolated directly into HTML:

```javascript
return `<div class="historyItem"${rowStyle}>
  <span><span style="color: ${presetColor};">&#9679;</span> ${presetName} &bull; ...`;
```

**Attack Scenario:**
1. User creates custom preset named: `<img src=x onerror="alert('XSS')">`
2. User completes a session with this preset
3. History entry renders with unescaped HTML, executing the script

Elsewhere in the code (line 1866), preset names are correctly rendered using `.textContent`, which escapes HTML. The history display should do the same.

**Fix:**

Either:
1. Escape the preset name before insertion:
```javascript
const presetNameEscaped = presetName
  .replace(/&/g, '&amp;')
  .replace(/</g, '&lt;')
  .replace(/>/g, '&gt;')
  .replace(/"/g, '&quot;')
  .replace(/'/g, '&#39;');
// Then use in template:
// `... ${presetNameEscaped} &bull;`
```

2. Or, build the history list using DOM methods instead of HTML templates:
```javascript
const itemEl = document.createElement("div");
itemEl.className = "historyItem";
const spanEl = document.createElement("span");
spanEl.textContent = `${presetName} • ${formatDuration(s.durationMs)} ...`;
itemEl.appendChild(spanEl);
```

---

### WR-02: Custom Preset Selection Lost on Page Reload

**File:** `index.html:2209–2223`

**Issue:**

User's selected custom preset is not restored after a page reload.

**Root Cause:**
- At line 2631: `let activePresetKey = "relax"` (default)
- At initialization (line 2329): `loadSettings()` is called
- At line 2209: `if (data.preset && data.preset in PRESETS)` — this only checks **built-in** presets
- Custom preset IDs (like `"custom-1234567890"`) are NOT in `PRESETS`, so they fail this check
- At line 2330: `loadCustomPresets()` is called (too late — custom presets not yet loaded)
- Result: `activePresetKey` remains `"relax"`, custom preset selection is lost

**Data Flow:**
```
activePresetKey initialized to "relax"
  ↓
loadSettings() checks: if (data.preset in PRESETS) { /* only built-in presets */ }
  ↓
Custom preset IDs fail the check → activePresetKey stays "relax"
  ↓
loadCustomPresets() called (too late to restore)
  ↓
Custom preset button rendered but not marked active
```

**Fix:**

Restore custom preset selection by:
1. Moving `loadCustomPresets()` BEFORE `loadSettings()`:
```javascript
loadCustomPresets();  // Load first
loadSettings();       // Then load settings (can now access customPresets)
renderCustomPresets();
// ... rest
```

2. Then update `loadSettings()` to check both sources:
```javascript
if (data.preset) {
  if (data.preset in PRESETS) {
    // Built-in preset
    activePresetKey = data.preset;
    activePhases = PRESETS[activePresetKey].map(p => ({...p}));
  } else if (customPresets.some(p => p.id === data.preset)) {
    // Custom preset exists — just set the key; buildActivePhases() will handle it
    activePresetKey = data.preset;
  }
  // If preset is deleted, activePresetKey stays "relax" (safe fallback)
}
```

---

## Info

### IN-01: CSV Import Incomplete Flag Detection Uses Wrong Column

**File:** `index.html:2497`

**Issue:**

The CSV export (lines 2354–2360) outputs only 4 columns: `[date, duration, cycles, preset]`.

The import logic at line 2497 tries to detect an "incomplete" flag:
```javascript
const incomplete = cols.length > 3 && cols[3].toLowerCase().includes("incomplete");
```

This checks if the **4th column (preset)** contains the word "incomplete". This is incorrect because:
1. The preset column contains preset names like `"relax"` or custom preset IDs, not status flags
2. If a preset name happens to contain "incomplete", the flag will be set incorrectly
3. The export does not save the incomplete flag (see line 2500–2504, incomplete is only set if mis-detected)

**Impact:** Sessions imported from CSV will incorrectly mark some as incomplete, or conversely, truly incomplete sessions won't be flagged.

**Fix:**

Either:
1. Add the incomplete flag to the CSV export:
```javascript
rows.push([s.date, duration, s.cycles, s.preset || "relax", s.incomplete ? "Incomplete" : ""].map(escape).join(","));
```

2. And update the import header validation:
```javascript
const hasExpectedHeader = 
  headerLine.includes("date") &&
  headerLine.includes("duration") &&
  headerLine.includes("cycles");
  // Don't require "incomplete" — it's optional
```

3. And fix the incomplete detection to use the 5th column:
```javascript
const incomplete = cols.length > 4 && cols[4].toLowerCase().includes("incomplete");
```

Or, remove the incomplete flag from CSV import/export entirely and accept the limitation that CSV exports only capture core fields.

---

_Reviewed: 2026-07-31_
_Reviewer: Claude (gsd-code-reviewer)_
_Depth: standard_
