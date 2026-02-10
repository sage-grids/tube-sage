# Task ID: 12

**Title:** Implement IndexedDB evaluation cache (L1) in the extension

**Status:** pending

**Dependencies:** 9

**Priority:** high

**Description:** Build the client-side IndexedDB cache layer (L1) for storing evaluations in the Chrome extension. Cache is keyed by composite key (youtube_id, transcript_hash, eval_version). Implement TTL-based expiry (7 days). Provide fast synchronous-feeling lookups for previously seen videos. Include cache stats tracking and periodic pruning via service worker alarms.

**Details:**

Create storage/cache.ts using IndexedDB (via idb library or raw API). Define EvaluationCache store with composite index on (youtube_id, transcript_hash, eval_version). Implement methods: get(videoId, transcriptHash, evalVersion), set(evaluation, ttl), delete(videoId), prune() (remove expired entries), getStats() (hit count, miss count, total entries, storage size). Set up chrome.alarms in service worker for periodic pruning (every 6 hours). Implement cache-first lookup pattern: check L1 -> if miss, return null (caller handles L2/L3). Write to L1 when evaluations are received from server or generated client-side. Track and expose cache statistics for the popup UI.

**Test Strategy:**

No test strategy provided.
