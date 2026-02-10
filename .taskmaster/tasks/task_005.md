# Task ID: 5

**Title:** Build evaluation pipeline package with heuristics and LLM integration

**Status:** pending

**Dependencies:** 2, 4

**Priority:** high

**Description:** Create the packages/evaluation package implementing the evaluation pipeline from PRD sections 6.1-6.4. Build the heuristics engine with four signal categories (Value Density, Manipulation & Clickbait, Extractive Behavior, Language Patterns). Implement the classification logic (HIGH_VALUE, MIXED, LOW_VALUE). Create the LLM prompt template system with versioning. Integrate with server-side LLM providers (OpenAI/Anthropic).

**Details:**

Implement heuristic analyzers: CTADensityAnalyzer (CTA phrases per minute), PromiseInflationAnalyzer (regex for hyperbolic patterns), RepetitionRateAnalyzer (n-gram analysis), EmotionalPrimingAnalyzer (manipulation keyword frequency). Each returns a normalized 0-1 score and evidence phrases. Implement ClassificationEngine with configurable thresholds from PRD 6.3. Create PromptTemplateManager for versioned prompt storage and rendering. Create LLMEvaluator that sends structured prompts and parses JSON responses. Support both Early Warning mode (partial transcript, fast) and Full Evaluation mode (complete transcript). All thresholds configurable via EvalVersion. Write comprehensive unit tests.

**Test Strategy:**

No test strategy provided.
