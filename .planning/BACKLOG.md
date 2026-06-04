# Backlog

## Feature Requests for v0.5+

### 1. Import/Export History
- Allow users to export their session history to a file format (JSON or CSV)
- Allow users to import previously exported history
- Useful for backup, data portability, and cross-device sync

### 2. Log Unfinished Sessions
- Track incomplete sessions (sessions that were stopped before completion)
- Store: cycle count reached, seconds elapsed
- Display in history alongside completed sessions
- Helps users track partial progress and trends

### 3. Safe Clean Button with Confirmation
- Add a "Clear History" button in settings panel
- Display confirmation popup before clearing
- Prevent accidental data loss
- Show warning about irreversibility

### 4. Enhance History Data
Current history: `date`, `cycles`, `durationMs`

Add:
- `mode` — which preset was used (Relax / Box / 4-7-8)
- Phase-level timing — duration for each phase (Inhale, Hold, Exhale, Hold2) per cycle
- Optional: notes or tags per session

### 5. Version Number in Info Popup
- Display app version in bottom of info panel
- Format: "v0.4.0" or similar
- Update on each release
- Helps users verify they have the latest version
