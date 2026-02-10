# Task ID: 3

**Title:** Set up database package with Prisma schema and repository layer

**Status:** pending

**Dependencies:** 1, 2

**Priority:** high

**Description:** Create the packages/db package with Prisma ORM configuration for PostgreSQL. Define the complete Prisma schema from PRD section 3.4: Video, Evaluation, TranscriptSegment, UserPreference, BehavioralSignal, EvalVersion entities. Implement the repository pattern from PRD section 3.3 with interfaces and Prisma implementations. Set up Docker Compose for local PostgreSQL.

**Details:**

Create prisma/schema.prisma with all entities, enums (SegmentType, SignalType), relations, and indexes (composite unique on Evaluation, unique on youtube_id, compound index on BehavioralSignal). Create src/client.ts with singleton Prisma client and connection pooling. Define repository interfaces: IVideoRepository, IEvaluationRepository, ITranscriptRepository, IUserPreferenceRepository, IBehavioralSignalRepository, IEvalVersionRepository. Implement Prisma repositories for each interface. Create repository factory in src/repositories/index.ts. Add docker-compose.yml at repo root with PostgreSQL 16 and Redis 7 services. Run initial prisma migrate dev.

**Test Strategy:**

No test strategy provided.
