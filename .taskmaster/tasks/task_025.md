# Task ID: 25

**Title:** Set up CI/CD pipeline with GitHub Actions

**Status:** pending

**Dependencies:** 1

**Priority:** medium

**Description:** Configure the CI/CD pipeline from PRD section 9.2. Set up GitHub Actions workflows for linting (ESLint + Prettier), type-checking, unit tests, and integration tests (against Docker PostgreSQL + Redis). Configure extension build to produce a .zip artifact. Set up Docker container builds for API and Worker services.

**Details:**

Create .github/workflows/ci.yml with jobs: lint (pnpm lint across all packages), type-check (pnpm type-check), unit-test (vitest run), integration-test (docker-compose up PostgreSQL + Redis, run prisma migrate deploy, run integration tests). Create .github/workflows/extension-build.yml that builds the extension and uploads .zip artifact. Create .github/workflows/deploy.yml for API/Worker Docker builds. Set up Turborepo remote caching for CI. Configure Dependabot for dependency updates. Add pnpm audit step for security. Create Dockerfiles for apps/api and apps/worker.

**Test Strategy:**

No test strategy provided.
