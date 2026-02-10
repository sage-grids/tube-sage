# Task ID: 16

**Title:** Build watch page early warning toast UI

**Status:** pending

**Dependencies:** 11, 13, 14

**Priority:** high

**Description:** Implement the watch page early warning system from PRD section 5.3. When red flags spike in the first 30-90 seconds of playback, display a subtle toast banner below the video player: 'High persuasion density detected. You may want to skip.' The banner auto-dismisses after 8 seconds and can be permanently dismissed for the current video.

**Details:**

Create EarlyWarningToast component injected below the YouTube video player (#movie_player or #player-container). Toast appears when: realtime evaluation returns LOW_VALUE classification during first 30-90 seconds, OR client-side heuristics detect high manipulation/extractive scores. Design: subtle, non-intrusive banner with warning icon, message text, and dismiss button. Auto-dismiss timer: 8 seconds. Permanent dismiss: store dismissed video IDs in session storage (not persisted across sessions). Animate in/out smoothly. Ensure toast doesn't interfere with video controls or YouTube's own overlays. Style to be visually distinct but not jarring.

**Test Strategy:**

No test strategy provided.
