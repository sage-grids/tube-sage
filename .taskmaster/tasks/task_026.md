# Task ID: 26

**Title:** Write unit and integration tests for backend packages

**Status:** pending

**Dependencies:** 4, 5, 3, 6, 8

**Priority:** medium

**Description:** Write comprehensive test suites using Vitest for all backend packages: packages/transcripts (cleaning, hashing, chunking), packages/evaluation (heuristic analyzers, classification logic, prompt rendering), packages/db (repository layer with test database), apps/api (endpoint integration tests), apps/worker (job processing tests). Target meaningful coverage of core business logic.

**Details:**

Test suites: 1) packages/transcripts: test cleanTranscript with various inputs (timestamps, unicode, whitespace), hashTranscript determinism, chunkTranscript segment boundaries. 2) packages/evaluation: test each heuristic analyzer with known-good and known-bad transcripts, classification engine threshold logic, prompt template rendering. 3) packages/db: integration tests with Docker PostgreSQL, test all repository methods (CRUD, composite key lookups, upserts). 4) apps/api: supertest integration tests for all endpoints, auth middleware, rate limiting, Zod validation rejection, contribute endpoint trust validation. 5) apps/worker: test job processing with mocked LLM calls, retry logic, cache write-back. Use Vitest with setup files for Docker services.

**Test Strategy:**

No test strategy provided.
