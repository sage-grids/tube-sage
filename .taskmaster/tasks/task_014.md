# Task ID: 14

**Title:** Build service worker: API communication and evaluation orchestration

**Status:** pending

**Dependencies:** 9, 12, 6

**Priority:** high

**Description:** Implement the Chrome extension service worker (background.ts) that coordinates API communication, manages the evaluation lifecycle, and orchestrates cache lookups. The service worker receives messages from content scripts, checks L1 cache, makes batch API calls to the server, and dispatches results back to content scripts for UI rendering.

**Details:**

Create background.ts service worker with message handlers: EVALUATE_BATCH (receives video IDs from content script, checks L1 cache, sends uncached to server batch endpoint, returns results), EVALUATE_REALTIME (sends partial transcript to realtime endpoint), SUBMIT_SIGNALS (fire-and-forget behavioral signals). Implement APIClient class for server communication with JWT auth header, error handling, and retry logic. Implement EvaluationOrchestrator that coordinates: L1 cache check -> server batch request -> cache write -> response dispatch. Set up chrome.alarms for periodic cache pruning. Handle extension lifecycle events (install, update). Store anonymous user UUID in chrome.storage.local. Implement JWT token management (exchange UUID for JWT, refresh on expiry).

**Test Strategy:**

No test strategy provided.
