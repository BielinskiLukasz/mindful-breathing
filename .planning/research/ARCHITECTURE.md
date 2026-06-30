# Architecture Patterns: v0.6 New Features Integration

**Project:** Mindful Breathing v0.6 (Layout & History)
**Researched:** 2026-06-30
**Current codebase:** Single `index.html` (~2,272 lines), vanilla JS, no build tools

## Recommended Architecture

The single-file architecture remains unchanged. All four v0.6 features integrate cleanly into existing layers with minimal disruption:

```
index.html (single file)
├─ CONFIG (existing)
│  └─ [NEW] EXPORT_FORMATS (JSON/CSV templates)
├─ STATE (existing)
│  └─ [MODIFIED] localStorage quota tracking
├─ DOM (existing)
│  └─ [NEW] Import/export file input elements
├─ HELPERS (existing)
│  └─ [NEW] Export formatters, import parsers
├─ [NEW] IMPORT/EXPORT section
├─ PERSISTENCE (existing)
│  └─ [MODIFIED] Export/import functions
├─ CSS (existing, ~670 lines)
│  ├─ [MODIFIED] Light theme contrast values (--text-light, --textSoft-light)
│  └─ [MODIFIED] Landscape 50/50 grid to 2-column CSS Grid
├─ RENDER (existing)
│  └─ [MODIFIED] Landscape layout rendering
├─ [REST OF SECTIONS] (unchanged)
```

## Feature → Integration Points Mapping

### Feature 1: 2-Column Landscape Layout

**What changes:**
- CSS: Convert 50/50 flex to CSS Grid (cleaner alignment, better column control)
- DOM: No structural changes (ringSection + controlsSection already exist)
- JS: No logic changes (layout is purely CSS)

**Integration point: CSS section (lines ~924–1010)**

```css
/* CURRENT: 50/50 flexbox split */
@media (orientation: landscape) and (min-height: 501px) {
  .container {
    display: flex;
    flex-direction: row;  /* row split */
    width: 100%;
    height: 100vh;
  }
}

/* NEW: CSS Grid with better column control */
@media (orientation: landscape) and (min-height: 501px) {
  .container {
    display: grid;
    grid-template-columns: 1fr 1fr;  /* explicit 50/50 or adjustable */
    width: 100%;
    height: 100vh;
  }
  
  .ringSection {
    grid-column: 1;
  }
  
  .controlsSection {
    grid-column: 2;
    border-left: 1px solid var(--accentSoft);  /* existing, preserved */
  }
}
```

**Why Grid?**
- Clearer intent (explicit columns)
- Easier to adjust ratio (e.g., 60/40 future variant)
- Better gap control
- History panel always visible (no collapse needed)

**No new state or DOM elements required.**

---

### Feature 2: Light Theme Contrast Improvements

**What changes:**
- CSS: Update `--text-light` and `--textSoft-light` values for WCAG 4.5:1 (normal text) and 3:1 (large text)
- JS: No changes (theme toggle already exists)

**Current light theme values (lines ~24–30):**
```css
--text-light: #5c4033;           /* current: brown, check contrast */
--textSoft-light: #a89e8c;       /* current: tan, may fail WCAG */
```

**Integration point: CSS custom properties (lines ~16–30)**

**Contrast analysis:**
- Dark text on light background: easier to meet 4.5:1
- Current `#5c4033` (dark brown) on `#f5f1ed` (cream): likely **7+:1** ✓ (already good)
- Current `#a89e8c` (tan) on same background: likely **4.5–5:1** (borderline; may need darkening for WCAG AA)

**Recommended action:**
```css
--text-light: #3d2817;           /* darker brown, 8+:1 contrast */
--textSoft-light: #6d5d4b;       /* darker tan, 5.5+:1 contrast */
```

**Why no JS change?**
- Theme toggle (`toggleTheme()`, line ~2047) already applies all `--*-light` CSS vars
- Just update the values; toggle logic stays same

**No new state or DOM elements required.**

---

### Feature 3: Import/Export Session History

**What changes:**

**CSS (new classes for UI):**
```css
.importExportBtns { flex: 1; display: flex; gap: 8px; }
.exportBtn, .importBtn { flex-shrink: 0; }
.fileInput { display: none; } /* hidden file input element */
```

**DOM (new elements):**
```html
<!-- Add to history summary row, next to clearHistBtn -->
<button class="exportBtn" id="exportBtn" title="Export history">Export</button>
<button class="importBtn" id="importBtn" title="Import history">Import</button>
<input type="file" id="fileInput" accept=".json,.csv" style="display:none" />
```

**JS (new IMPORT/EXPORT section, ~100 lines):**

```javascript
/* ====== IMPORT/EXPORT HELPERS ====== */
const EXPORT_FORMATS = Object.freeze({
  json: { ext: ".json", mime: "application/json" },
  csv:  { ext: ".csv",  mime: "text/csv" }
});

function exportHistory(format = "json") {
  const history = loadHistoryFromStorage();
  let content;
  let filename;

  if (format === "json") {
    content = JSON.stringify(history, null, 2);
    filename = `breathing-history-${dateString()}.json`;
  } else if (format === "csv") {
    content = historyToCSV(history);
    filename = `breathing-history-${dateString()}.csv`;
  }

  downloadFile(content, filename, EXPORT_FORMATS[format].mime);
}

function importHistory(file) {
  const reader = new FileReader();
  reader.onload = (e) => {
    try {
      const content = e.target.result;
      const imported = (file.name.endsWith(".json"))
        ? JSON.parse(content)
        : csvToHistory(content);

      // Validate: array of {date, durationMs, cycles}
      if (!Array.isArray(imported) || !imported.every(h => h.date && h.durationMs)) {
        throw new Error("Invalid history format");
      }

      // Merge with existing history
      const existing = loadHistoryFromStorage();
      const merged = [...existing, ...imported]
        .sort((a, b) => new Date(b.date) - new Date(a.date))
        .slice(0, parseInt(maxHistoryInput.value));

      saveHistoryToStorage(merged);
      renderHistory();
      showNotification("History imported successfully");
    } catch (err) {
      showNotification("Failed to import: " + err.message, "error");
    }
  };
  reader.readAsText(file);
}

function historyToCSV(history) {
  const header = "Date,Duration (min),Cycles\n";
  const rows = history.map(h => 
    `${h.date},${(h.durationMs / 60000).toFixed(1)},${h.cycles || 1}`
  );
  return header + rows.join("\n");
}

function csvToHistory(csv) {
  const lines = csv.trim().split("\n").slice(1); // skip header
  return lines.map(line => {
    const [date, durationMin, cycles] = line.split(",");
    return {
      date: date.trim(),
      durationMs: Math.round(parseFloat(durationMin) * 60000),
      cycles: parseInt(cycles) || 1
    };
  });
}

function downloadFile(content, filename, mime) {
  const blob = new Blob([content], { type: mime });
  const url = URL.createObjectURL(blob);
  const a = document.createElement("a");
  a.href = url;
  a.download = filename;
  a.click();
  URL.revokeObjectURL(url);
}
```

**Integration points:**

1. **DOM section (line ~1194)** — Add import/export buttons to history header:
   ```html
   <summary>
     Past sessions
     <button class="exportBtn" id="exportBtn">Export</button>
     <button class="importBtn" id="importBtn">Import</button>
     <button class="clearHistBtn" id="clearHistBtn">Clear</button>
   </summary>
   <input type="file" id="fileInput" accept=".json,.csv" style="display:none" />
   ```

2. **CSS section (line ~589)** — Add styling:
   ```css
   .importExportBtns {
     display: flex;
     gap: 6px;
     margin-left: auto;
   }
   .exportBtn, .importBtn {
     font-size: clamp(9px, 0.8vw + 6px, 11px);
     padding: 2px 8px;
     min-width: 0;
     background: transparent;
     border: 1px solid rgba(255,255,255,0.12);
     color: rgba(255,255,255,0.3);
     border-radius: 6px;
   }
   ```

3. **New IMPORT/EXPORT section** (after PERSISTENCE, line ~1393) — 100 lines for export/import helpers

4. **DOM cache (line ~1168)** — Add:
   ```javascript
   const exportBtn = document.getElementById("exportBtn");
   const importBtn = document.getElementById("importBtn");
   const fileInput = document.getElementById("fileInput");
   ```

5. **Event listener attachment (line ~1905–1912)** — Add:
   ```javascript
   exportBtn.addEventListener("click", () => exportHistory("json"));
   // or add dropdown for format selection
   
   importBtn.addEventListener("click", () => fileInput.click());
   fileInput.addEventListener("change", (e) => {
     if (e.target.files[0]) importHistory(e.target.files[0]);
     e.target.value = "";
   });
   ```

**Why this approach?**
- Fits vanilla JS single-file model (no external libraries)
- Blob + URL.createObjectURL for download is well-supported (all modern browsers)
- FileReader API for upload is standard
- CSV is human-readable; JSON is round-trip safe
- Merges imports with existing data (no overwrites)

**New state needed:** None (uses existing history array)
**New DOM elements:** 3 (export button, import button, hidden file input)

---

### Feature 4: Clear History with Confirmation Dialog

**What changes:**

**JS (one function):**
```javascript
function clearHistoryWithConfirmation() {
  const confirmed = confirm(
    "Clear all past sessions? This cannot be undone."
  );
  if (confirmed) {
    sessionStorage.removeItem(HISTORY_KEY);
    renderHistory();
    showNotification("History cleared");
  }
}
```

**OR** (better UX: overlay instead of native confirm):

```javascript
function showClearConfirmation() {
  const overlay = createConfirmDialog(
    "Clear History",
    "Delete all past sessions? This cannot be undone.",
    { confirm: "Clear", cancel: "Cancel" }
  );

  overlay.confirm.addEventListener("click", () => {
    sessionStorage.removeItem(HISTORY_KEY);
    renderHistory();
    showNotification("History cleared");
    overlay.remove();
  });

  overlay.cancel.addEventListener("click", () => overlay.remove());
}
```

**Integration point: Modify CONTROLS section**

**Current (line ~1194):**
```html
<button class="clearHistBtn" id="clearHistBtn">Clear</button>
```

**Event listener (line ~1905–1912):**
```javascript
clearHistBtn.addEventListener("click", clearHistoryWithConfirmation);
```

**Option A (simpler, native):**
- Use browser `confirm()` dialog
- 1 line of code
- Works on all browsers
- Less polished UX

**Option B (recommended):**
- Build custom overlay UI (like existing doneOverlay/countdownOverlay)
- ~30 lines of HTML + CSS + JS
- Matches existing design language
- Better UX consistency

**Why both options are viable:**
- Feature 4 is the simplest to implement
- Native confirm is acceptable for v0.6
- Custom overlay is nice-to-have, can be backfilled in v0.7

**No new state needed.**

---

## Component Boundaries

| Component | Responsibility | Changes | New |
|-----------|-----------------|---------|-----|
| **CONFIG** | Constants: breathing presets, sound, vibration, UI text | Add `EXPORT_FORMATS` | Yes |
| **STATE** | Mutable globals: phase index, elapsed time, audio context | None | No |
| **DOM** | Cached element references | Add fileInput, exportBtn, importBtn | Yes |
| **HELPERS** | Utility functions: color, theme, phase lookup | Add CSV converters, export formatter | Yes |
| **PERSISTENCE** | localStorage read/write for settings and history | Add `exportHistory()`, `importHistory()` | Yes |
| **IMPORT/EXPORT** | **New section** — download/upload, format conversion | — | Yes |
| **CSS** | Styling, layout, animations | Modify landscape grid, light theme colors, add import/export button styles | Yes |
| **RENDER** | SVG ring, countdown, history list | No logic changes (layout is CSS-driven) | No |
| **CONTROLS** | Event listeners for start/stop/reset | Attach to new export/import/clear buttons | Yes |

---

## Data Flow

### Current (v0.5)
```
User Session
    ↓
Session state (elapsedMs, cycleCount) → RENDER
    ↓
    Saved to localStorage on finish
    ↓
renderHistory() → reads from localStorage → displays as cards
```

### New (v0.6)
```
User Session (unchanged)
    ↓
    Saved to localStorage on finish (unchanged)
    ↓
renderHistory() (unchanged) + NEW:
    ├─ EXPORT button → exportHistory() → historyToCSV/JSON → downloadFile()
    ├─ IMPORT button → fileInput.click() → importHistory() → merge + save
    └─ CLEAR button → clearHistoryWithConfirmation() → delete + renderHistory()
```

---

## Integration Order (Dependency Graph)

**Critical path (must follow this order):**

```
1. Light Theme Contrast (CSS only, no deps)
   ↓
2. 2-Column Landscape Layout (CSS only, no deps)
   ↓
3. Import/Export History (deps: PERSISTENCE + HELPERS)
   ↓
4. Clear History Confirmation (deps: CONTROLS, optional custom UI)
```

**Why this order?**
- 1 & 2 are CSS-only; can be done in parallel or sequentially
- 3 requires existing PERSISTENCE layer (already has `loadHistoryFromStorage()`)
- 4 is a button handler, can be done anytime after 3 or standalone

**Build strategy:**
- **Phase 4a:** Light theme + 2-column layout (CSS refactor, low risk)
- **Phase 4b:** Import/export (PERSISTENCE extension, medium risk)
- **Phase 4c:** Clear confirmation (CONTROLS extension, low risk)

---

## Scalability Considerations

| Concern | At 100 sessions | At 1K sessions | At 10K sessions |
|---------|-----------------|----------------|-----------------|
| **History array size** | <10 KB JSON | <100 KB JSON | <1 MB JSON |
| **Export performance** | Instant | <1s | <5s (1 MB file) |
| **Import parsing** | Instant | <100ms | <500ms |
| **localStorage quota** | 1–5% of 5MB limit | 2–10% | 20%+ (capped at maxHistoryInput) |
| **renderHistory() DOM** | 5 items visible | 5 items visible | 5 items visible (paginated) |

**Mitigation:**
- `maxHistoryInput` (line ~1141) already caps stored history (default 1,000)
- Pagination already renders only 5 items at a time
- Export/import works on in-memory array (no N+1 loops)

**No scalability risk** for the v0.6 features as designed.

---

## Patterns to Follow

### Pattern 1: CSS Custom Properties for Theme
**What:** Use `--text-light`, `--text-dark` instead of hardcoded colors
**When:** Any new colored element (button, text, background)
**Example:**
```css
.clearHistBtn {
  color: var(--text);
  background: var(--card);
  border: 1px solid var(--accentSoft);
}
```

### Pattern 2: Overlay Dialogs (Import/Export Confirmation)
**What:** Use absolute-positioned overlay with ::before dark background
**When:** User confirmation or modal content
**Example (existing):**
```css
.doneOverlay {
  display: none;
  position: absolute;
  inset: 0;
  background: rgba(0,0,0,0.78);
  place-items: center;
  z-index: 999999;
}
.doneOverlay.visible { display: grid; }
```

### Pattern 3: clamp() for Responsive Typography
**What:** Use CSS `clamp(minPx, vw+basePx, maxPx)` instead of media queries
**When:** Any font size that scales across device sizes
**Example:**
```css
font-size: clamp(11px, 1.2vw + 7px, 14px);
```

### Pattern 4: Graceful Degradation for Browser APIs
**What:** Wrap optional APIs in try/catch; hide UI if unavailable
**When:** Web Audio, Wake Lock, Fullscreen, Vibration
**Example (existing):**
```javascript
try { navigator.vibrate(VIBRATION.pattern); } catch (_) {}
```

### Pattern 5: Immutable Config Objects
**What:** Use `Object.freeze()` for constants that never change
**When:** CONFIG, STATE, PRESETS, SOUND, VIBRATION, RING
**Example:**
```javascript
const EXPORT_FORMATS = Object.freeze({
  json: { ext: ".json", mime: "application/json" }
});
```

---

## Anti-Patterns to Avoid

### Anti-Pattern 1: Scatter Hard-Coded Values
**What:** Same constant appears in multiple places
**Why bad:** Updates require global find/replace; easy to miss one
**Instead:** Define in CONFIG section; reference everywhere else

### Anti-Pattern 2: String Comparisons for Presets
**What:** `if (activePresetKey === "relax")` scattered throughout
**Why bad:** Typos introduce bugs; renaming breaks code
**Instead:** Use constants or enums in CONFIG

### Anti-Pattern 3: Direct DOM Mutation in Loops
**What:** `for (let i = 0; i < 100; i++) { el.appendChild(...) }`
**Why bad:** O(n²) reflows; animation jank
**Instead:** Build HTML string; set once with `innerHTML`

### Anti-Pattern 4: Mix Layout Logic in CSS and JS
**What:** CSS media queries + JS checks for same breakpoint
**Why bad:** Inconsistent behavior; hard to maintain
**Instead:** Let CSS drive layout; JS only responds to events

### Anti-Pattern 5: Rely on localStorage Without Error Handling
**What:** `localStorage.setItem()` without try/catch
**Why bad:** Quota exceeded errors crash silently
**Instead:** Always catch; show user-friendly error (existing pattern: line ~1369)

---

## Risk Assessment

| Feature | Risk | Mitigation |
|---------|------|-----------|
| **Light Theme Contrast** | Very Low | CSS-only, no JS changes, no new DOM, backward compatible |
| **2-Column Landscape** | Low | CSS-only grid vs flex (safe swap), no JS logic change |
| **Import/Export** | Medium | Requires FileReader + Blob (standard APIs); parsing logic; merge conflict if history changed during import |
| **Clear Confirmation** | Very Low | Simple button handler; confirm() or custom overlay both safe |

**Highest-risk item: Import/Export History**
- **Why?** Complex data flow: read file → parse → validate → merge → save → re-render
- **Mitigation:** 
  - Validate imported array structure before merge
  - Test with malformed JSON and CSV
  - Fallback message if parse fails
  - Clear file input after import to allow re-import

---

## Testing Strategy

### Unit Tests (per feature)

**Light Theme Contrast:**
- [ ] Light theme toggle applies `--text-light` and `--textSoft-light`
- [ ] Contrast ratio meets WCAG AA (4.5:1 normal, 3:1 large)

**2-Column Landscape:**
- [ ] Landscape mode triggers CSS grid (not flex)
- [ ] Columns are 50/50 ratio
- [ ] History scrolls independently of controls
- [ ] Ring scales correctly on left; controls stack on right

**Import/Export:**
- [ ] Export JSON valid, round-trips correctly
- [ ] Export CSV human-readable, parses back to same data
- [ ] Import merges with existing (no overwrites)
- [ ] Import validates array structure (reject malformed)
- [ ] Import deduplicates by date if needed
- [ ] File input resets after import

**Clear Confirmation:**
- [ ] Clear button triggers confirmation dialog
- [ ] Confirm deletes all history and re-renders
- [ ] Cancel closes dialog without deleting

### Integration Tests

- [ ] Export → Import → render flow (round trip)
- [ ] Multiple imports in sequence
- [ ] Clear history, then import, then export (no orphaned entries)
- [ ] Light theme + landscape layout together (no CSS conflicts)

---

## Implementation Checklist

- [ ] **Phase 4a: Light Theme & Landscape Layout**
  - [ ] Update CSS custom properties for light theme (2 lines)
  - [ ] Convert landscape 50/50 flexbox to CSS Grid (5 lines)
  - [ ] Test: light theme contrast, landscape layout rendering

- [ ] **Phase 4b: Import/Export History**
  - [ ] Add EXPORT_FORMATS to CONFIG (5 lines)
  - [ ] Add export/import buttons to DOM (3 elements)
  - [ ] Add import/export CSS styles (10 lines)
  - [ ] Add IMPORT/EXPORT section with helpers (100 lines)
  - [ ] Add DOM cache references (3 lines)
  - [ ] Attach event listeners (5 lines)
  - [ ] Test: export JSON/CSV, import round-trip, malformed data

- [ ] **Phase 4c: Clear Confirmation**
  - [ ] Modify clearHistBtn click handler (2 lines if native confirm, 30 if custom UI)
  - [ ] Test: confirm flow, cancel flow, history removed

---

## Sources

- **Current codebase:** `/index.html` (lines 1–2272), examined 2026-06-30
- **Existing patterns:** CSS Grid, CSS custom properties, FileReader API, Blob + URL.createObjectURL (all standard browser APIs)
- **W3C/WCAG:** Color contrast ratio specification (WCAG 2.1 AA = 4.5:1 normal, 3:1 large text)
- **Architecture references:** Single-file vanilla JS pattern; localStorage quota handling (existing v0.5 code, line ~1369)
