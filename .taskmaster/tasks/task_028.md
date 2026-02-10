# Task ID: 28

**Title:** Set up Docker deployment configuration for API and Worker

**Status:** pending

**Dependencies:** 6, 8

**Priority:** low

**Description:** Create production-ready Dockerfiles and docker-compose configurations for the API server and Worker service from PRD section 9.3. Include multi-stage builds for minimal image size, health checks, environment variable configuration, and orchestration with PostgreSQL and Redis dependencies.

**Details:**

Create apps/api/Dockerfile: multi-stage build (build stage with pnpm install + turbo prune + build, production stage with node:20-alpine + minimal dependencies). Create apps/worker/Dockerfile: same multi-stage pattern. Create docker-compose.prod.yml with services: api (builds apps/api, exposes port 3000, depends on postgres + redis), worker (builds apps/worker, depends on postgres + redis), postgres (postgres:16-alpine with volume), redis (redis:7-alpine with persistence). Add health check endpoints. Configure environment variables via .env.production.example. Add .dockerignore files. Ensure Prisma client is generated in Docker build.

**Test Strategy:**

No test strategy provided.
