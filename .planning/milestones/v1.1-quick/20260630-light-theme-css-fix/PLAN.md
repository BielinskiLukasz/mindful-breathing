---
quick_id: 20260630-light-theme-css-fix
type: quick
date: 2026-06-30
files_modified:
  - index.html
---

<objective>
Fix light-theme CSS contrast by replacing hardcoded rgba(255,255,255,...) dark-only values with CSS variables across all affected rules in index.html. Also fix button size inconsistency (iconToggle 32px → 34px) and add --cardHover theme variable for hover states.

Root cause: Phase 4 executor correctly updated LIGHT_THEME JS values and applyThemeColors(), but the CSS still contains hardcoded dark-mode-only colors (rgba(255,255,255,...)) that are invisible on the cream light-theme background.
</objective>

<tasks>

<task type="auto">
  <name>Task 1: Add --cardHover CSS variable to theme system</name>
  <files>index.html</files>
  <action>
    Three edits:

    EDIT A — :root CSS block: add `--cardHover: #2e2e2e;` after the existing `--textSoft-light: #6b6058;` line.

    EDIT B — DARK_THEME object (after bg, card, accent, accentSoft, text, textSoft): add `cardHover: "#2e2e2e",` after the `accentSoft` line.

    EDIT C — LIGHT_THEME object: add `cardHover: "#dedad4",` after `accentSoft` line (slightly darker than card #ebe7e1 for hover effect).

    EDIT D — applyThemeColors() function: add `root.style.setProperty("--cardHover", theme.cardHover || "#2e2e2e");` after the `--textSoft` setProperty line.
  </action>
</task>

<task type="auto">
  <name>Task 2: Fix iconToggle size and all hardcoded dark-only CSS colors</name>
  <files>index.html</files>
  <action>
    Make the following CSS replacements:

    1. `.cycle { color: #c7c7c7 }` → `color: var(--textSoft);`

    2. `.modeIndicator { color: rgba(255,255,255,0.35) }` → `color: var(--textSoft);`

    3. `.subHint { color: rgba(255,255,255,0.55) }` → `color: var(--textSoft);`

    4. `.presetBtn { background: #222; color: rgba(255,255,255,0.55); border: 1px solid rgba(255,255,255,0.15) }` →
       `background: var(--card); color: var(--textSoft); border: 1px solid var(--accentSoft);`

    5. `.presetBtn:hover { background: #2e2e2e; color: rgba(255,255,255,0.85) }` →
       `background: var(--cardHover); color: var(--text);`

    6. `button { background: #2b2b2b; color: #fff }` →
       `background: var(--card); color: var(--text);`

    7. `button:hover { background: #383838 }` → `background: var(--cardHover);`

    8. `button:active { background: rgba(255,255,255,0.1) }` → `background: var(--accentSoft);`

    9. `.container:not(.running) #toggleBtn { background: rgba(0,0,0,0.2) }` →
       `background: var(--accentSoft);`

    10. `.container:not(.running) #toggleBtn:hover { background: rgba(255,255,255,0.06) }` →
        `background: var(--cardHover);`

    11. `.resetBtn { color: rgba(255,255,255,0.5); border-color: rgba(255,255,255,0.12) }` →
        `color: var(--textSoft); border-color: var(--accentSoft);`

    12. `.resetBtn:hover { color: rgba(255,255,255,0.8); border-color: rgba(255,255,255,0.25) }` →
        `color: var(--text); border-color: var(--accent);`

    13. `.countdownSkipBtn { border: 1px solid rgba(255,255,255,0.35); color: rgba(255,255,255,0.65) }` →
        `border: 1px solid var(--accentSoft); color: var(--textSoft);`

    14. `.countdownSkipBtn:hover { color: #fff; border-color: rgba(255,255,255,0.7) }` →
        `color: var(--text); border-color: var(--accent);`

    15. `.doneTime { color: rgba(255,255,255,0.6) }` → `color: var(--textSoft);`

    16. `.infoBtn { border: 1px solid rgba(255,255,255,0.12); color: rgba(255,255,255,0.4) }` →
        `border: 1px solid var(--accentSoft); color: var(--textSoft);`

    17. `.infoBtn:hover { background: rgba(255,255,255,0.06); color: rgba(255,255,255,0.8); border-color: rgba(255,255,255,0.25) }` →
        `background: var(--accentSoft); color: var(--text); border-color: var(--accent);`

    18. `.fullscreenBtn { border: 1px solid rgba(255,255,255,0.12); color: rgba(255,255,255,0.4) }` →
        `border: 1px solid var(--accentSoft); color: var(--textSoft);`

    19. `.fullscreenBtn:hover { background: rgba(255,255,255,0.06); color: rgba(255,255,255,0.8); border-color: rgba(255,255,255,0.25) }` →
        `background: var(--accentSoft); color: var(--text); border-color: var(--accent);`

    20. `.iconToggle { width: 32px; height: 32px; border: 1px solid rgba(255,255,255,0.12); color: rgba(255,255,255,0.65) }` →
        `width: 34px; height: 34px; border: 1px solid var(--accentSoft); color: var(--textSoft);`

    21. `.iconToggle:hover { background: rgba(255,255,255,0.06); color: rgba(255,255,255,0.9) }` →
        `background: var(--accentSoft); color: var(--text);`

    22. `.iconToggle.muted { color: rgba(255,255,255,0.2); border-color: rgba(255,255,255,0.06) }` →
        `color: var(--textSoft); border-color: var(--accentSoft); opacity: 0.45;`
  </action>
  <verify>
    grep -c 'rgba(255,255,255' index.html
    Expected: near 0 (only legitimate uses like box-shadow and the running state red button remain, not color/border values)
    grep -c 'var(--textSoft)' index.html
    Expected: higher count showing variables are in use
  </verify>
</task>

</tasks>

<output>
Create `.planning/quick/20260630-light-theme-css-fix/SUMMARY.md` when done.
</output>
