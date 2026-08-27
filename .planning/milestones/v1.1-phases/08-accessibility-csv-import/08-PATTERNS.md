# Phase 8: Accessibility & CSV Import - Pattern Map

**Mapped:** 2026-07-28
**Files analyzed:** 7 modifications to index.html
**Analogs found:** 7/7 with strong matches

## File Classification

| New/Modified File | Role | Data Flow | Closest Analog | Match Quality |
|-------------------|------|-----------|----------------|---------------|
| `index.html` (CSS `:focus-visible`) | styling | request-response | `.durInput:focus` style rule | exact |
| `index.html` (`#phaseEl aria-live`) | HTML attribute | event-driven | `.infoPanel role="dialog"` | role-match |
| `index.html` (Panel focus trap logic) | controller | request-response | `openInfo()` / `closeInfo()` handlers | exact |
| `index.html` (Keyboard guard) | controller | request-response | KEYBOARD SHORTCUTS handler | exact |
| `index.html` (CSV parser function) | service | file-I/O + CRUD | `importJson()` function | data-flow-match |
| `index.html` (CSV import handler) | controller | file-I/O + CRUD | Import button click handler | exact |
| `index.html` (File input accept attribute) | HTML attribute | request-response | `<input type="file" accept=".json">` | exact |

---

## Pattern Assignments

### CSS: `:focus-visible` rule (A11Y-03, D-01, D-02)

**Analog:** CSS section, line 536 (`.durInput:focus` pattern)

**Current pattern** (lines 536):
```css
.durInput:focus { outline: none; border-color: var(--accent); }
```

**New pattern — add to CSS section** (after hover/active states, around line 1125):
```css
/* ====== FOCUS INDICATORS (keyboard-only, D-01, D-02) ====== */
:focus-visible {
  outline: 2px solid var(--accent);
  outline-offset: 3px;
}
```

**Rationale:**
- Applies globally to all interactive elements (buttons, inputs, sliders, icon toggles, close buttons)
- Uses CSS custom property `var(--accent)` — adapts to both light and dark themes automatically
- `:focus-visible` pseudo-class ensures keyboard-only focus (not on mouse/touch), per D-02
- 3px offset provides visual separation per D-01 spec

---

### HTML: Add `aria-live="polite"` to `#phaseEl` (A11Y-02, D-03)

**Analog:** HTML section, line 1198 (info panel ARIA attributes)

**Current element** (line 1264):
```html
<div class="phase" id="phase">Pause</div>
```

**Modified** (add attribute):
```html
<div class="phase" id="phase" aria-live="polite">Pause</div>
```

**Rationale:**
- `aria-live="polite"` makes phase name changes announced by screen readers
- Announced events: phase transitions (Inhale → Hold → Exhale) + session state (started/paused)
- "Polite" politeness: phase changes happen every 4–8 seconds, plenty of time for announcements (D-04)
- No new element needed — reuses existing `#phaseEl` that already updates on phase change
- Per D-05: Do NOT announce cycle count or elapsed time on every second — would be unusable

---

### HTML: Update file input `accept` attribute (HIST-12, D-11)

**Analog:** HTML section, line 1318 (existing import file input)

**Current element** (line 1318):
```html
<input type="file" id="importFileInput" accept=".json" style="display:none">
```

**Modified** (change accept attribute):
```html
<input type="file" id="importFileInput" accept=".json,.csv" style="display:none">
```

**Rationale:**
- Single import button handles both JSON and CSV (D-11)
- File extension detection in import handler will route to correct parser
- Handler checks `file.name.endsWith('.csv')` vs default JSON

---

### Panel Focus Management (A11Y-01, D-08, D-09)

**Analog:** PANELS section, lines 2513–2539 (info panel open/close)

**Current pattern** (lines 2516–2523):
```javascript
function openInfo() { infoOverlay.classList.add("visible"); }
function closeInfo() { infoOverlay.classList.remove("visible"); }

document.getElementById("infoBtn").addEventListener("click", openInfo);
infoCloseBtn.addEventListener("click", closeInfo);
infoOverlay.addEventListener("click", e => {
  if (e.target === infoOverlay) closeInfo();
});
```

**New pattern — modify openInfo/closeInfo/settings handlers** (D-08, D-09):

For each panel open function, add focus movement and trap:
```javascript
let openPanelElement = null;  // Track which panel is open (global state)

function openInfo() {
  infoOverlay.classList.add("visible");
  openPanelElement = infoOverlay;
  document.getElementById("infoCloseBtn").focus();  // D-08: move focus to close button
}

function closeInfo() {
  infoOverlay.classList.remove("visible");
  openPanelElement = null;
  document.getElementById("infoBtn").focus();  // D-09: return focus to opening button
}
```

**Focus trap logic** (add to global keyboard handler or panel-specific logic):
```javascript
// When user presses Tab inside open panel, cycle focus only within that panel's focusable elements
if (openPanelElement) {
  const focusableElements = openPanelElement.querySelectorAll(
    'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
  );
  const firstElement = focusableElements[0];
  const lastElement = focusableElements[focusableElements.length - 1];
  
  if (e.code === "Tab") {
    if (e.shiftKey && document.activeElement === firstElement) {
      e.preventDefault();
      lastElement.focus();
    } else if (!e.shiftKey && document.activeElement === lastElement) {
      e.preventDefault();
      firstElement.focus();
    }
  }
}
```

**ESC to close any open panel** (update existing Escape handler, line 2574–2575):
```javascript
case "Escape":
  if (isCountingDown) { e.preventDefault(); cancelCountdown(); }
  else if (openPanelElement === infoOverlay) { e.preventDefault(); closeInfo(); }
  else if (openPanelElement === settingsOverlay) { e.preventDefault(); closeSettings(); }
  break;
```

**Rationale:**
- D-08: On panel open, focus moves to close button (first interactive element)
- D-09: Focus trap keeps Tab cycling within panel only
- ESC closes open panel and returns focus to the button that opened it
- `openPanelElement` global tracks which panel is currently open (needed for keyboard guard in D-10)

---

### Keyboard Shortcut Guard (A11Y-01, D-10)

**Analog:** KEYBOARD SHORTCUTS section, lines 2570–2581

**Current pattern** (lines 2570–2581):
```javascript
document.addEventListener("keydown", e => {
  if (e.target.tagName === "INPUT" || e.target.tagName === "TEXTAREA") return;
  if (e.metaKey || e.ctrlKey || e.altKey) return;
  switch (e.code) {
    case "Escape":
      if (isCountingDown) { e.preventDefault(); cancelCountdown(); }
      break;
    case "Space": e.preventDefault(); toggleBtn.click(); break;
    case "KeyR":  resetBtn.click();    break;
    case "KeyF":  fullscreenBtn.click(); break;
  }
});
```

**Modified — add early guard** (D-10):
```javascript
document.addEventListener("keydown", e => {
  if (e.target.tagName === "INPUT" || e.target.tagName === "TEXTAREA") return;
  if (e.metaKey || e.ctrlKey || e.altKey) return;
  
  // D-10: Disable global shortcuts (Space, R, F) while any panel is open
  const anyPanelOpen = openPanelElement !== null;
  
  switch (e.code) {
    case "Escape":
      if (isCountingDown) { e.preventDefault(); cancelCountdown(); }
      else if (anyPanelOpen) { e.preventDefault(); /* panel close handled above */ }
      break;
    case "Space": 
      if (anyPanelOpen) return;  // ESC closes panel; Space is skipped
      e.preventDefault(); toggleBtn.click(); 
      break;
    case "KeyR":  
      if (anyPanelOpen) return;
      resetBtn.click();    
      break;
    case "KeyF":  
      if (anyPanelOpen) return;
      fullscreenBtn.click(); 
      break;
  }
});
```

**Rationale:**
- D-10: Global shortcuts (Space, R, F) disabled when any panel is open
- ESC still closes the open panel (not skipped)
- Uses `openPanelElement` global state tracking from panel focus management

---

### CSV Parser Function (HIST-12, D-12, D-13, D-14)

**Analog:** SESSION HISTORY section, lines 1826–1883 (importJson function)

**Current importJson pattern** (lines 1826–1883):
```javascript
function importJson(file) {
  if (!file) return;
  const reader = new FileReader();
  reader.onload = function() {
    try {
      let imported;
      try {
        imported = JSON.parse(reader.result);
      } catch (_) {
        showImportFeedback("Import failed: invalid file format");
        return;
      }
      if (!Array.isArray(imported)) {
        showImportFeedback("Import failed: invalid file format");
        return;
      }
      let importedCount = 0, dupCount = 0, invalidCount = 0;
      let existing = [];
      try {
        const raw = localStorage.getItem(HISTORY_KEY);
        existing = raw ? JSON.parse(raw) : [];
      } catch (_) { existing = []; }
      const existingDates = new Set(existing.map(s => s.date));
      imported.forEach(entry => {
        if (typeof entry.date !== "string" || !Number.isFinite(entry.durationMs) || !Number.isFinite(entry.cycles)) {
          invalidCount++;
          return;
        }
        if (existingDates.has(entry.date)) {
          dupCount++;
          return;
        }
        existing.push({ date: entry.date, durationMs: entry.durationMs, cycles: entry.cycles, preset: entry.preset || "relax" });
        existingDates.add(entry.date);
        importedCount++;
      });
      // ... rest of function
    } catch (_) {
      showImportFeedback("Import failed: invalid file format");
    }
  };
  reader.readAsText(file);
}
```

**New CSV parser function — add after importJson** (around line 1883):
```javascript
function importCsv(file) {
  if (!file) return;
  const reader = new FileReader();
  reader.onload = function() {
    try {
      const lines = reader.result.split('\n').map(l => l.trim()).filter(l => l);
      if (!lines.length) {
        showImportFeedback("No valid sessions found in file");
        return;
      }
      
      // D-12: Strict format validation — expect header row
      const headerLine = lines[0].toLowerCase();
      const hasExpectedHeader = 
        headerLine.includes("date") && 
        headerLine.includes("duration") && 
        headerLine.includes("cycles");
      if (!hasExpectedHeader) {
        showImportFeedback("No valid sessions found in file");
        return;
      }
      
      let importedCount = 0, skipCount = 0;
      let existing = [];
      try {
        const raw = localStorage.getItem(HISTORY_KEY);
        existing = raw ? JSON.parse(raw) : [];
      } catch (_) { existing = []; }
      const existingDates = new Set(existing.map(s => s.date));
      
      // Parse data rows (skip header)
      for (let i = 1; i < lines.length; i++) {
        try {
          const cols = lines[i].split(',').map(c => c.trim());
          if (cols.length < 3) { skipCount++; continue; }
          
          const date = cols[0];
          const durationStr = cols[1]; // M:SS format, e.g., "5:30"
          const cyclesStr = cols[2];
          
          // D-13: Parse M:SS duration back to milliseconds
          const [mins, secs] = durationStr.split(':');
          const totalSec = parseInt(mins, 10) * 60 + parseInt(secs, 10);
          const durationMs = totalSec * 1000;
          
          const cycles = parseInt(cyclesStr, 10);
          
          // Validate parsed values
          if (typeof date !== "string" || !Number.isFinite(durationMs) || !Number.isFinite(cycles)) {
            skipCount++;
            continue;
          }
          
          // D-16: Dedup — same strategy as JSON import (exact date string match)
          if (existingDates.has(date)) {
            skipCount++;
            continue;
          }
          
          // D-14: Import incomplete sessions if they parse correctly
          const incomplete = cols.length > 3 && cols[3].toLowerCase().includes("incomplete");
          
          existing.push({ 
            date: date, 
            durationMs: durationMs, 
            cycles: cycles, 
            preset: "relax",  // CSV doesn't store preset; default to relax
            incomplete: incomplete
          });
          existingDates.add(date);
          importedCount++;
        } catch (_) {
          skipCount++;
          continue;
        }
      }
      
      // D-15: Import feedback pattern (matches JSON feedback)
      if (importedCount === 0) {
        showImportFeedback("No valid sessions found in file");
        return;
      }
      
      existing.sort((a, b) => b.date.localeCompare(a.date));
      if (existing.length > maxHistoryEntries) existing.length = maxHistoryEntries;
      localStorage.setItem(HISTORY_KEY, JSON.stringify(existing));
      renderHistory();
      sessionStorage.setItem("historyPage", "1");
      
      const message = "Imported " + importedCount + " sessions" + (skipCount > 0 ? ", " + skipCount + " skipped" : "");
      showImportFeedback(message);
    } catch (_) {
      showImportFeedback("Could not read file");
    }
  };
  reader.readAsText(file);
}
```

**Rationale:**
- D-12: Strict format validation — header row must contain "date", "duration", "cycles"
- D-13: Parse M:SS format (e.g., "5:30") to milliseconds for durationMs field
- D-14: Preserve incomplete flag from CSV if present
- D-16: Dedup by exact date string match (same as JSON import)
- D-15: Flash feedback pattern matches JSON import feedback
- Silent catch on parse errors — no `throw` anywhere (constraint per CLAUDE.md)

---

### CSV Import Handler (HIST-12, D-11)

**Analog:** HISTORY ACTIONS section, lines 2482–2483 (import button handler)

**Current pattern** (lines 2482–2483):
```javascript
document.getElementById("importBtn").addEventListener("click", () => document.getElementById("importFileInput").click());
document.getElementById("importFileInput").addEventListener("change", function() { importJson(this.files[0]); this.value = ""; });
```

**Modified — detect file extension and route** (D-11):
```javascript
document.getElementById("importBtn").addEventListener("click", () => document.getElementById("importFileInput").click());
document.getElementById("importFileInput").addEventListener("change", function() {
  const file = this.files[0];
  if (!file) { this.value = ""; return; }
  
  // D-11: Route to correct parser based on file extension
  if (file.name.endsWith('.csv')) {
    importCsv(file);
  } else {
    importJson(file);  // default to JSON for .json or unknown extensions
  }
  this.value = "";  // reset input for next file
});
```

**Rationale:**
- D-11: Single import button, one file input, routes on `.endsWith('.csv')` check
- CSV parser added as separate function (importCsv, per above pattern)
- Resets `this.value = ""` after either handler completes (allows re-importing same file)
- Error handling delegated to CSV and JSON parser functions

---

## Shared Patterns

### Error Handling (All handlers)
**Source:** `index.html` SESSION HISTORY section (lines 1704–1713, 1826–1883)
**Apply to:** All CSV import and panel management code
```javascript
// Pattern 1: Silent try/catch with empty catch (per CLAUDE.md constraint)
try {
  // Parse or manipulate data
} catch (_) {
  // Silent — no throw, no console.log
}

// Pattern 2: Feedback flash banner (non-blocking, 3–5 second dismiss)
function showImportFeedback(message) {
  const warningEl = document.getElementById("storageWarning");
  if (!warningEl) return;
  warningEl.textContent = message;
  warningEl.style.display = "block";
  setTimeout(() => { warningEl.style.display = "none"; }, 5000);
}
```

### CSS Custom Properties (All styling)
**Source:** `index.html` `:root` CSS variables (lines 17–40)
**Apply to:** All new focus styles
```css
/* Always use var(--accent) for interactive element colors — adapts to light/dark theme */
:focus-visible {
  outline: 2px solid var(--accent);  /* Not hardcoded color */
  outline-offset: 3px;
}
```

### Panel State Tracking (All panel logic)
**Source:** Global state pattern (similar to existing `isRunning`, `isCountingDown`)
**Apply to:** Panel open/close and keyboard guard
```javascript
let openPanelElement = null;  // Track which panel is currently open (null if none)

function openInfo() { 
  infoOverlay.classList.add("visible");
  openPanelElement = infoOverlay;  // Set global state
  // ... focus management
}

function closeInfo() {
  infoOverlay.classList.remove("visible");
  openPanelElement = null;  // Clear global state
  // ... return focus
}
```

---

## Implementation Checklist

### CSS Changes
- [ ] Add `:focus-visible` rule with `outline: 2px solid var(--accent); outline-offset: 3px;` (line ~1125)
- [ ] Verify contrast: 3:1 minimum on both light and dark themes (per D-01)

### HTML Changes
- [ ] Add `aria-live="polite"` to `#phaseEl` (line 1264)
- [ ] Change file input `accept=".json"` to `accept=".json,.csv"` (line 1318)

### JavaScript Changes
- [ ] Add global `openPanelElement = null;` state variable (top of script, around line 1476)
- [ ] Modify `openInfo()` / `closeInfo()` to manage focus and set `openPanelElement` (lines 2516–2523)
- [ ] Modify `openSettings()` / `closeSettings()` similarly (lines 2532–2539)
- [ ] Add focus trap logic to keyboard handler (Tab cycling within open panel)
- [ ] Add ESC close handler for open panels (update Escape case, line 2574)
- [ ] Add `anyPanelOpen` guard to Space/R/F shortcuts (lines 2577–2579)
- [ ] Add `importCsv()` function after `importJson()` (after line 1883)
- [ ] Modify import file input change handler to detect `.csv` and route (lines 2482–2483)

### Testing Points
- Focus outline appears on Tab navigation (not on mouse click) — verify `:focus-visible` vs `:focus`
- Phase label changes announced by screen reader — test with screen reader
- Panel open moves focus to close button; Tab cycles within panel only
- ESC closes open panel and returns focus to opener
- Global shortcuts (Space, R, F) disabled when panel open
- CSV import accepts correct format; rejects invalid files
- CSV dedup by exact date string match; duplicate sessions skipped
- Import feedback banner shows correct counts (e.g., "Imported 5 sessions, 2 skipped")

---

## No Analog Found

All patterns found existing analogs in the codebase. Phase 8 modifies index.html only (no new files).

---

## Metadata

**Analog search scope:** `index.html` sections: CSS (lines 16–1125), HTML (lines 1129–1330), JavaScript (lines 1341–2612)
**Key sections analyzed:** CONFIG, STATE, DOM, PERSISTENCE, SESSION HISTORY, EXPORT/IMPORT, RENDER, KEYBOARD SHORTCUTS, PANELS, CONTROLS
**Pattern extraction date:** 2026-07-28
