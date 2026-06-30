# Phase 5: History Data Management - Context

**Gathered:** 2026-06-30
**Status:** Ready for planning

<domain>
## Phase Boundary

Deliver full user control over session data: export the history as JSON or CSV file downloads, import a JSON backup (merging with existing history, skipping exact-timestamp duplicates), and clear all history with a native `<dialog>` confirmation using destructive-action language. All operations work entirely offline using the File API and FileReader API.

</domain>

<decisions>
## Implementation Decisions

### Button Placement

- **D-01:** Export JSON, Export CSV, and Import JSON buttons live in the **history panel** (`.history <details>` section), as a compact row of small buttons **above the session list** (between the `<summary>` and `#historyList`).
- **D-02:** A hidden `<input type="file" accept=".json">` for import lives **inline in the history panel**, immediately adjacent to the Import button in the DOM — triggered by the button's click event.
- **D-03:** The existing **Clear History button stays standalone in the settings panel** — no grouping, no section header, no restructuring. Minimal change to existing settings layout.

### Import Feedback

- **D-04:** Import **failure** (malformed JSON or schema mismatch for all entries) → inline 5-second flash on the existing **`#storageWarning` element**: `"Import failed: invalid file format"`.
- **D-05:** Import **success** → inline 5-second flash on `#storageWarning`: `"Imported X sessions (Y duplicates skipped)"`. Both counts always shown even when Y=0.
- **D-06:** **Partial failure** (valid JSON but some entries missing required fields) → skip invalid entries, import valid ones. Flash includes the skipped count: `"Imported X sessions (Y invalid entries skipped)"`.
- **D-07:** All feedback reuses the **existing `#storageWarning` element** — no new DOM element needed.

### CSV Export Format

- **D-08:** The duration column uses **human-readable `M:SS` format** (e.g., `"5:32"`) — matches what the history panel already displays. Header: `duration`.
- **D-09:** Column order and headers: **`date, duration, cycles, preset`** — matches the reading order in the history panel.
- **D-10:** The `date` column uses the **full ISO timestamp** (e.g., `"2026-06-30T11:45:38.188Z"`) — preserves full precision, matches the JSON format.
- **D-11:** Filenames are **date-stamped** using today's date: `mb-sessions-YYYY-MM-DD.json` and `mb-sessions-YYYY-MM-DD.csv`. Date extracted from `new Date()` at export time.

### Duplicate Detection & Merge Logic

- **D-12:** Duplicate detection uses **exact ISO string match** (`s1.date === s2.date`) — two sessions on the same calendar day but different times are BOTH kept. Safest for data fidelity.
- **D-13:** After merge, **re-sort the combined array by date descending** (newest first: `b.date.localeCompare(a.date)`) — consistent with how new sessions are always added at the front via `list.unshift(entry)`.
- **D-14:** **Enforce `maxHistoryEntries` cap after import merge** — trim to cap after re-sorting. Consistent behavior: cap is always respected regardless of source.

### Confirmation Dialog for Clear History (from prior locked decisions)

- **D-15 (locked):** Clear History uses a **native `<dialog>` element** — provides free focus trapping and ESC handling. Dialog content must use destructive-action language (e.g., button label: `"Delete all sessions"`). Accidental dismissal (ESC or clicking outside) does NOT delete data.

### Export Technique (from prior locked decisions)

- **D-16 (locked):** Export uses **Blob + `URL.createObjectURL` + `<a download>`** — no server required, works fully offline.
- **D-17 (locked):** Import uses **FileReader API** — `readAsText()` followed by `JSON.parse()`, then schema validation, then atomic write (read → merge → write once to localStorage).

### Claude's Discretion

- Exact button styling for Export/Import row — follow the existing preset button CSS pattern (`.presetBtn` or similar small button style); exact sizing left to implementer.
- Exact `<dialog>` styling — implementer styles it consistently with the app's info/settings panel aesthetic.
- JSON validation schema specifics — a session entry is valid if it has `date` (string), `durationMs` (finite number), `cycles` (finite number). `preset` may be optional (default `"relax"` on missing).
- Whether to reset pagination (`historyPage` in sessionStorage) to page 1 after a successful import — implementer's call.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Requirements & Roadmap

- `.planning/ROADMAP.md` §"Phase 5: History Data Management" — Goal, success criteria (SC-1 through SC-4), requirements (HIST-08, HIST-09, HIST-10, HIST-11)
- `.planning/REQUIREMENTS.md` §"History Data Management" — exact acceptance conditions for HIST-08 through HIST-11

### Codebase

- `index.html` lines ~1562–1639 — `saveHistory()` and `renderHistory()` functions; session data format `{ date, durationMs, cycles, preset }`; `HISTORY_KEY = "mb_history"` storage key
- `index.html` lines ~1201–1214 — `.history <details>` section HTML where Export/Import button row will be inserted above `#historyList`
- `index.html` lines ~2218–2225 — existing `clearHistBtn` click handler (settings panel) — reference for existing clear pattern; confirmation dialog replaces the current no-confirm delete
- `index.html` lines ~1571–1579 — `#storageWarning` element flash pattern (5s non-blocking message) — reuse for import feedback
- `index.html` lines ~1282–1295 — `SESSION` frozen constant and `HISTORY_KEY` / `maxHistoryEntries` variable — cap enforcement reference

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets

- **`#storageWarning` element + flash pattern** (lines ~1571–1579): already implements a 5s non-blocking inline message. Reuse for import success/failure feedback — just update `textContent` and call the same show/hide pattern.
- **`saveHistory(entry)` function** (line ~1564): writes to `HISTORY_KEY` with cap enforcement. The import function will bypass this (needs bulk write) but should replicate the QuotaExceededError catch pattern.
- **`renderHistory()` function** (line ~1583): already reads from `HISTORY_KEY` and re-renders the panel. Call after import to refresh the list.
- **`formatDuration(ms)`**: already formats milliseconds to `M:SS` — reuse for CSV duration column.
- **`MODE_LABELS`**: maps preset keys (`relax`, `box`, `478`) to display names — available but not needed for CSV (CSV exports the key, not display name).

### Established Patterns

- **Blob + `<a download>` export**: standard File API pattern — `URL.createObjectURL(new Blob([content], {type}))`, set `<a>.href` + `.download`, programmatically click, then `URL.revokeObjectURL()`.
- **FileReader import**: `reader.readAsText(file)`, then `reader.onload` callback, `JSON.parse(reader.result)`, schema validate each entry.
- **try/catch for localStorage ops**: all localStorage reads/writes wrapped in try/catch with silent failure. Import should follow the same pattern.
- **`Object.freeze()` for config**: any new constants (e.g., export MIME types) should be frozen.
- **`const` over `let`**: use `const` for all new variables unless reassignment is required.

### Integration Points

- **History panel HTML** (`.history <details>`): new button row inserted between `<summary>` and `#historyList` — also add hidden `<input type="file" id="importFileInput" accept=".json" style="display:none">` here.
- **Settings panel** (`clearHistBtn`): replace current immediate-delete handler with one that opens the `<dialog>` confirmation first.
- **`#storageWarning` element**: existing element in the settings/UI area — repurpose as the unified import feedback channel.
- **`maxHistoryEntries`** global: referenced after import merge for cap enforcement.

</code_context>

<specifics>
## Specific Ideas

- CSV duration column: use existing `formatDuration(s.durationMs)` output directly — ensures consistency with history panel display.
- Filename date extraction: `new Date().toISOString().slice(0, 10)` gives `YYYY-MM-DD` — simple and reliable.
- Import button label: `"Import"` (short, fits in the compact row alongside `"Export JSON"` and `"Export CSV"`).
- Confirmation dialog button: `"Delete all sessions"` as the confirm button text with a danger/destructive visual style; `"Cancel"` to dismiss.
- Import flash examples: `"Imported 12 sessions"`, `"Imported 8 sessions (3 duplicates skipped)"`, `"Imported 5 sessions (2 invalid entries skipped)"`, `"Import failed: invalid file format"`.

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 5-History Data Management*
*Context gathered: 2026-06-30*
