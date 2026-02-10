# Task ID: 7

**Title:** Implement contributed evaluation endpoint and trust system

**Status:** pending

**Dependencies:** 6

**Priority:** high

**Description:** Add the POST /api/v1/evaluate/contribute and GET /api/v1/evaluate/prompt-template endpoints to the API server. Implement the contributed evaluation trust and validation system from PRD section 5.4.5: schema validation via Zod, eval_version matching, trust scoring per contributor, rate limiting (30/hour/user), and anomaly detection for outlier signal scores.

**Details:**

Create ContributionService with methods: validateContribution() (Zod schema check + eval_version match), scoreTrust() (compare against existing server evaluations for same videos, build per-user trust score), detectAnomalies() (flag signal scores that are statistical outliers). Add contributor_trust_score column to UserPreference or create new ContributorProfile entity. New contributors start at low trust; evaluations stored but only served to others after cross-validation. Rate limit: 30 contributions/hour/user. The prompt-template endpoint returns the current prompt template text and eval_version tag, cacheable until version bumps. Add response statuses: ACCEPTED, REJECTED, PENDING_REVIEW with reason field.

**Test Strategy:**

No test strategy provided.
