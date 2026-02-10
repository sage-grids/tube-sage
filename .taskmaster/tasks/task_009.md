# Task ID: 9

**Title:** Scaffold Chrome extension with Manifest V3 and React

**Status:** pending

**Dependencies:** 1, 2

**Priority:** high

**Description:** Create the apps/extension Chrome extension scaffold with Manifest V3. Set up the service worker (background.ts), content script entry (content/inject.ts), popup UI (React), and options page (React). Configure manifest.json with required permissions (activeTab, storage, tabs), content script matching for youtube.com, and CSP headers. Set up the build pipeline using Vite or webpack for bundling extension assets.

**Details:**

Create manifest.json (Manifest V3) with: permissions (activeTab, storage, tabs), host_permissions (*://*.youtube.com/*), content_scripts matching YouTube URLs, service_worker registration, popup and options page declarations, CSP headers. Set up build config (Vite with CRXJS or webpack) to bundle: background service worker, content script, popup React app, options React app. Create basic React shells for popup and options pages. Set up chrome.storage.local types. Configure hot-reload for development. Add extension-specific tsconfig extending shared config.

**Test Strategy:**

No test strategy provided.
