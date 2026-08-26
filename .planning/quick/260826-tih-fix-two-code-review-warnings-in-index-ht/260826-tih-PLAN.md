---
phase: quick-260826-tih
plan: 01
type: execute
wave: 1
depends_on: []
files_modified:
  - index.html
autonomous: true
requirements:
  - WR-01
  - WR-02

estimate:
  tokens: 12000
  raw_tokens: 8000
  tasks: 1
  confidence: high

must_haves:
  truths:
    - On pointer-fine devices, theme switches and preset selection animate button backgrounds smoothly (0.2s)
    - On pointer-fine devices, clicking a button produces a scale-down press (scale 0.97), not a scale-up hover
    - The @media (hover: hover) block at line 1254 contains all five transition properties
    - The hover selector is button:hover:not(:active)
  artifacts:
    - index.html (modified hover states block at ~line 1254)
  key_links:
    - hover media query transition → base button transition (must be superset, not replacement)
    - button:hover:not(:active) selector → button:active selector (specificity conflict resolved)
---

<objective>
Fix two CSS correctness bugs introduced in the phase 07 hover-states media query.

Purpose: Pointer-fine (desktop/laptop) users currently lose background/border-color/color button transitions and get a scale-up on click instead of the intended press-down.
Output: index.html with corrected transition declaration and corrected hover selector.
</objective>

<execution_context>
@C:/my-code/vibe-coding/mindful-breathing/.claude/gsd-core/workflows/execute-plan.md
@C:/my-code/vibe-coding/mindful-breathing/.claude/gsd-core/templates/summary.md
</execution_context>

<context>
@.planning/STATE.md
@.planning/phases/07-landscape-layout-ux-polish/07-REVIEW.md
</context>

<tasks>

<task type="auto">
  <name>Task 1: Fix WR-01 and WR-02 in hover-states media query (index.html ~line 1254)</name>
  <files>index.html</files>
  <action>
In the HOVER STATES block at line 1254 of index.html, make exactly two edits:

WR-01 fix — replace the `button { transition: ... }` declaration inside the media query so it includes all five properties (merging the base rule values with the hover-only additions). The base rule at ~line 220 defines `transform 0.05s ease, background 0.2s ease, border-color 0.2s ease, color 0.2s ease`; keep those durations for the three color properties and use 120ms for filter and transform in the hover context:

  transition: background 0.2s ease, border-color 0.2s ease, color 0.2s ease, filter 120ms ease, transform 120ms ease;

WR-02 fix — change the selector `button:hover` to `button:hover:not(:active)` so the hover scale-up does not override the existing `button:active { transform: scale(0.97) }` rule when a button is being clicked.

The block after both fixes must look exactly like this (no other lines changed):

    /* ====== HOVER STATES (pointer: fine) ====== */
    @media (hover: hover) and (pointer: fine) {
      button {
        transition: background 0.2s ease, border-color 0.2s ease, color 0.2s ease, filter 120ms ease, transform 120ms ease;
      }
      button:hover:not(:active) {
        filter: brightness(1.08);
        transform: scale(1.02);
      }
    }

Do not touch any other part of the file.
  </action>
  <verify>
    <automated>grep -n "button:hover:not(:active)" index.html</automated>
    <automated>grep -n "background 0.2s ease, border-color 0.2s ease, color 0.2s ease, filter 120ms ease" index.html</automated>
  </verify>
  <done>
    - `grep "button:hover:not(:active)" index.html` returns exactly one match
    - `grep "background 0.2s ease, border-color 0.2s ease, color 0.2s ease, filter 120ms ease, transform 120ms ease" index.html` returns exactly one match inside the hover media query
    - `grep "button:hover {" index.html` inside the hover block returns no match (old selector gone)
    - No other lines in index.html are changed
  </done>
</task>

</tasks>

<threat_model>
## Trust Boundaries

| Boundary | Description |
|----------|-------------|
| CSS rule cascade | Specificity and source order determine which transition/transform wins |

## STRIDE Threat Register

| Threat ID | Category | Component | Severity | Disposition | Mitigation |
|-----------|----------|-----------|----------|-------------|------------|
| T-tih-01 | Tampering | CSS hover block | low | accept | Pure CSS edit; no user input or data boundary involved |
</threat_model>

<verification>
After editing, open index.html in a desktop browser with a mouse and verify:
1. Click a preset button — observe scale-DOWN (press) not scale-up
2. Switch theme — button backgrounds animate smoothly over ~0.2s
3. Hover over a button (without clicking) — filter brightens and button scales up slightly
</verification>

<success_criteria>
- Both grep checks from the verify block pass
- The hover media query contains exactly one `button` rule and one `button:hover:not(:active)` rule
- No unrelated lines in index.html are modified
</success_criteria>

<output>
Create `.planning/quick/260826-tih-fix-two-code-review-warnings-in-index-ht/260826-tih-SUMMARY.md` when done
</output>
