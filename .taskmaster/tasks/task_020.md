# Task ID: 20

**Title:** Build options page: AI provider configuration, privacy mode, and data export

**Status:** pending

**Dependencies:** 9, 17

**Priority:** high

**Description:** Build the React options page for the Chrome extension. Include the AI Providers configuration section from PRD 5.4.2 (provider selector, API key input, base URL override, model selector, test connection, contribution toggle), privacy mode toggle, API endpoint configuration, and data export. Display persistent API key safety messaging from PRD 5.4.3.

**Details:**

Create React app in options/ directory. Major sections: 1) AI Providers section: provider dropdown (OpenAI, OpenRouter, Ollama, OpenCode), masked API key input (hidden for Ollama), optional base URL field (default localhost:11434 for Ollama), model ID text field, 'Test Connection' button (calls AIProviderManager.testConnection()), Community Contribution toggle with label text from PRD, 'Remove API Key' button. 2) Persistent trust notice: non-dismissible banner stating 'Your API keys are stored securely in your browser and are never sent to TubeSage servers. All AI calls are made directly from your browser to your chosen provider.' 3) Privacy Mode toggle (disables all server communication). 4) Server endpoint configuration (custom API URL). 5) Data export (export evaluation cache and preferences as JSON). Wire all settings to chrome.storage.local. Validate API key format per provider before saving.

**Test Strategy:**

No test strategy provided.
