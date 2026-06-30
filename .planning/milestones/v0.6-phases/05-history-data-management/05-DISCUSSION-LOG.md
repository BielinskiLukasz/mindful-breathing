# Phase 5: History Data Management - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-06-30
**Phase:** 5-History Data Management
**Areas discussed:** Button placement, Import feedback, CSV format & filename, Duplicate detection precision

---

## Button Placement

### Export/Import button location

| Option | Description | Selected |
|--------|-------------|----------|
| Settings panel | Group with clearHistBtn — all data management in one place | |
| History panel | Add row below session list — contextual | |
| History panel top, Clear in settings | Export/Import near the data; Clear stays with settings | ✓ |

**User's choice:** History panel top (Export/Import), Clear stays standalone in settings

---

### History panel button arrangement

| Option | Description | Selected |
|--------|-------------|----------|
| Row of small buttons at top | Compact row above session list | ✓ |
| Row of small buttons at bottom | Below paginated list, above nav | |
| You decide | Leave to implementer | |

**User's choice:** Row of small buttons at the top of the history panel, above the session list

---

### Hidden file input placement

| Option | Description | Selected |
|--------|-------------|----------|
| Inline in history panel | Hidden `<input type="file">` next to Import button, triggered by click | ✓ |
| You decide | Leave DOM placement to implementer | |

**User's choice:** Inline in history panel

---

### Clear History button grouping in settings

| Option | Description | Selected |
|--------|-------------|----------|
| Keep standalone | clearHistBtn stays as-is — no restructuring | ✓ |
| Group under a 'Data' label | Add section header above clearHistBtn | |
| You decide | Leave to implementer | |

**User's choice:** Keep standalone — minimal code change

---

## Import Feedback

### Failure feedback

| Option | Description | Selected |
|--------|-------------|----------|
| Inline flash message | 5s non-blocking flash on storageWarning: "Import failed: invalid file format" | ✓ |
| Alert overlay | Modal dialog with error detail | |
| Silent fail | Nothing shown | |

**User's choice:** Inline flash message reusing existing storageWarning element

---

### Success feedback

| Option | Description | Selected |
|--------|-------------|----------|
| Count summary flash | "Imported X sessions (Y duplicates skipped)" | ✓ |
| Silent success | History panel refreshes, no message | |
| You decide | Leave to implementer | |

**User's choice:** Count summary flash

---

### Partial schema-invalid entries

| Option | Description | Selected |
|--------|-------------|----------|
| Skip invalid, import valid | Merge valid entries; flash includes skipped count | ✓ |
| Fail entire import | Reject the whole file if any entry is invalid | |
| You decide | Leave to implementer | |

**User's choice:** Skip invalid, import valid

---

### Flash message location

| Option | Description | Selected |
|--------|-------------|----------|
| Reuse existing storageWarning element | Same 5s flash element already in the app | ✓ |
| New element in history panel | Separate element closer to Import button | |
| You decide | Leave to implementer | |

**User's choice:** Reuse #storageWarning

---

## CSV Format & Filename

### Duration column format

| Option | Description | Selected |
|--------|-------------|----------|
| Human-readable M:SS | e.g., "5:32" — matches history panel display | ✓ |
| Raw milliseconds | e.g., "332000" — precise but needs conversion | |
| Both columns | duration_ms + duration | |

**User's choice:** Human-readable M:SS

---

### Column order and headers

| Option | Description | Selected |
|--------|-------------|----------|
| date, duration, cycles, preset | Matches history panel reading order | ✓ |
| date, preset, duration, cycles | Context before stats | |
| You decide | Leave to implementer | |

**User's choice:** date, duration, cycles, preset

---

### Date column format

| Option | Description | Selected |
|--------|-------------|----------|
| Full ISO timestamp | "2026-06-30T11:45:38.188Z" — full precision | ✓ |
| Date only (YYYY-MM-DD) | "2026-06-30" — simpler, loses time | |
| You decide | Leave to implementer | |

**User's choice:** Full ISO timestamp

---

### Filename convention

| Option | Description | Selected |
|--------|-------------|----------|
| Date-stamped | mb-sessions-2026-06-30.json / .csv | ✓ |
| Static name | mindful-breathing-history.json / .csv | |
| You decide | Leave to implementer | |

**User's choice:** Date-stamped filename

---

## Duplicate Detection Precision

### Deduplication logic

| Option | Description | Selected |
|--------|-------------|----------|
| Exact ISO string match | Two sessions same day but different times: BOTH kept | ✓ |
| Calendar-date match (YYYY-MM-DD) | Any session on same calendar day = duplicate | |
| You decide | Leave to implementer | |

**User's choice:** Exact ISO string match — safest for data fidelity

---

### Post-import sort order

| Option | Description | Selected |
|--------|-------------|----------|
| Newest first, re-sorted after merge | Sort by date descending after merging | ✓ |
| Keep existing order, append new entries | No re-sort | |
| You decide | Leave to implementer | |

**User's choice:** Re-sort newest first after merge

---

### History cap enforcement after import

| Option | Description | Selected |
|--------|-------------|----------|
| Yes, enforce cap after import | Trim to maxHistoryEntries after merge | ✓ |
| No, skip cap enforcement on import | Import can exceed cap temporarily | |
| You decide | Leave to implementer | |

**User's choice:** Enforce cap — consistent behavior, cap always respected

---

## Claude's Discretion

- Exact button styling for Export/Import row (follow existing preset button CSS pattern)
- Exact `<dialog>` styling for confirmation (consistent with app's panel aesthetic)
- JSON validation schema specifics (`date` + `durationMs` + `cycles` required; `preset` optional, defaults to `"relax"`)
- Whether to reset pagination to page 1 after successful import

## Deferred Ideas

None — discussion stayed within phase scope.
