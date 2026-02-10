# Task ID: 8

**Title:** Build background worker service with BullMQ job processing

**Status:** pending

**Dependencies:** 3, 5

**Priority:** high

**Description:** Create the apps/worker background job processor using BullMQ and Redis. Implement async transcript processing, queued full evaluations triggered by the API's batch endpoint, and background cache warming. Workers consume jobs from the evaluation queue, run them through the evaluation pipeline, and write results back to L3 (PostgreSQL) and L2 (Redis) cache layers.

**Details:**

Set up BullMQ worker with Redis connection. Define job types: FULL_EVALUATION (video_id + transcript -> full LLM evaluation), CACHE_WARM (pre-evaluate trending/popular videos). Implement job processors that use packages/evaluation for LLM calls and packages/db repositories for persistence. Add retry logic with exponential backoff for failed LLM calls. Implement job progress tracking. Configure concurrency limits. Write results to Evaluation table (L3) and Redis cache (L2). Add health check endpoint for worker monitoring. Log processing metrics (latency, success/failure rates).

**Test Strategy:**

No test strategy provided.
