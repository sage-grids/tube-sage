# TubeSage — Task Overview

28 tasks covering the full MVP implementation as defined in the Technical PRD.

## Foundation Layer (Tasks 1–2)

No dependencies — start here.

| ID | Task | Priority |
|---|---|---|
| 1 | Initialize monorepo with Turborepo + pnpm workspaces | High |
| 2 | Set up shared packages: types, constants, Zod schemas | High |

## Core Backend Packages (Tasks 3–5)

Depend on foundation.

| ID | Task | Priority | Dependencies |
|---|---|---|---|
| 3 | Database package: Prisma schema + repository layer | High | 1, 2 |
| 4 | Transcript processing package | High | 1, 2 |
| 5 | Evaluation pipeline: heuristics + LLM integration | High | 2, 4 |

## Backend Services (Tasks 6–8)

Depend on core packages.

| ID | Task | Priority | Dependencies |
|---|---|---|---|
| 6 | API server with core evaluation endpoints | High | 3, 5 |
| 7 | Contributed evaluation endpoint + trust system | High | 6 |
| 8 | Background worker with BullMQ | High | 3, 5 |

## Extension Core (Tasks 9–14)

Extension scaffold + core features.

| ID | Task | Priority | Dependencies |
|---|---|---|---|
| 9 | Chrome extension scaffold (Manifest V3 + React) | High | 1, 2 |
| 10 | Content script: video discovery + DOM observer | High | 9 |
| 11 | Client-side transcript acquisition | High | 9, 4 |
| 12 | IndexedDB evaluation cache (L1) | High | 9 |
| 13 | Client-side heuristic scoring | Medium | 9, 5, 11 |
| 14 | Service worker: API communication + orchestration | High | 9, 12, 6 |

## Extension UI (Tasks 15–16, 19–20)

User-facing surfaces.

| ID | Task | Priority | Dependencies |
|---|---|---|---|
| 15 | Feed overlay: video card badges + tooltips | High | 10, 14 |
| 16 | Watch page early warning toast | High | 11, 13, 14 |
| 19 | Popup UI: settings + cache stats | Medium | 9, 12 |
| 20 | Options page: AI providers, privacy mode, data export | High | 9, 17 |

## Community Contribution Mode (Tasks 17–18)

Client-side AI evaluation via Vercel AI SDK.

| ID | Task | Priority | Dependencies |
|---|---|---|---|
| 17 | AI Provider Manager (Vercel AI SDK: OpenAI, OpenRouter, Ollama, OpenCode) | High | 9, 5 |
| 18 | Community Contribution Mode: full evaluation + submission flow | High | 17, 14, 12, 7 |

## Cross-Cutting Concerns (Tasks 21–24)

Auth, caching, signals, preferences.

| ID | Task | Priority | Dependencies |
|---|---|---|---|
| 21 | JWT authentication flow | High | 6, 9 |
| 22 | Redis L2 cache layer | High | 6, 3 |
| 23 | Behavioral signals collection | Medium | 6, 9, 14 |
| 24 | User preferences sync | Medium | 6, 9, 21 |

## Quality & Deployment (Tasks 25–28)

CI/CD, tests, Docker.

| ID | Task | Priority | Dependencies |
|---|---|---|---|
| 25 | CI/CD pipeline (GitHub Actions) | Medium | 1 |
| 26 | Backend unit + integration tests | Medium | 4, 5, 3, 6, 8 |
| 27 | E2E tests (Playwright) | Medium | 15, 16, 19, 20, 18 |
| 28 | Docker deployment configuration | Low | 6, 8 |

## Critical Path

```
1 → 2 → (3 + 4) → 5 → 6 → 7 ─┐
                                ├→ 18 (Community Contribution Mode)
1 → 2 → 9 → 12 → 14 → 17 ────┘
```

## Key Stats

- **Total tasks:** 28
- **High priority:** 20
- **Medium priority:** 7
- **Low priority:** 1
- **Most depended-on task:** #9 — Chrome extension scaffold (11 dependents)
- **Recommended starting point:** Task #1
