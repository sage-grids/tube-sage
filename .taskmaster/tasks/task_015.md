# Task ID: 15

**Title:** Build feed overlay UI: video card badges and tooltips

**Status:** pending

**Dependencies:** 10, 14

**Priority:** high

**Description:** Implement the browse feed indicator UI from PRD section 5.3. Inject color-coded badges into YouTube video card thumbnails showing evaluation results: green (HIGH_VALUE), amber (MIXED), red (LOW_VALUE), gray (not evaluated). Add click-to-expand tooltip showing the 1-2 line explanation. Include 'Hide this video' and 'Hide similar videos' actions in the tooltip.

**Details:**

Create UI components injected into YouTube DOM: EvaluationBadge (small colored indicator in thumbnail corner with colors from PRD: #22C55E green, #EAB308 amber, #EF4444 red, #9CA3AF gray), EvaluationTooltip (popover on badge click showing classification label, explanation text, signal breakdown, hide actions). Use CSS-in-JS or injected stylesheets scoped to avoid YouTube CSS conflicts. Handle badge placement for different card types (grid cards, list items, sidebar recommendations). Update badges reactively when evaluations arrive (initial gray -> colored after evaluation). Implement hide actions that send behavioral signals and visually dim/remove the video card. Ensure badges are re-injected after YouTube SPA navigations.

**Test Strategy:**

No test strategy provided.
