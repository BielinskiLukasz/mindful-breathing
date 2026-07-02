# UAT Checklist — release-0-6

Manual verification items that automated (headless) testing cannot cover.

## Sound & Haptics

- [OK] Start a session → confirm audible beep on each phase transition
- [OK] On mobile, Start a session → confirm haptic feedback on phase transitions

## Browser APIs

- [OK] Start a long session on mobile → screen stays on (Wake Lock working)

## Light Mode — Body Background

- [OK] Open app in dark mode, toggle light mode while session is stopped → outer body margin outside the card should match the card's beige color (known regression: stays dark until session starts or Reset is pressed)

## Mobile Safari

- [SKIP] Toggle light mode on Safari (iOS) → verify outer body bg and panels both match the light theme (CSS custom property inheritance can differ) — iOS unavailable this session

## Landscape Layout

- [NOK] Open on a real phone in landscape (height > 500px, e.g. iPhone 14 Pro Max landscape) → verify 50/50 grid split looks correct 
    It works is width is > 500px, but should work for height before rotation
- [OK] Open on a small phone in landscape (height ≤ 500px) → verify compact scrollable layout, not the grid

## History — Import / Export / Clear

- [OK] Export JSON → file downloads, open it and confirm it contains your sessions
- [OK] Export CSV → file downloads, open in a spreadsheet and confirm columns (date, duration, cycles, preset)
- [OK] Import a valid JSON export → confirm "Imported N sessions" feedback appears
- [OK] Import a file with invalid JSON (e.g. a .txt file renamed to .json) → confirm "Import failed: invalid file format" message
- [OK] Import a file containing duplicates of existing sessions → confirm duplicates are skipped and count is shown
- [OK] Click Clear → confirm dialog opens with "Delete all sessions?" + Cancel + Delete buttons
- [OK] In the clear dialog, click outside the dialog (backdrop) → confirm dialog closes without deleting
- [OK] In the clear dialog, click Cancel → confirm sessions are still there
- [OK] In the clear dialog, click Delete all sessions → confirm history is empty

## PWA

- [OK] On Chrome/Android, visit the app → "Add to Home Screen" prompt appears or is available in browser menu
- [OK] After installing, launch from home screen → app opens in standalone mode (no browser chrome), icon looks correct
