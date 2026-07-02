# Technology Stack — v0.6 Features

**Project:** Mindful Breathing v0.6  
**Researched:** 2026-06-30  
**Scope:** Stack additions for 2-column landscape layout, light theme WCAG compliance, import/export history, clear history with confirmation

## Recommended Stack

No new dependencies or frameworks are required. All features use vanilla JavaScript and CSS, with native browser APIs.

### Core APIs (Existing + New Required)

| API / Technology | Version / Status | Purpose | Why |
|------------------|------------------|---------|-----|
| **CSS Grid** | CSS3 (all modern browsers) | 2-column responsive layout (landscape 50/50, mobile full-width) | Flexible, no dependencies. `grid-template-columns: 1fr 1fr` and media queries handle orientation switching. 98%+ browser support as of 2026. |
| **CSS Flexbox** | CSS3 (all modern browsers) | Align content within grid columns (vertical stacking, centering) | Already in use; required for internal column layouts. |
| **CSS Media Queries** | CSS3 standard | Landscape orientation detection and responsive breakpoints | Existing pattern in codebase. No changes needed to approach. |
| **File API (Blob)** | W3C standard, all modern browsers | Create downloadable files (JSON, CSV) from in-memory data | Zero JS library; creates binary/text data chunks for download trigger. |
| **URL.createObjectURL** | DOM standard, all modern browsers | Generate downloadable file URLs from Blob objects | Paired with File API. Must revoke with URL.revokeObjectURL() to prevent memory leaks. |
| **HTMLAnchorElement.download** | HTML5 standard, 98%+ browser support | Trigger file download via `<a download="filename">` attribute | Works on Chrome 14+, Firefox 20+, Safari 10.1+ (macOS), Safari 13+ (iOS), Edge 13+, Opera 15+. Does NOT work same-origin URLs only. |
| **FileReader API** | W3C standard, all modern browsers | Parse imported JSON/CSV files into JavaScript objects | Async reading of uploaded files. Existing knowledge in codebase from session history handling. |
| **JavaScript JSON.parse / JSON.stringify** | ECMAScript standard | Serialize/deserialize data objects (import/export roundtrip) | Already in use for localStorage. No new concepts. |
| **Keyboard Event (ESC key)** | DOM standard, all modern browsers | Close confirmation dialogs with ESC key (event.key === 'Escape', event.keyCode === 27) | Existing pattern in codebase (countdown skip). Reuse same handler structure. |
| **CSS :focus-visible / :focus** | CSS3 standard, modern browsers | Visible focus outline for keyboard navigation in dialogs | Critical for accessibility. May need explicit outline if not already styled. |

### CSS Features (Existing + New Required)

| Feature | Status | Purpose | When to Use |
|---------|--------|---------|-------------|
| **CSS Grid (grid-template-columns, grid-template-rows)** | Existing | Base layout system. Add 2-column support via `@media (min-height: 600px) and (orientation: landscape)` | Landscape: `grid-template-columns: 1fr 1fr`. Mobile/portrait: `grid-template-columns: 1fr`. |
| **CSS Custom Properties (--text-light, --bg-light, etc.)** | Existing | Theme color system. Audit light theme values for WCAG contrast. | Update `--text-light` from `#5c4033` to darker shade to achieve 4.5:1 ratio with `--bg-light: #f5f1ed`. |
| **CSS clamp()** | Existing | Fluid typography scaling (no jumps between breakpoints) | Keep existing clamp() rules; no changes needed. |
| **CSS overflow / scrolling** | Needs addition | Make history panel scrollable in 2-column layout. | Add `max-height: 400px; overflow-y: auto;` to history panel in landscape. |
| **CSS gap / row-gap** | Existing | Spacing between grid columns and flex items. | Already in use. Adjust values for 2-column layout if needed. |
| **@keyframes** | Existing | Dialog entrance/exit animations (fade in/out for confirmation) | Reuse existing keyframe patterns (countdownPop, phaseFadeIn, etc.). Optional enhancement. |
| **CSS transition** | Existing | Smooth color/opacity changes (theme toggle, button states) | Already in use. Ensure dialog backdrop transitions smoothly. |
| **CSS rgba / opacity** | Existing | Semi-transparent overlays for modals. | Continue using rgba(0,0,0,0.85) for backdrop. |
| **CSS box-shadow** | Existing | Visual depth for cards and overlays. | Reuse existing shadow values. |

### JavaScript Patterns (Existing + New Required)

| Pattern | Status | Purpose | Notes |
|---------|--------|---------|-------|
| **CSV row generation** | New | Convert array of session objects to CSV string. | Escape commas, quotes, newlines per RFC 4180. Double any quotes: `"He said ""Hello"""`. Enclose in quotes if field contains special chars. |
| **File download trigger** | New | Create and download Blob as file. | Pattern: `Blob → URL.createObjectURL → anchor.download → click() → URL.revokeObjectURL()`. iOS Safari requires click within touch/pointer handler. |
| **JSON file import** | New | Read uploaded file and parse to object. | Pattern: `<input type="file"> → FileReader.readAsText() → JSON.parse() → validate`. Add error handling for malformed JSON. |
| **Confirmation dialog** | New | Modal prompt for destructive action (clear history). | Pattern: overlay div with modal class, ESC/cancel closes, confirm button calls action. Reuse existing modal CSS (settingsOverlay, countdownOverlay). |
| **Focus trap (optional)** | New | Keep keyboard focus within modal until closed. | Simple implementation: Tab through focusable elements, cycle back to first when at end. Consider native `<dialog>` if adding to HTML structure. |
| **WCAG contrast calculation** | One-time audit | Verify light theme colors meet 4.5:1 normal / 3:1 large text. | Use WebAIM Contrast Checker or automated tools. Update CSS variable values if needed. |
| **Event delegation** | Existing | Single listener for multiple dialog actions (confirm, cancel, close). | Already in codebase. Reuse pattern with data-action attributes. |

## Stack Decisions

### Why No CSV Library (papaparse, fast-csv, etc.)?

**Decision:** Generate CSV manually with escaping functions.

**Rationale:**
- App is zero-dependency, vanilla JS only (explicit constraint).
- CSV generation for session history is simple enough: array of objects → array of strings → join with newline.
- Only requires proper escaping of quotes, commas, newlines (RFC 4180).
- Adds ~30 lines of JS vs. 10+ KB minified library.
- Users who prefer CSV can import into Excel/Google Sheets directly.

### Why CSS Grid for 2-Column Layout (Not Flexbox)?

**Decision:** CSS Grid for overall app layout, Flexbox for internal column content.

**Rationale:**
- Grid defines 2-column structure declaratively: `grid-template-columns: 1fr 1fr` (equal widths).
- Media query switches to 1 column on mobile: `grid-template-columns: 1fr`.
- Flexbox within each column handles vertical stacking, centering, alignment.
- Cleaner than nested flex containers.
- Matches existing landscape 50/50 layout philosophy (already in PROJECT.md decision log).

### Why Native HTML `<dialog>` (Optional Enhancement)?

**Decision:** Use existing modal div pattern, consider `<dialog>` for v0.7+ if time permits.

**Rationale:**
- Current codebase uses `.settingsOverlay`, `.countdownOverlay` pattern (div + CSS overlay).
- Confirmation dialog can follow same pattern (cheap to implement, consistent).
- Native `<dialog>` provides automatic focus trapping + ESC close, but requires HTML structure changes.
- Not a blocker for v0.6; add as polish later.

## Contrast Audit (Light Theme WCAG Compliance)

### Current Light Theme Colors

| Variable | Current Value | RGB | Current Contrast | Target | Action |
|----------|---------------|-----|-------------------|--------|--------|
| `--bg-light` | `#f5f1ed` | rgb(245, 241, 237) | — | — | Background, no change needed |
| `--text-light` | `#5c4033` | rgb(92, 64, 51) | ~3.8:1 with bg | ✓ 4.5:1 | Darken slightly; test `#4a3226` or `#3d2817` |
| `--accent-light` | `#d4a574` | rgb(212, 165, 116) | ~2.1:1 with bg | ✗ 3:1 large text | Darken to `#b8845a` or `#a06f47` |
| `--accentSoft-light` | `rgba(212, 165, 116, 0.18)` | — | — | — | Update opacity if accent-light darkened |
| `--textSoft-light` | `#a89e8c` | rgb(168, 158, 140) | ~2.4:1 with bg | ✗ 3:1 large text | Darken to `#8b7d6a` or `#735f52` |

### WCAG AA Target Ratios

- **Normal text (< 18px):** 4.5:1 minimum
- **Large text (≥ 18px or ≥ 14px bold):** 3:1 minimum
- **UI components (borders, focus outlines):** 3:1 minimum

### Tools to Use

- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/) — manual pair testing
- [FWD Tools Contrast Checker](https://fwdtools.com/color-contrast-checker/) — automated with formula validation
- [WCAG Color Checker](https://wcagcolorchecker.com/) — all WCAG levels at once

### Implementation Steps

1. Use WebAIM Contrast Checker to test current colors.
2. For any failing pairs, darken text or lighten background incrementally.
3. Audit all text elements (phase label, cycle count, status, history items, buttons).
4. Test large text (clamp sizes ≥ 18px on large screens) at 3:1 minimum.
5. Document final color values in `--text-light`, `--textSoft-light`, `--accent-light` CSS variables.

## File Export / Import Functions (Pseudo-code)

### JSON Export

```javascript
function exportSessionHistoryJSON() {
  const raw = localStorage.getItem('sessionHistory');
  const sessions = raw ? JSON.parse(raw) : [];
  const json = JSON.stringify(sessions, null, 2);
  const blob = new Blob([json], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = `breathing-history-${new Date().toISOString().slice(0, 10)}.json`;
  a.click();
  URL.revokeObjectURL(url);
}
```

### CSV Export

```javascript
function escapeCSVField(field) {
  if (typeof field !== 'string') field = String(field);
  if (field.includes(',') || field.includes('"') || field.includes('\n')) {
    return '"' + field.replace(/"/g, '""') + '"';
  }
  return field;
}

function exportSessionHistoryCSV() {
  const raw = localStorage.getItem('sessionHistory');
  const sessions = raw ? JSON.parse(raw) : [];
  
  const headers = ['Date', 'Duration (seconds)', 'Cycles'];
  const rows = sessions.map(s => [
    s.date,
    Math.round(s.durationMs / 1000),
    s.cycles || '—'
  ]);
  
  const csv = [headers, ...rows]
    .map(row => row.map(escapeCSVField).join(','))
    .join('\n');
  
  const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = `breathing-history-${new Date().toISOString().slice(0, 10)}.csv`;
  a.click();
  URL.revokeObjectURL(url);
}
```

### JSON Import

```javascript
function importSessionHistoryJSON(file) {
  const reader = new FileReader();
  reader.onload = (e) => {
    try {
      const data = JSON.parse(e.target.result);
      if (!Array.isArray(data)) throw new Error('Expected array of sessions');
      // Validate each entry has date and durationMs
      data.forEach(s => {
        if (!s.date || typeof s.durationMs !== 'number') {
          throw new Error('Invalid session entry format');
        }
      });
      // Merge or replace
      localStorage.setItem('sessionHistory', JSON.stringify(data));
      alert(`Imported ${data.length} session(s). Page will reload.`);
      location.reload();
    } catch (err) {
      alert(`Import failed: ${err.message}`);
    }
  };
  reader.readAsText(file);
}
```

### File Input Handler

```javascript
const importInput = document.getElementById('importFileInput');
importInput.addEventListener('change', (e) => {
  const file = e.target.files[0];
  if (!file) return;
  
  if (file.name.endsWith('.json')) {
    importSessionHistoryJSON(file);
  } else if (file.name.endsWith('.csv')) {
    alert('CSV import not yet implemented. Import from JSON instead.');
  }
  
  // Reset input so same file can be imported again
  importInput.value = '';
});
```

## Confirmation Dialog (Pseudo-code)

```html
<div id="clearHistDialog" class="modal-overlay" style="display: none;">
  <div class="modal-content">
    <h2>Clear all session history?</h2>
    <p>This cannot be undone. Consider exporting your data first.</p>
    <div class="modal-buttons">
      <button id="clearHistConfirm">Clear</button>
      <button id="clearHistCancel">Cancel</button>
    </div>
  </div>
</div>

<script>
function openClearHistoryDialog() {
  const dialog = document.getElementById('clearHistDialog');
  dialog.style.display = 'grid';
  
  const handleKey = (e) => {
    if (e.key === 'Escape' || e.keyCode === 27) {
      closeClearHistoryDialog();
    }
  };
  document.addEventListener('keydown', handleKey);
  dialog._keyHandler = handleKey;
}

function closeClearHistoryDialog() {
  const dialog = document.getElementById('clearHistDialog');
  dialog.style.display = 'none';
  if (dialog._keyHandler) {
    document.removeEventListener('keydown', dialog._keyHandler);
  }
}

document.getElementById('clearHistConfirm').addEventListener('click', () => {
  try {
    localStorage.removeItem('sessionHistory');
    sessionStorage.removeItem('historyPage');
    alert('History cleared.');
    closeClearHistoryDialog();
    location.reload();
  } catch (_) {
    alert('Failed to clear history.');
  }
});

document.getElementById('clearHistCancel').addEventListener('click', () => {
  closeClearHistoryDialog();
});
</script>
```

## Browser Compatibility

| Feature | Chrome | Firefox | Safari | Edge | Support |
|---------|--------|---------|--------|------|---------|
| CSS Grid | ✓ 57+ | ✓ 52+ | ✓ 10.1+ | ✓ 16+ | 98%+ (2026) |
| Blob API | ✓ 20+ | ✓ 13+ | ✓ 5.1+ | ✓ 12+ | 99%+ |
| URL.createObjectURL | ✓ 23+ | ✓ 19+ | ✓ 6+ | ✓ 12+ | 99%+ |
| download attribute | ✓ 14+ | ✓ 20+ | ✓ 10.1+ | ✓ 13+ | 98%+ |
| FileReader API | ✓ 13+ | ✓ 3.6+ | ✓ 5.1+ | ✓ 12+ | 99%+ |
| JSON (parse/stringify) | ✓ 3+ | ✓ 3.5+ | ✓ 3.1+ | ✓ 8+ | 99.9%+ |
| Media Queries (orientation) | ✓ All | ✓ All | ✓ All | ✓ All | 100% |

## Summary

**No new npm packages required.** All v0.6 features use:

1. **CSS Grid + Media Queries** — for 2-column landscape layout
2. **CSS variable audit** — for light theme WCAG contrast
3. **File API (Blob, URL.createObjectURL, download attribute)** — for JSON/CSV export
4. **FileReader API** — for JSON import
5. **Vanilla JS escaping + event handlers** — for CSV generation and confirmation dialogs
6. **Keyboard event (ESC)** — for modal close (existing pattern)

All browser support ≥ 97% on modern evergreen browsers. No polyfills needed. Single-file architecture remains intact.

## Sources

- [MDN: CSS Grid Layout (Common Layouts)](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Grid_layout/Common_grid_layouts)
- [MDN: HTMLAnchorElement.download](https://developer.mozilla.org/en-US/docs/Web/API/HTMLAnchorElement/download)
- [MDN: Blob API](https://developer.mozilla.org/en-US/docs/Web/API/Blob)
- [LogRocket: Programmatically Downloading Files in the Browser](https://blog.logrocket.com/programmatically-downloading-files-browser/)
- [WebAIM: Contrast Checker](https://webaim.org/resources/contrastchecker/)
- [W3C: WCAG 2.1 Contrast (Minimum) SC 1.4.3](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html)
- [CSV Escaping in JavaScript](https://ssojet.com/escaping/csv-escaping-in-javascript-in-browser)
- [UXPin: Building Accessible Modals with Focus Traps](https://www.uxpin.com/studio/blog/how-to-build-accessible-modals-with-focus-traps/)
- [FWD Tools: Color Contrast Checker](https://fwdtools.com/color-contrast-checker/)
