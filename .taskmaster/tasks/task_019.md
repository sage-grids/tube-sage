# Task ID: 19

**Title:** Build popup UI: settings panel with cache stats and evaluation info

**Status:** pending

**Dependencies:** 9, 12

**Priority:** medium

**Description:** Build the React popup UI for the Chrome extension. Include a strictness slider, hidden channel management, cache statistics display (total entries, hit rate, storage size), current evaluation version info, and quick toggles for enabling/disabling the extension. The popup should be lightweight and load instantly.

**Details:**

Create React app in popup/ directory. Components: StrictnessSlider (adjustable threshold for evaluation sensitivity), HiddenChannelsList (view/manage hidden channels), CacheStats (display L1 cache metrics: total entries, hit/miss ratio, storage usage, last pruned), EvalVersionInfo (show current eval_version tag), QuickToggle (enable/disable extension on current page/globally), ContributionStatusBadge (show if community contribution is active, with link to options for setup). Use chrome.storage.local for persisting preferences. Communicate with service worker via chrome.runtime.sendMessage. Keep bundle size minimal (<100KB). Style consistently with extension theme.

**Test Strategy:**

No test strategy provided.
