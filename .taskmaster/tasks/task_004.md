# Task ID: 4

**Title:** Build transcript processing package

**Status:** pending

**Dependencies:** 1, 2

**Priority:** high

**Description:** Create the packages/transcripts package with utilities for transcript fetching, cleaning, chunking, and hashing. Implement transcript text cleaning (strip timestamps, normalize punctuation, collapse whitespace), SHA-256 hashing, and chunking into segments (EARLY: first 60-120s, FULL: up to 10 minutes). Support both raw caption XML/JSON parsing and plain text input.

**Details:**

Implement: parseTimedTextXML() for YouTube timedtext XML format, parseTimedTextJSON() for JSON format, cleanTranscript() for text normalization, hashTranscript() using SHA-256, chunkTranscript() to split into EARLY and FULL segments with millisecond timestamps, extractPlainText() to get raw text from timed segments. All functions should be pure and side-effect-free for use in both browser and Node.js. Export types: TranscriptSegment, TranscriptChunk, SegmentType. Write unit tests with Vitest.

**Test Strategy:**

No test strategy provided.
