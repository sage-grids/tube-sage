# Task ID: 23

**Title:** Implement behavioral signals collection

**Status:** pending

**Dependencies:** 6, 9, 14

**Priority:** medium

**Description:** Build the behavioral signal collection system from PRD sections 4.2 and 3.4. The extension detects implicit user signals (early exit, long watch, hide) and submits them in batches via POST /api/v1/signals. The API processes signals asynchronously and stores them in the BehavioralSignal table for future personalization.

**Details:**

Extension side: create SignalCollector that detects: EARLY_EXIT (user leaves watch page within first 30s), LONG_WATCH (user watches >80% of video), HIDE (user clicks hide on a video card). Batch signals and fire-and-forget to POST /api/v1/signals every 30 seconds or on page unload. API side: implement /api/v1/signals endpoint that accepts { signals: [{ video_id, signal_type, timestamp }] }, validates via Zod, and writes to BehavioralSignal table asynchronously (fire-and-forget response, process in background). Signals are associated with anonymous user_id from JWT.

**Test Strategy:**

No test strategy provided.
