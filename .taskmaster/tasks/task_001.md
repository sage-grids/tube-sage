# Task ID: 1

**Title:** Initialize monorepo with Turborepo and pnpm workspaces

**Status:** pending

**Dependencies:** None

**Priority:** high

**Description:** Set up the monorepo structure as defined in PRD section 2.2. Initialize Turborepo with pnpm workspaces. Create all package directories: apps/extension, apps/api, apps/worker, packages/evaluation, packages/transcripts, packages/db, packages/shared, packages/config. Configure turbo.json with build/dev/lint/test pipelines.

**Details:**

Create pnpm-workspace.yaml listing all apps/* and packages/*. Initialize turbo.json with pipeline definitions for build, dev, lint, type-check, and test. Each package gets its own package.json and tsconfig.json. The packages/config directory should contain shared tsconfig.base.json, eslint.config.js, and prettier config. All packages use TypeScript strict mode. Add .npmrc for pnpm configuration.

**Test Strategy:**

No test strategy provided.
