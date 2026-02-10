# Task ID: 24

**Title:** Implement user preferences sync between extension and API

**Status:** pending

**Dependencies:** 6, 9, 21

**Priority:** medium

**Description:** Build the user preferences system from PRD sections 4.2 and 3.4. Users configure strictness level, hidden channels, and other settings in the extension popup/options. Preferences sync to the server via GET/PUT /api/v1/preferences and are stored in the UserPreference table.

**Details:**

Extension side: create PreferenceService that manages: strictness (number 0-1), hiddenChannels (string[]), privacyMode (boolean), contributionEnabled (boolean). Store locally in chrome.storage.local. Sync to server on change via PUT /api/v1/preferences. Load from server on extension startup via GET /api/v1/preferences (server is source of truth for cross-device sync readiness). API side: implement GET /api/v1/preferences (return user prefs by JWT user_id) and PUT /api/v1/preferences (upsert UserPreference record). Validate with Zod schema. Handle offline mode: queue preference updates and sync when connectivity returns.

**Test Strategy:**

No test strategy provided.
