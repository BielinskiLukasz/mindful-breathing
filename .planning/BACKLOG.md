Backlog

Last updated: 2026-07-09

---

B-01 · Standardize icon sizes to 32×32

Status: captured · not scheduled
 Priority: High
 Prerequisites: None

What: Standardize info and fullscreen icons to 32×32 across all views and orientations.

Why: Eliminates visual inconsistencies and improves UI polish.

---

B-02 · Fix Vibration API on Android/Chrome

Status: captured · not scheduled
 Priority: High
 Prerequisites: None

What: Debug navigator.vibrate() behavior on Android Chrome and verify vibration pattern arrays.

Why: Core breathing feedback is currently unreliable on some devices.

---

B-03 · Smooth theme transitions

Status: captured · not scheduled
 Priority: Medium
 Prerequisites: None

What: Add smooth transitions when switching between light and dark themes.

Why: Improves overall visual quality and perceived responsiveness.

---

B-04 · Improve button hover states

Status: captured · not scheduled
 Priority: Medium
 Prerequisites: None

What: Add/refine hover interactions for all actionable controls.

Why: Provides better interaction feedback.

---

B-05 · Refine countdown timing animations

Status: captured · not scheduled
 Priority: Medium
 Prerequisites: None

What: Improve countdown timing transitions and associated micro-interactions.

Why: Makes session flow feel smoother and more polished.

---

B-06 · Add scrollable history panel in landscape mode

Status: captured · not scheduled
 Priority: High
 Prerequisites: B-01

What: Restore history visibility in landscape mode and add dedicated scrolling support.

Why: History is currently inaccessible in landscape layout.

---

B-07 · Move fullscreen button below breathing ring

Status: captured · not scheduled
 Priority: High
 Prerequisites: B-01

What: Relocate fullscreen button from top-right corner to left column below the ring.

Why: Improves layout consistency and space utilization.

---

B-08 · Vertically center Start/Reset controls in landscape

Status: captured · not scheduled
 Priority: High
 Prerequisites: B-01

What: Properly center Start and Reset buttons within the right column.

Why: Current alignment looks unbalanced.

---

B-09 · Reposition session information row in landscape

Status: captured · not scheduled
 Priority: High
 Prerequisites: B-01

What: Move session information below the breathing ring in landscape mode.

Why: Improves layout hierarchy and readability.

---

B-10 · Refine landscape grid layout

Status: captured · not scheduled
 Priority: High
 Prerequisites: B-06, B-07, B-08, B-09

What: Finalize overall 2-column landscape layout and eliminate overlapping components.

Why: Current landscape experience has significant usability issues.

---

B-11 · Improve light theme contrast

Status: captured · not scheduled
 Priority: High
 Prerequisites: None

What: Update colors to achieve WCAG contrast targets defined in LIGHT-THEME-READABILITY-REPORT.md.

Why: Current light theme has readability and accessibility issues.

---

B-12 · Export history to JSON

Status: captured · not scheduled
 Priority: Medium
 Prerequisites: None

What: Allow users to export session history in JSON format.

Why: Enables backup and data portability.

---

B-13 · Export history to CSV

Status: captured · not scheduled
 Priority: Medium
 Prerequisites: None

What: Allow users to export session history in CSV format.

Why: Makes data usable in spreadsheets and analytics tools.

---

B-14 · Import history from exported files

Status: captured · not scheduled
 Priority: Medium
 Prerequisites: B-12

What: Import previously exported session history.

Why: Enables migration, restore, and device transfer scenarios.

---

B-15 · Clear history with confirmation dialog

Status: captured · not scheduled
 Priority: Medium
 Prerequisites: None

What: Add a confirmation modal before deleting all history.

Why: Prevents accidental data loss.

---

B-16 · Fix landscape media-query breakpoint logic

Status: captured · not scheduled
 Priority: High
 Prerequisites: B-10

What: Use rotated viewport height (≤500px) instead of width when activating compact landscape layout.

Why: Layout breaks on tall phones such as iPhone 14 Pro Max.

---

B-17 · Add keyboard focus states

Status: captured · not scheduled
 Priority: Low
 Prerequisites: None

What: Add visible focus outlines for keyboard users.

Why: Improves accessibility.

---

B-18 · Implement logical tab navigation

Status: captured · not scheduled
 Priority: Low
 Prerequisites: B-17

What: Define and verify logical keyboard navigation order.

Why: Improves usability without a mouse/touchscreen.

---

B-19 · Add ARIA labels and accessibility attributes

Status: captured · not scheduled
 Priority: Low
 Prerequisites: None

What: Add appropriate screen-reader labels and accessibility metadata.

Why: Improves accessibility compliance.

---

B-20 · Add landscape layout position toggle

Status: captured · not scheduled
 Priority: Low
 Prerequisites: B-10

What: Allow users to choose ring-left or ring-right layout.

Why: Provides layout flexibility for different preferences.

---

B-21 · Track unfinished sessions

Status: captured · not scheduled
 Priority: Low
 Prerequisites: None

What: Record interrupted sessions including elapsed time and completed cycles.

Why: Enables better session analytics.

---

B-22 · Store extended history metadata

Status: captured · not scheduled
 Priority: Low
 Prerequisites: None

What: Store preset mode and phase-level timing information in history records.

Why: Improves analytics possibilities.

---

B-23 · Display application version in info dialog

Status: captured · not scheduled
 Priority: Low
 Prerequisites: None

What: Show current application version (e.g., v0.6) inside the info panel.

Why: Helps testing, support, and troubleshooting.

---

B-24 · Dedicated layout for ultra-narrow phones (<500px)

Status: captured · not scheduled
 Priority: Medium
 Prerequisites: B-10

What: Implement a separate portrait layout optimized for very narrow devices.

Why: Current responsive layout is not optimal on small screens.

---

B-25 · Add dark-mode background tint transitions

Status: captured · not scheduled
 Priority: Low
 Prerequisites: B-03

What: Animate background tint changes in dark mode.

Why: Further improves visual polish.

---
