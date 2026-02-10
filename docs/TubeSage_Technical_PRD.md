

**TubeSage**

Real-Time YouTube Surfing Assistant

Technical Product Requirements Document

Version 1.0  |  February 2026

**CONFIDENTIAL**

| Document Owner | Engineering & Product |
| :---- | :---- |
| **Status** | Draft |
| **Target Release** | v1.0 MVP |
| **Tech Stack** | TypeScript, React, Node.js, PostgreSQL (primary), Prisma ORM |
| **Last Updated** | February 10, 2026 |

# **Table of Contents**

# **1\. Executive Summary**

## **1.1 Product Vision**

TubeSage is a Chrome extension backed by a lightweight evaluation service that augments the YouTube browsing experience with real-time, explainable quality indicators. Its purpose is interruption prevention: helping users identify low-value, manipulative, or clickbait videos before they click, and providing early-warning nudges during the first seconds of playback.

The guiding principle is: protect attention first; insight comes later.

## **1.2 Problem Statement**

YouTube optimizes its native signals (views, likes, thumbnails) for engagement, not usefulness. Users routinely click videos that promise insight but deliver fluff, discover manipulation only after minutes are lost, and face repeated exposure to creators who optimize for extraction over value. TubeSage provides pre-click and early-watch signals that protect user attention in real time.

## **1.3 Target User**

Intentional YouTube consumers: learners, researchers, and thinkers who feel friction or regret after watching low-value content and who want less content, not more recommendations. This product serves active surfers, not passive scrollers.

## **1.4 Core Job-To-Be-Done**

*While I browse YouTube, **help me quickly distinguish worthwhile content from attention traps**, so I don’t waste time.*

## **1.5 Success Criteria (MVP)**

* Users stop clicking videos they would have clicked before installing TubeSage.

* Users report measurably less regret after browsing sessions.

* Users describe the product as: *“A spam filter for YouTube.”*

# **2\. System Architecture Overview**

## **2.1 High-Level Architecture**

TubeSage follows a client-heavy, server-light architecture. The Chrome extension performs transcript acquisition, text cleaning, and lightweight heuristic scoring on the client side. The backend API provides canonical LLM-based evaluations, a global cache, and shared data consistency. This design minimizes server costs while keeping the user experience fast.

**Architecture Components**

| Component | Technology | Responsibility |
| :---- | :---- | :---- |
| Chrome Extension | TypeScript, React (popup/options), Chrome APIs | DOM injection, transcript capture, local heuristics, local cache (IndexedDB), UI overlays |
| API Server | Node.js, Express/Fastify, TypeScript | Video ID normalization, LLM evaluation orchestration, global cache management, rate limiting |
| Worker Service | Node.js, BullMQ, TypeScript | Async transcript processing, queued full evaluations, background cache warming |
| Database Layer | PostgreSQL (primary) via Prisma ORM | Evaluation cache, user preferences, behavioral signals, evaluation versioning |
| LLM Provider | OpenAI / Anthropic API (configurable) | Transcript-based content classification and explanation generation |

## **2.2 Monorepo Structure**

The project uses a monorepo managed by Turborepo with pnpm workspaces. All packages are written in TypeScript with strict mode enabled.

| Path | Description |
| :---- | :---- |
| apps/extension | Chrome extension (Manifest V3, React popup, content scripts) |
| apps/api | REST API server (Express or Fastify) |
| apps/worker | Background job processor (BullMQ \+ Redis) |
| packages/evaluation | Evaluation pipeline: heuristics engine \+ LLM prompt chains |
| packages/transcripts | Transcript fetching, cleaning, chunking utilities |
| packages/db | Prisma schema, client, migrations, repository layer |
| packages/shared | Shared types, constants, DTOs, error codes, validation schemas (Zod) |
| packages/config | Shared ESLint, TypeScript, and build configurations |

# **3\. Database Architecture & Abstraction Layer**

## **3.1 ORM Selection: Prisma**

The project uses Prisma as its ORM and database abstraction layer. Prisma was chosen over alternatives (TypeORM, Drizzle, Sequelize) for several reasons:

* Type-safe query builder with auto-generated TypeScript types from the schema.

* First-class support for PostgreSQL (primary), MySQL, SQLite, SQL Server, and CockroachDB via swappable provider configuration.

* Declarative schema language with built-in migration tooling (prisma migrate).

* Excellent developer experience: introspection, Prisma Studio, and a mature ecosystem.

## **3.2 Why Not MongoDB?**

MongoDB was evaluated and rejected as the primary datastore for v1 for the following reasons:

| Consideration | PostgreSQL (Selected) | MongoDB (Rejected for v1) |
| :---- | :---- | :---- |
| Data model | Evaluations, signals, and cache entries are highly relational (video → evaluations → signals → versions). Relational joins are natural. | Document model would require denormalization or manual joins, increasing complexity for versioned evaluations. |
| Query patterns | Lookups by composite keys (video\_id \+ transcript\_hash \+ eval\_version), aggregations for analytics, and transactional writes fit SQL perfectly. | Flexible queries are possible but lack the transactional guarantees needed for cache-coherent evaluation versioning. |
| Schema evolution | Prisma migrations provide controlled, reversible schema changes with type safety. | Schema-less flexibility is unnecessary here; our schema is well-defined and benefits from enforcement. |
| JSONB support | PostgreSQL’s native JSONB columns handle semi-structured data (LLM responses, signal metadata) without sacrificing relational integrity. | Storing JSON is MongoDB’s strength, but PostgreSQL JSONB closes the gap for our use case. |
| ORM abstraction | Prisma’s PostgreSQL support is mature and full-featured. | Prisma’s MongoDB support exists but has limitations (no migrations, no raw transactions). |
| Operational cost | Managed PostgreSQL (Supabase, Neon, RDS) is cost-effective and well-understood. | MongoDB Atlas is viable but adds operational complexity without clear benefit for this workload. |

**Verdict:** PostgreSQL is the correct choice for TubeSage v1. The data is inherently relational, the query patterns favor SQL, and Prisma’s abstraction allows switching to another SQL provider (MySQL, CockroachDB) with a single configuration change if the need arises. MongoDB could serve as a secondary store for unstructured analytics or logging in a future iteration.

## **3.3 Database Abstraction Layer (Repository Pattern)**

To decouple business logic from the ORM, the packages/db package exposes a repository layer. All data access goes through repository interfaces, making the Prisma implementation swappable.

**Layer Architecture**

| Layer | Location | Responsibility |
| :---- | :---- | :---- |
| Prisma Schema | packages/db/prisma/schema.prisma | Canonical source of truth for data model; generates typed client |
| Prisma Client | packages/db/src/client.ts | Singleton Prisma client instance with connection pooling |
| Repository Interfaces | packages/db/src/repositories/\*.interface.ts | TypeScript interfaces defining data access contracts (e.g., IEvaluationRepository) |
| Prisma Repositories | packages/db/src/repositories/\*.prisma.ts | Concrete Prisma implementations of each repository interface |
| Repository Factory | packages/db/src/repositories/index.ts | Factory function that wires concrete implementations; swap point for testing or provider changes |
| DTOs / Types | packages/shared/src/types/\*.ts | Transport-layer types decoupled from Prisma-generated types |

This means the API and Worker services never import Prisma directly. They import repository interfaces and the factory wires the correct implementation at bootstrap. For unit testing, in-memory or mock repositories can be injected without touching the database.

## **3.4 Data Model (Prisma Schema)**

The following table describes the core entities in the database. The full Prisma schema lives in packages/db/prisma/schema.prisma.

| Entity | Key Fields | Purpose |
| :---- | :---- | :---- |
| Video | id (PK), youtube\_id (unique), channel\_id, title, published\_at, metadata (JSONB) | Canonical video record; deduplicated by youtube\_id |
| Evaluation | id (PK), video\_id (FK), transcript\_hash, eval\_version, classification, confidence, explanation, signals (JSONB), created\_at | Cached evaluation result; keyed by (video\_id, transcript\_hash, eval\_version) composite unique |
| TranscriptSegment | id (PK), video\_id (FK), start\_ms, end\_ms, text, segment\_type (enum: EARLY, FULL) | Stored transcript chunks for re-evaluation when prompts change |
| UserPreference | id (PK), user\_id (unique), strictness, hidden\_channels (JSONB), created\_at, updated\_at | Per-user configuration synced from extension |
| BehavioralSignal | id (PK), user\_id, video\_id (FK), signal\_type (enum: EARLY\_EXIT, LONG\_WATCH, HIDE, etc.), timestamp | Implicit feedback for future personalization |
| EvalVersion | id (PK), version\_tag (unique), prompt\_hash, heuristic\_hash, created\_at | Tracks prompt/heuristic versions for cache invalidation |

**Key Indexes**

* Evaluation: composite unique on (video\_id, transcript\_hash, eval\_version) for cache lookups.

* Video: unique index on youtube\_id; index on channel\_id for channel-level queries.

* BehavioralSignal: compound index on (user\_id, signal\_type, timestamp) for time-range aggregations.

# **4\. API Specification**

## **4.1 API Design Principles**

* RESTful JSON API with versioned paths (/api/v1/\*).

* All request and response bodies validated with Zod schemas (shared in packages/shared).

* Authentication via extension-issued JWT (anonymous user ID \+ optional account linking).

* Rate limiting: 60 requests/minute per user for evaluation endpoints; cached lookups are free.

* Batch-first: the primary endpoint accepts arrays of video IDs for feed-level evaluation.

## **4.2 Core Endpoints**

| Method | Path | Description |
| :---- | :---- | :---- |
| POST | /api/v1/evaluate/batch | Submit an array of video IDs (max 50\) with optional transcript segments. Returns cached evaluations immediately; queues uncached videos for processing. Response includes status per video (CACHED, QUEUED, ERROR). |
| GET | /api/v1/evaluate/:videoId | Retrieve a single evaluation by video ID. Returns 404 if not yet evaluated. Accepts ?version= query param to pin evaluation version. |
| POST | /api/v1/evaluate/realtime | Submit a partial transcript (first 60–120s) for real-time early-warning evaluation. Returns a lightweight classification within seconds. Used during video playback. |
| POST | /api/v1/signals | Submit behavioral signals (early exit, long watch, hide) in batches. Fire-and-forget from the extension; processed asynchronously. |
| GET | /api/v1/preferences | Retrieve user preferences. |
| PUT | /api/v1/preferences | Update user preferences (strictness level, hidden channels, etc.). |
| GET | /api/v1/health | Health check \+ current eval\_version tag. |

## **4.3 Request/Response Contracts**

**POST /api/v1/evaluate/batch — Request**

Content-Type: application/json

| Field | Type | Required | Description |
| :---- | :---- | :---- | :---- |
| videos | VideoInput\[\] | Yes | Array of video objects to evaluate (max 50\) |
| videos\[\].youtube\_id | string | Yes | YouTube video ID (11 characters) |
| videos\[\].transcript | string | null | No | Client-extracted transcript text. If provided, server skips transcript fetch. |
| videos\[\].transcript\_hash | string | null | No | SHA-256 hash of the cleaned transcript. Used for cache key matching. |

**POST /api/v1/evaluate/batch — Response**

| Field | Type | Description |
| :---- | :---- | :---- |
| results | EvaluationResult\[\] | One entry per submitted video, in the same order |
| results\[\].youtube\_id | string | Echo of the submitted video ID |
| results\[\].status | "CACHED" | "QUEUED" | "ERROR" | Processing status for this video |
| results\[\].evaluation | Evaluation | null | Full evaluation object if CACHED; null if QUEUED or ERROR |
| results\[\].evaluation.classification | "HIGH\_VALUE" | "MIXED" | "LOW\_VALUE" | Coarse quality label |
| results\[\].evaluation.confidence | number (0–1) | Model confidence in the classification |
| results\[\].evaluation.explanation | string | 1–2 line human-readable explanation |
| results\[\].evaluation.signals | SignalBreakdown | Structured signal scores (value\_density, manipulation, extractive, language) |
| eval\_version | string | The evaluation pipeline version used |

# **5\. Chrome Extension Architecture**

## **5.1 Manifest V3 Structure**

The extension is built on Chrome Manifest V3 with the following components:

| Component | File(s) | Responsibility |
| :---- | :---- | :---- |
| Service Worker | background.ts | Manages extension lifecycle, coordinates API calls, handles alarms for periodic cache pruning |
| Content Script | content/inject.ts | Injected into YouTube pages. Observes DOM mutations for video cards, injects overlay indicators, captures transcript streams |
| Popup UI | popup/ (React) | Settings panel: strictness slider, hidden channel management, cache stats, evaluation version info |
| Options Page | options/ (React) | Extended settings: API endpoint configuration, privacy mode toggle, data export |
| IndexedDB Store | storage/cache.ts | Client-side evaluation cache keyed by (youtube\_id, transcript\_hash, eval\_version). TTL-based expiry. |

## **5.2 Client-Side Processing Pipeline**

The extension performs significant work on the client to minimize server load:

**Step 1: Video Discovery**

A MutationObserver watches for new video card elements in the YouTube DOM (home feed, search results, subscriptions, sidebar recommendations). When new cards appear, the content script extracts youtube\_id values from href attributes.

**Step 2: Local Cache Check**

Each discovered video ID is checked against the local IndexedDB cache. Cache hits return the stored evaluation immediately, and the UI overlay is rendered without any network call.

**Step 3: Transcript Acquisition (Watch Page)**

When the user navigates to a watch page, the extension uses a hybrid approach:

* **Option A — DOM Caption Stream:** If captions are enabled, the content script observes the live caption DOM elements and streams timed text as it appears. This provides the first 30–90 seconds of transcript with zero network cost.

* **Option B — TimedText Endpoint Fetch:** The extension discovers caption track metadata from the player config and fetches the full caption XML/JSON directly from YouTube’s timedtext endpoints. This is more complete but slightly slower.

The extension uses Option A when available and falls back to Option B. Transcripts are cleaned (timestamps stripped, punctuation normalized) and hashed (SHA-256) client-side.

**Step 4: Local Heuristic Scoring**

Before sending anything to the server, the extension runs lightweight heuristic checks:

* CTA density: frequency of call-to-action phrases per minute of transcript.

* Promise inflation: regex-based detection of hyperbolic framing patterns.

* Repetition rate: n-gram analysis to detect low information density.

* Emotional priming: keyword frequency for manipulation-associated language.

If the local heuristic confidence is high (above a configurable threshold), the extension can render a preliminary indicator without waiting for the server.

**Step 5: Server Evaluation Request**

For uncached videos, the extension sends a batch request to POST /api/v1/evaluate/batch with video IDs and client-extracted transcripts. For real-time early warnings during playback, a separate call to POST /api/v1/evaluate/realtime sends the first 60–120 seconds of transcript.

## **5.3 UI Surfaces**

**Browse Feed Indicators**

Each video card receives a small, color-coded badge injected into the thumbnail corner:

| Indicator | Color | Meaning |
| :---- | :---- | :---- |
| 🟢 Likely High Value | Green (\#22C55E) | Strong informational specificity, low manipulation signals, minimal CTAs |
| 🟡 Mixed / Promotional | Amber (\#EAB308) | Some value present but combined with promotional or vague content |
| 🔴 Likely Clickbait | Red (\#EF4444) | High persuasion density, low informational value, extractive patterns |
| ⬜ Not Evaluated | Gray (\#9CA3AF) | No transcript available or evaluation pending |

Clicking the badge reveals a tooltip with the 1–2 line explanation. The user can also "Hide this video" or "Hide similar videos" from this tooltip.

**Watch Page Early Warning**

If red flags spike in the first 30–90 seconds of playback, a subtle toast banner appears below the video player: “High persuasion density detected. You may want to skip.” The banner auto-dismisses after 8 seconds and can be permanently dismissed for the current video.

# **6\. Evaluation Pipeline**

## **6.1 Pipeline Overview**

The evaluation pipeline lives in packages/evaluation and combines fast heuristics with LLM-based classification. It operates in two modes:

| Mode | Trigger | Input | Latency Target | Cost |
| :---- | :---- | :---- | :---- | :---- |
| Early Warning | User starts watching a video | First 60–120 seconds of transcript | \< 3 seconds | Low (small prompt, cached aggressively) |
| Full Evaluation | Batch request from feed or user-initiated | Full transcript (or first 10 minutes) | \< 10 seconds (async acceptable) | Medium (larger prompt, one LLM call per uncached video) |

## **6.2 Signal Categories**

The evaluation model scores each video across four signal dimensions. Each dimension produces a normalized score (0–1) and a set of evidence phrases.

| Signal Category | What It Measures | Example Indicators |
| :---- | :---- | :---- |
| Value Density | How quickly and specifically the video delivers concrete information | Time to first concrete insight, specificity vs. vagueness, repetition rate, use of examples/data |
| Manipulation & Clickbait | Emotional or cognitive manipulation tactics | Promise inflation (“this will change everything”), emotional priming without substance, authority signaling without evidence, false urgency |
| Extractive Behavior | Monetization or funnel-oriented pressure | CTA frequency per minute, early funnel placement, scarcity framing, course/product pitches, affiliate link density |
| Language Patterns | Structural rhetorical quality | Excessive generalities, moral licensing, false dichotomies, hedging vs. specificity ratio, filler word density |

## **6.3 Classification Logic**

The final classification is derived from a weighted combination of the four signal scores:

* **HIGH\_VALUE:** Value Density score ≥ 0.6 AND Manipulation score ≤ 0.3 AND Extractive score ≤ 0.3

* **LOW\_VALUE:** Value Density score ≤ 0.3 OR Manipulation score ≥ 0.7 OR Extractive score ≥ 0.7

* **MIXED:** Everything else. Indicates partial value with caveats.

Weights and thresholds are configurable per EvalVersion, allowing A/B testing and iterative tuning.

## **6.4 LLM Prompt Design**

The LLM receives a structured prompt containing the transcript segment, the signal definitions, and instructions to output a JSON object with scores, classification, and a 1–2 sentence explanation. The prompt is versioned (stored in EvalVersion) and its hash is part of the cache key, ensuring that prompt changes automatically invalidate stale evaluations.

The prompt explicitly instructs the model to be viewer-centric, non-moralizing, and to focus on informational utility rather than content quality judgments.

## **6.5 Caching Strategy**

| Cache Layer | Technology | Key | TTL | Purpose |
| :---- | :---- | :---- | :---- | :---- |
| L1 — Client | IndexedDB (extension) | (youtube\_id, transcript\_hash, eval\_version) | 7 days | Instant results for previously seen videos; no network call |
| L2 — Server Memory | Redis | (youtube\_id, transcript\_hash, eval\_version) | 24 hours | Hot cache for frequently requested videos across all users |
| L3 — Database | PostgreSQL (Evaluation table) | (video\_id, transcript\_hash, eval\_version) composite unique | Indefinite | Canonical persistent cache; source of truth for all evaluations |

Cache flow: L1 → L2 → L3 → LLM evaluation → write back to L3 → L2 → return to client (client writes L1). When the eval\_version changes, all cache layers are effectively invalidated because the version is part of the key.

# **7\. Performance & Scalability Requirements**

| Metric | Target | Measurement |
| :---- | :---- | :---- |
| Cached evaluation latency (L1) | \< 5 ms | Time from video card render to indicator display for IndexedDB-cached results |
| Cached evaluation latency (L2) | \< 50 ms | Round-trip time for Redis-cached server responses |
| Uncached batch evaluation | \< 10 seconds (p95) | Time from batch submission to all results available, including LLM calls |
| Real-time early warning | \< 3 seconds (p95) | Time from transcript submission to early-warning classification response |
| Extension memory footprint | \< 50 MB | Total extension memory usage including IndexedDB cache |
| Extension CPU (idle) | \< 1% CPU | CPU usage when not actively processing a YouTube page |
| API throughput | ≥ 500 req/s | Sustained request handling at the API layer (cached responses) |
| Database query time (cache hit) | \< 10 ms (p99) | Composite-key lookup on the Evaluation table |
| Availability | 99.5% uptime | API server availability (graceful degradation: extension works offline with L1 cache) |

## **7.1 Scalability Strategy**

* **Horizontal API scaling:** Stateless API servers behind a load balancer. Session state lives in Redis and PostgreSQL.

* **Worker queue scaling:** BullMQ workers can be scaled independently based on queue depth. Each worker is stateless.

* **Database read replicas:** Prisma supports read replicas natively. Cache-hit reads can be directed to replicas, keeping the primary for writes.

* **Client-side offloading:** As the local heuristic engine improves, the percentage of evaluations requiring server-side LLM calls decreases over time.

# **8\. Security & Privacy**

## **8.1 Data Collection Principles**

* The extension collects the minimum data necessary: video IDs, transcript segments, and anonymous behavioral signals.

* No YouTube account credentials are accessed or stored.

* Behavioral signals (early exit, long watch, hide) are associated with anonymous user IDs, not personally identifiable information.

* Transcript text sent to the server is used solely for evaluation and is not stored beyond the TranscriptSegment cache (which exists for re-evaluation purposes and can be opted out of).

## **8.2 Authentication & Authorization**

* The extension generates a random UUID on first install, stored in chrome.storage.local.

* This UUID is exchanged for a short-lived JWT via a lightweight /auth/token endpoint.

* All API requests include the JWT in the Authorization header.

* Rate limiting is enforced per anonymous user ID.

* Optional: users can link their account to an email for cross-device sync (future feature).

## **8.3 Privacy Mode**

A "Privacy Mode" toggle in the extension options disables all server communication. In this mode, the extension operates entirely on client-side heuristics and the local IndexedDB cache. No data leaves the browser.

## **8.4 Security Measures**

* All API communication over HTTPS with TLS 1.3.

* Input validation on all endpoints via Zod schemas (defense against injection and malformed payloads).

* Rate limiting and abuse detection at the API gateway level.

* Prisma parameterized queries prevent SQL injection by design.

* Content Security Policy (CSP) headers in the extension manifest restrict script sources.

* Dependency auditing via pnpm audit in CI pipeline.

# **9\. Development & Deployment**

## **9.1 Development Environment**

| Tool | Version | Purpose |
| :---- | :---- | :---- |
| Node.js | ≥ 20 LTS | Runtime for API, Worker, and build tooling |
| pnpm | ≥ 9 | Package manager with workspace support |
| Turborepo | Latest | Monorepo build orchestration and caching |
| TypeScript | ≥ 5.4 (strict mode) | Language for all packages |
| Prisma | ≥ 5.x | ORM, migrations, and database client generation |
| Docker Compose | Latest | Local PostgreSQL, Redis, and service orchestration |
| Vitest | Latest | Unit and integration testing |
| Playwright | Latest | E2E testing for the Chrome extension |

## **9.2 CI/CD Pipeline**

* GitHub Actions for CI: lint (ESLint \+ Prettier), type-check, unit tests, integration tests (against Docker PostgreSQL \+ Redis).

* Database migrations run automatically in CI via prisma migrate deploy.

* Extension builds produce a .zip artifact for Chrome Web Store submission.

* API and Worker deploy as Docker containers to a container orchestration platform (e.g., Railway, Fly.io, or AWS ECS).

* Preview environments per pull request for the API (with ephemeral databases).

## **9.3 Deployment Architecture**

| Service | Hosting | Scaling |
| :---- | :---- | :---- |
| API Server | Docker container (Fly.io / Railway / ECS) | Auto-scale 2–10 instances based on request volume |
| Worker | Docker container (same platform) | Auto-scale 1–5 instances based on BullMQ queue depth |
| PostgreSQL | Managed service (Neon / Supabase / RDS) | Vertical scaling \+ read replicas as needed |
| Redis | Managed service (Upstash / ElastiCache) | Single instance sufficient for v1; cluster for scale |
| Chrome Extension | Chrome Web Store | Client-side; no hosting required |

# **10\. Ethical Guidelines & Constraints**

TubeSage occupies a sensitive position: it makes judgments about content that creators have produced. The following constraints are non-negotiable:

| Principle | Implementation |
| :---- | :---- |
| Viewer-centric, not creator-punitive | Evaluations are about video utility to the viewer, never about creator quality or character. Labels apply to individual videos, not to channels. |
| No public shaming | Evaluation data is never exposed publicly. There are no leaderboards, no creator scores, and no exportable reports that could be used to attack creators. |
| Transparent reasoning | Every classification includes a human-readable explanation. Users can always see why a video was flagged. |
| No permanent labels | Evaluations are versioned and expire. A video evaluated under v1 heuristics is re-evaluated when v2 ships. No video carries a permanent stigma. |
| User stays in control | Users can override any classification, adjust strictness, whitelist channels, and disable the extension per-page or globally. |
| Not a truth arbiter | TubeSage does not evaluate factual accuracy. It assesses informational density, manipulation tactics, and extractive patterns. It says “probably not worth your time,” not “this is false.” |

# **11\. Roadmap**

| Phase | Scope | Timeline |
| :---- | :---- | :---- |
| v1.0 — MVP | Chrome extension with feed indicators, early-warning toast, server-side LLM evaluation, PostgreSQL cache, basic heuristics. Anonymous users only. | Q2 2026 |
| v1.1 — Personalization | Personal value profiles, adjustable strictness, channel whitelisting, behavioral signal integration for personalized scoring. | Q3 2026 |
| v1.2 — Local Intelligence | On-device evaluation via WebGPU/WASM small model. Server becomes optional for most evaluations. Dramatically reduced server costs. | Q4 2026 |
| v2.0 — Platform Expansion | Support for additional platforms (X, podcast platforms, blogs). Community-aggregated signals (opt-in). Research/focus mode. | 2027 |

# **12\. Appendix**

## **12.1 Glossary**

| Term | Definition |
| :---- | :---- |
| Evaluation | The complete assessment of a video: classification label, confidence score, explanation, and signal breakdown |
| Signal | A measurable dimension of video quality (value density, manipulation, extractive behavior, language patterns) |
| Transcript Hash | SHA-256 hash of the cleaned, normalized transcript text; used as part of the cache key |
| Eval Version | A tagged release of the evaluation pipeline (prompt text \+ heuristic weights); part of the cache key |
| Early Warning | A fast, partial evaluation based on the first 60–120 seconds of transcript |
| Full Evaluation | A comprehensive evaluation based on the complete transcript (or first 10 minutes) |
| Heuristic | A lightweight, rule-based check that runs client-side (regex patterns, keyword frequency, n-gram analysis) |
| CTA | Call to Action — a prompt directing the viewer to subscribe, click, buy, or take a funnel action |

## **12.2 Technology Decision Log**

| Decision | Chosen | Alternatives Considered | Rationale |
| :---- | :---- | :---- | :---- |
| Primary Language | TypeScript (strict) | JavaScript, Go, Rust | Full-stack type safety across extension \+ API \+ shared packages; team familiarity; Prisma integration |
| Frontend Framework | React | Svelte, Vue, Vanilla | Extension popup/options benefit from component model; ecosystem maturity; team preference |
| ORM | Prisma | TypeORM, Drizzle, Sequelize, Knex | Best type-safety, multi-provider support, migration tooling, and DX for TypeScript projects |
| Primary Database | PostgreSQL | MongoDB, SQLite, MySQL | Relational model fits evaluation data; JSONB for flexibility; mature managed hosting options; Prisma’s strongest support |
| Job Queue | BullMQ \+ Redis | pg-boss, RabbitMQ, SQS | Simple setup; Redis serves double duty as cache \+ queue broker; great TypeScript SDK |
| Monorepo Tool | Turborepo \+ pnpm | Nx, Lerna, Yarn workspaces | Fast incremental builds; native pnpm integration; minimal configuration overhead |
| Testing | Vitest \+ Playwright | Jest \+ Puppeteer, Cypress | Vitest is faster and ESM-native; Playwright handles extension E2E better than alternatives |

