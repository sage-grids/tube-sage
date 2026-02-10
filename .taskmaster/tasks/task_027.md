# Task ID: 27

**Title:** Write E2E tests for Chrome extension with Playwright

**Status:** pending

**Dependencies:** 15, 16, 19, 20, 18

**Priority:** medium

**Description:** Set up Playwright for end-to-end testing of the Chrome extension. Test the core user flows: feed page badge rendering, tooltip interactions, watch page early warning toast, popup UI interactions, options page AI provider configuration, and Community Contribution Mode flow.

**Details:**

Configure Playwright for Chrome extension testing (load unpacked extension). Test scenarios: 1) Feed page: navigate to YouTube home, verify badges appear on video cards, verify tooltip opens on badge click with explanation text. 2) Watch page: navigate to a video, verify early warning toast appears for known-bad content (mock API), verify toast auto-dismisses after 8s. 3) Popup: open popup, verify cache stats display, verify strictness slider persists changes. 4) Options: open options page, configure OpenAI provider (mock key), test connection button, enable contribution mode, verify trust messaging is visible. 5) Privacy mode: enable privacy mode, verify no server requests are made. Use Playwright fixtures for extension loading and YouTube page mocking.

**Test Strategy:**

No test strategy provided.
