# Task ID: 21

**Title:** Implement JWT authentication flow

**Status:** pending

**Dependencies:** 6, 9

**Priority:** high

**Description:** Build the authentication system from PRD section 8.2. On first install, the extension generates a random UUID stored in chrome.storage.local. This UUID is exchanged for a short-lived JWT via a /auth/token endpoint on the API server. All subsequent API requests include the JWT in the Authorization header. Implement token refresh logic for expired JWTs.

**Details:**

Extension side: generate UUID v4 on first install (chrome.runtime.onInstalled), store in chrome.storage.local, implement AuthService with methods: getToken() (return cached JWT or exchange UUID for new one), refreshToken(), getAuthHeaders() -> { Authorization: 'Bearer ...' }. API side: create POST /auth/token endpoint that accepts { user_id: UUID } and returns { token: JWT, expires_in: number }. JWT payload: { sub: UUID, iat, exp }. Sign with server-side secret. Add authMiddleware to all /api/v1/* routes that validates JWT, extracts user_id, and attaches to request context. Handle expired tokens with 401 response. Extension auto-refreshes on 401.

**Test Strategy:**

No test strategy provided.
