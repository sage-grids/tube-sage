# Task ID: 13

**Title:** Implement client-side heuristic scoring in the extension

**Status:** pending

**Dependencies:** 9, 5, 11

**Priority:** medium

**Description:** Port the lightweight heuristic analyzers from packages/evaluation to run in the browser content script. Implement the four heuristic checks from PRD section 5.2 Step 4: CTA density, promise inflation, repetition rate, and emotional priming. These run on the client before any server request and can render preliminary indicators if confidence is high enough.

**Details:**

Reuse or adapt heuristic analyzers from packages/evaluation for browser context (ensure no Node.js-only APIs). Implement: CTADensityAnalyzer (count CTA phrases per minute of transcript), PromiseInflationAnalyzer (regex patterns for hyperbolic framing), RepetitionRateAnalyzer (n-gram frequency analysis for low information density), EmotionalPrimingAnalyzer (manipulation keyword frequency). Create HeuristicEngine that runs all four and produces a preliminary SignalBreakdown with confidence score. If confidence exceeds configurable threshold, emit a preliminary-evaluation event that the UI can render immediately. This provides instant feedback without waiting for server or LLM.

**Test Strategy:**

No test strategy provided.
