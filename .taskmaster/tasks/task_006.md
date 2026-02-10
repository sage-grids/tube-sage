# Task ID: 6

**Title:** Build API server with core evaluation endpoints

**Status:** pending

**Dependencies:** 3, 5

**Priority:** high

**Description:** Create the apps/api REST API server using Express or Fastify with TypeScript. Implement all core endpoints from PRD section 4.2: POST /api/v1/evaluate/batch, GET /api/v1/evaluate/:videoId, POST /api/v1/evaluate/realtime, POST /api/v1/signals, GET/PUT /api/v1/preferences, GET /api/v1/health. Set up Zod request validation middleware, JWT authentication, rate limiting (60 req/min per user), and error handling.

**Details:**

Set up Express/Fastify app with TypeScript. Create middleware: authMiddleware (JWT validation), rateLimitMiddleware (60/min per user, cached lookups free), validateMiddleware (Zod schema validation). Implement route handlers for each endpoint. Wire repository layer from packages/db for data access. Integrate packages/evaluation for LLM evaluation orchestration. Set up Redis client for L2 cache layer. Implement batch evaluation logic: check L2 cache -> check L3 DB -> queue uncached for worker processing. Return CACHED/QUEUED/ERROR status per video. Add health endpoint returning current eval_version. Configure CORS for extension origin.

**Test Strategy:**

No test strategy provided.
