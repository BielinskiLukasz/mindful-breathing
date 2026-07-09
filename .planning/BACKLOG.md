# Backlog

Last updated: 2026-07-10

---

## Staged for v1.1

Promoted during 2026-07-10 backlog review. Include in v1.1 milestone planning.

---

B-04 · Improve button hover states

Status: promoted → v1.1
Priority: Medium
Prerequisites: None

What: Add/refine hover interactions for all actionable controls.

Why: Provides better interaction feedback.

---

B-05 · Refine countdown timing animations

Status: promoted → v1.1
Priority: Medium
Prerequisites: None

What: Improve countdown timing transitions and associated micro-interactions.

Why: Makes session flow feel smoother and more polished.

---

B-07 · Move fullscreen button below breathing ring

Status: promoted → v1.1
Priority: High
Prerequisites: B-01 (done — basic landscape 2-col layout shipped in v0.6)

What: Relocate fullscreen button from top-right corner to left column below the ring.

Why: Improves layout consistency and space utilization.

---

B-08 · Vertically center Start/Reset controls in landscape

Status: promoted → v1.1
Priority: High
Prerequisites: B-01 (done)

What: Properly center Start and Reset buttons within the right column.

Why: Current alignment looks unbalanced.

---

B-09 · Reposition session information row in landscape

Status: promoted → v1.1
Priority: High
Prerequisites: B-01 (done)

What: Move session information below the breathing ring in landscape mode.

Why: Improves layout hierarchy and readability.

---

B-10 · Refine landscape grid layout

Status: promoted → v1.1
Priority: High
Prerequisites: B-07, B-08, B-09

What: Finalize overall 2-column landscape layout and eliminate overlapping components.

Why: Current landscape experience has significant usability issues.

---

B-14 · Import history from exported files

Status: promoted → v1.1
Priority: Medium
Prerequisites: B-12 (done — export history shipped in v0.6)

What: Import previously exported session history.

Why: Enables migration, restore, and device transfer scenarios.

---

B-16 · Fix landscape media-query breakpoint logic

Status: promoted → v1.1
Priority: High
Prerequisites: B-10

What: Use rotated viewport height (≤500px) instead of width when activating compact landscape layout.

Why: Layout breaks on tall phones such as iPhone 14 Pro Max.

---

B-17 · Add keyboard focus states

Status: promoted → v1.1
Priority: Low
Prerequisites: None

What: Add visible focus outlines for keyboard users.

Why: Improves accessibility.

---

B-18 · Implement logical tab navigation

Status: promoted → v1.1
Priority: Low
Prerequisites: B-17

What: Define and verify logical keyboard navigation order.

Why: Improves usability without a mouse/touchscreen.

---

B-19 · Add ARIA labels and accessibility attributes

Status: promoted → v1.1
Priority: Low
Prerequisites: None

What: Add appropriate screen-reader labels and accessibility metadata.

Why: Improves accessibility compliance.

---

## Not scheduled

---

B-20 · Add landscape layout position toggle

Status: captured · not scheduled
Priority: Low
Prerequisites: B-10

What: Allow users to choose ring-left or ring-right layout.

Why: Provides layout flexibility for different preferences.

---

B-22 · Store extended history metadata

Status: captured · not scheduled
Priority: Low
Prerequisites: None

What: Store preset mode and phase-level timing information in history records.

Why: Improves analytics possibilities.

---

B-24 · Dedicated layout for ultra-narrow phones (<500px)

Status: captured · not scheduled
Priority: Medium
Prerequisites: B-10

What: Implement a separate portrait layout optimized for very narrow devices.

Why: Current responsive layout is not optimal on small screens.

---

## Removed

B-02 · Fix Vibration API on Android/Chrome — removed 2026-07-10. Android OS suppresses navigator.vibrate() at the system level on Samsung/Android devices; vibration toggle already hidden via display:none; not actionable at the app level.
