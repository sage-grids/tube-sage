# Task ID: 17

**Title:** Implement AI Provider Manager with Vercel AI SDK for client-side evaluation

**Status:** pending

**Dependencies:** 9, 5

**Priority:** high

**Description:** Build the AI Provider Manager module (ai/providers.ts) in the Chrome extension using the Vercel AI SDK. Support four providers: OpenAI (@ai-sdk/openai), OpenRouter (@ai-sdk/openrouter), Ollama (ollama-ai-provider), and OpenCode (@ai-sdk/openai with custom baseURL). This module handles provider instantiation, API key storage in chrome.storage.local, and executing LLM evaluations entirely in the browser. API keys never leave the browser.

**Details:**

Install Vercel AI SDK packages: ai, @ai-sdk/openai, @ai-sdk/openrouter, ollama-ai-provider. Create AIProviderManager class with methods: configureProvider(type, apiKey, baseUrl?, model?), testConnection() (minimal test prompt), executeEvaluation(transcript, promptTemplate) -> EvaluationResult, getProviderConfig() -> ProviderConfig | null, removeApiKey(). Store provider config in chrome.storage.local (API key encrypted at rest by Chrome). Create provider factory: createOpenAIProvider(apiKey), createOpenRouterProvider(apiKey), createOllamaProvider(baseUrl), createOpenCodeProvider(apiKey, baseUrl). Each returns a Vercel AI SDK provider instance. The executeEvaluation method constructs the prompt using the server-provided template, calls generateObject() from AI SDK, and returns parsed EvaluationResult. Ensure module isolation: this module is the ONLY code that accesses API keys, and it never exposes keys to any other module.

**Test Strategy:**

No test strategy provided.
