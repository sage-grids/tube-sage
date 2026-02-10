# Task ID: 22

**Title:** Implement Redis L2 cache layer on the API server

**Status:** pending

**Dependencies:** 6, 3

**Priority:** high

**Description:** Set up the Redis-based L2 cache layer on the API server from PRD section 6.5. Cache evaluations in Redis keyed by (youtube_id, transcript_hash, eval_version) with a 24-hour TTL. The cache sits between the client (L1) and the database (L3). On cache miss, check L3 (PostgreSQL), then queue for LLM evaluation. Write back to L2 after L3 writes.

**Details:**

Create CacheService using ioredis or redis client. Key format: eval:{youtube_id}:{transcript_hash}:{eval_version}. Methods: getEvaluation(videoId, transcriptHash, evalVersion) -> Evaluation | null, setEvaluation(evaluation, ttl=86400), invalidateByVersion(evalVersion), getMultiple(keys[]) for batch lookups. Integrate into the API evaluation flow: batch endpoint checks L2 first, then falls back to L3 repository lookup, then queues for worker. Worker writes to L3 then L2. Contributed evaluations also write to L2 after L3 acceptance. Add Redis connection config to environment variables. Handle Redis connection failures gracefully (fall back to L3-only).

**Test Strategy:**

No test strategy provided.
