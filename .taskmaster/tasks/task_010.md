# Task ID: 10

**Title:** Implement content script: video discovery and DOM mutation observer

**Status:** pending

**Dependencies:** 9

**Priority:** high

**Description:** Build the content script that injects into YouTube pages and discovers video cards. Implement MutationObserver to watch for new video card elements across all YouTube surfaces (home feed, search results, subscriptions, sidebar recommendations). Extract youtube_id values from href attributes on video card elements. Handle YouTube's SPA navigation (pushState/popState) to re-scan on page transitions.

**Details:**

Create content/inject.ts as the main content script entry. Implement VideoDiscoveryService with MutationObserver watching for ytd-video-renderer, ytd-rich-item-renderer, ytd-compact-video-renderer elements. Extract video IDs from /watch?v= href patterns. Handle YouTube SPA navigation by listening to yt-navigate-finish events and popstate. Debounce rapid DOM mutations. Maintain a Set of discovered video IDs to avoid duplicate processing. Expose a callback/event system for downstream consumers (cache lookup, evaluation request). Handle edge cases: shorts, playlists, live streams (skip or flag differently).

**Test Strategy:**

No test strategy provided.
