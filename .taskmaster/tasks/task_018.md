# Task ID: 18

**Title:** Implement Community Contribution Mode: client-side evaluation and result submission

**Status:** pending

**Dependencies:** 17, 14, 12, 7

**Priority:** high

**Description:** Build the full Community Contribution Mode flow from PRD section 5.4.4. When enabled and a cache miss occurs for a video the user is watching, the extension performs LLM evaluation client-side using the AI Provider Manager, renders results locally, and submits the evaluation to the server via POST /api/v1/evaluate/contribute. Fetch the current prompt template from GET /api/v1/evaluate/prompt-template and cache it locally.

**Details:**

Create ContributionService in the extension with methods: isEnabled() -> boolean, evaluateAndContribute(videoId, transcript, transcriptHash). Flow: 1) Check L1 cache miss, 2) Fetch prompt template from server (cache locally, refresh on eval_version change), 3) Call AIProviderManager.executeEvaluation() with transcript and prompt template, 4) Write result to L1 IndexedDB cache immediately, 5) Submit result to POST /api/v1/evaluate/contribute with: youtube_id, transcript_hash, eval_version, classification, confidence, explanation, signals, provider, model. 6) Handle response (ACCEPTED/REJECTED/PENDING_REVIEW). Wire into EvaluationOrchestrator: after L1 miss and before server batch request, check if contribution mode is active and execute client-side evaluation. If client-side evaluation succeeds, skip server request for that video. Add contribution toggle state to chrome.storage.local synced with options page.

**Test Strategy:**

No test strategy provided.
