# Task ID: 11

**Title:** Implement client-side transcript acquisition

**Status:** pending

**Dependencies:** 9, 4

**Priority:** high

**Description:** Build the transcript acquisition system in the content script for watch pages. Implement two strategies from PRD section 5.2 Step 3: Option A (DOM Caption Stream) observing live caption elements for the first 30-90 seconds, and Option B (TimedText Endpoint Fetch) discovering caption track metadata and fetching full caption XML/JSON from YouTube's timedtext endpoints. Use packages/transcripts for cleaning and hashing.

**Details:**

Implement CaptionStreamObserver (Option A): observe .ytp-caption-segment elements via MutationObserver, stream timed text as it appears, collect first 30-90 seconds of transcript with zero network cost. Implement TimedTextFetcher (Option B): extract caption track metadata from ytInitialPlayerResponse or player config, construct timedtext API URL, fetch and parse caption XML/JSON. Create TranscriptAcquisitionService that uses Option A when available and falls back to Option B. Integrate packages/transcripts for cleanTranscript() and hashTranscript(). Emit transcript-ready events for downstream consumers.

**Test Strategy:**

No test strategy provided.
