# Task ID: 2

**Title:** Set up shared packages: types, constants, and validation schemas

**Status:** pending

**Dependencies:** 1

**Priority:** high

**Description:** Create the packages/shared package with all shared TypeScript types, DTOs, Zod validation schemas, constants, and error codes used across the extension, API, and worker. Define core types: VideoInput, EvaluationResult, SignalBreakdown, Classification enum (HIGH_VALUE, MIXED, LOW_VALUE), EvalStatus enum (CACHED, QUEUED, ERROR), and all API request/response contracts from PRD section 4.3.

**Details:**

Define Zod schemas for all API request/response bodies. Export TypeScript types inferred from Zod schemas. Include constants: signal thresholds, classification rules, rate limits, cache TTLs. Define error code enum. Create evaluation-related types: SignalCategory scores, Evaluation, ContributedEvaluation. Create provider-related types: AIProvider enum (OPENAI, OPENROUTER, OLLAMA, OPENCODE), ProviderConfig. All exports via barrel index.ts.

**Test Strategy:**

No test strategy provided.
