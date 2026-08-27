---
phase: quick/260827-hit
plan: 01
type: execute
wave: 1
depends_on: []
files_modified:
  - index.html
autonomous: true
requirements:
  - A11Y-statusText-aria-live

estimate:
  tokens: 5000
  raw_tokens: 3000
  tasks: 1
  confidence: high

must_haves:
  truths:
    - statusText span announces state changes to screen readers without requiring focus
    - No existing aria-live coverage is duplicated or removed
  artifacts:
    - index.html with aria-live="polite" on #statusText
  key_links:
    - "#statusText reads its own content; #announcer covers runtime phase transitions (A11Y-02)"
---

<objective>
Add aria-live="polite" to the #statusText span so screen readers announce
status changes (Ready / Running / Paused) without the user needing to focus
the element.

Purpose: Close a soft defensive A11Y gap. The #announcer element covers
runtime phase transitions (A11Y-02), but statusText's own state changes
(start/stop/reset) are silently updated and never announced.

Output: index.html with one added HTML attribute on line ~1486.

Note: WR-01 (desktop transition shorthand) and WR-02 (hover/active transform
conflict) were already resolved in commit 64b1f28 by quick task 260826-tih.
This plan addresses only the remaining item.
</objective>

<execution_context>
@C:/my-code/vibe-coding/mindful-breathing/.claude/gsd-core/workflows/execute-plan.md
@C:/my-code/vibe-coding/mindful-breathing/.claude/gsd-core/templates/summary.md
</execution_context>

<context>
@C:/my-code/vibe-coding/mindful-breathing/index.html
</context>

<tasks>

<task type="auto">
  <name>Task 1: Add aria-live="polite" to #statusText</name>
  <files>index.html</files>
  <action>
    Locate the span with id="statusText" (currently at line ~1486 inside .status div).
    The element currently reads:
      &lt;span id="statusText"&gt;Stopped&lt;/span&gt;
    Add aria-live="polite" as an attribute so screen readers announce content
    changes passively. The corrected element is:
      &lt;span id="statusText" aria-live="polite"&gt;Stopped&lt;/span&gt;

    Do not add aria-atomic or aria-relevant — defaults are correct for this element.
    Do not touch the nearby #announcer element (already has aria-live="polite"
    aria-atomic="true") or the #phase element (already has aria-live="polite").
    No other changes to index.html are required for this task.
  </action>
  <verify>
    <automated>grep -c 'id="statusText" aria-live="polite"' C:/my-code/vibe-coding/mindful-breathing/index.html</automated>
  </verify>
  <done>
    grep returns 1, confirming the single #statusText span carries aria-live="polite".
    No other aria-live attributes were added or removed in the file.
  </done>
</task>

</tasks>

<threat_model>
## Trust Boundaries

| Boundary | Description |
|----------|-------------|
| HTML attribute | Static markup only — no runtime data or user input |

## STRIDE Threat Register

| Threat ID | Category | Component | Severity | Disposition | Mitigation |
|-----------|----------|-----------|----------|-------------|------------|
| T-260827-01 | Information Disclosure | aria-live on statusText | low | accept | Status text values (Ready/Running/Paused) are non-sensitive UI strings from frozen UI config; no user data is exposed |
</threat_model>

<verification>
1. Open index.html in a browser and use a screen reader (NVDA, VoiceOver, or axe DevTools).
2. Start and stop a session — statusText content changes from "Ready" to "Running" to "Paused".
3. Screen reader announces each change without requiring focus on the element.
4. Automated: grep returns exactly 1 match for `id="statusText" aria-live="polite"`.
</verification>

<success_criteria>
- #statusText has aria-live="polite" in source
- No regression to #announcer or #phase aria attributes
- grep gate passes: count == 1
</success_criteria>

<output>
Create `.planning/quick/260827-hit-fix-three-css-a11y-tech-debt-items-wr-01/260827-hit-01-SUMMARY.md` when done
</output>
