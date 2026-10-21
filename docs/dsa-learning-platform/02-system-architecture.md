# System Architecture

## 1. High-level architecture

The platform uses a modular web architecture:

- **Next.js frontend** renders lessons, dashboards, visualizations, and the coding playground.
- **Node.js API** owns business rules, authentication authorization, curriculum delivery, progress, submissions, and analytics reads.
- **PostgreSQL** stores users, content metadata, progress, submissions, assessments, badges, and analytics aggregates.
- **Judge workers** execute code in isolated sandboxes and publish verdicts.
- **AI tutor service** wraps LLM calls, retrieval, guardrails, prompt templates, and token accounting.
- **Object storage** stores large lesson assets, diagrams, exported reports, and optional submission artifacts.

## 2. Frontend architecture

### Framework

- Next.js App Router with TypeScript.
- Server Components for static curriculum pages and SEO-friendly public content.
- Client Components for editor, simulations, dashboards, quiz interactions, and authenticated flows.

### Major modules

| Module | Responsibility |
| --- | --- |
| `app/(marketing)` | Landing pages, pricing, public curriculum preview. |
| `app/(auth)` | Sign in, sign up, password reset, OAuth callback. |
| `app/(learn)` | Topic pages, lesson reader, roadmap, quizzes. |
| `app/(practice)` | Problem list, problem detail, editor, submissions. |
| `app/(dashboard)` | Progress analytics, badges, leaderboard, recommendations. |
| `components/simulations` | Reusable visual DSA simulation components. |
| `lib/api` | Typed API client, request wrappers, auth token handling. |
| `lib/editor` | Monaco/CodeMirror integration, language templates, test runner UI. |

### State management

- Server data: TanStack Query or React Server Actions backed by typed API clients.
- Local UI state: React state and URL state.
- Auth session: secure cookie for SSR plus client session context.
- Editor state: local component state with explicit save/submit actions.

### Rendering strategy

- Public and stable lesson content: static generation or cached server rendering.
- Personalized dashboards/progress: authenticated dynamic rendering.
- Coding playground: client-heavy page with server APIs for run/submit.

## 3. Backend architecture

### Services

| Service | Responsibility |
| --- | --- |
| Auth service | Session validation, token refresh, roles, account lifecycle. |
| Curriculum service | Topics, lessons, prerequisites, roadmap queries. |
| Progress service | Lesson completion, mastery score, streaks, recommendations. |
| Problem service | Problem catalog, constraints, examples, language templates. |
| Submission service | Submission creation, status transitions, verdict persistence. |
| Judge service | Queue orchestration and sandbox execution results. |
| Assessment service | Quiz attempts, scored assessments, retake policy. |
| Badge service | Badge rule evaluation and awards. |
| AI tutor service | Retrieval, prompt construction, LLM calls, usage limits. |
| Analytics service | Learner and admin metrics. |

### Runtime components

- API server: stateless Node.js process.
- Submission queue: durable queue for judge jobs.
- Judge workers: horizontally scalable, network-restricted execution workers.
- AI workers: optional queue for long-running tutoring or code-review tasks.
- Analytics jobs: scheduled aggregation for dashboards and leaderboards.

## 4. Database design principles

- PostgreSQL is the source of truth.
- UUID primary keys for externally referenced records.
- `created_at` and `updated_at` timestamps on mutable tables.
- Referential integrity through foreign keys.
- Soft deletion for user-authored content where auditability matters.
- JSONB only for flexible metadata; core queryable fields stay normalized.
- Indexes on foreign keys, slugs, status fields, and dashboard query paths.

## 5. API design

### Style

- REST-first API under `/api/v1`.
- JSON request and response bodies.
- Zod/OpenAPI schemas for validation and documentation.
- Cursor-based pagination for large lists.
- Idempotency keys for submissions and state-changing operations that clients may retry.

### Standard response envelope

```json
{
  "data": {},
  "meta": {
    "requestId": "req_123"
  }
}
```

### Error response

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request body",
    "details": [],
    "requestId": "req_123"
  }
}
```

### Common status codes

| Code | Meaning |
| --- | --- |
| `200` | Successful read/update. |
| `201` | Resource created. |
| `202` | Submission accepted for async judging. |
| `400` | Invalid request. |
| `401` | Missing or invalid authentication. |
| `403` | Authenticated but not authorized. |
| `404` | Resource not found or not visible to caller. |
| `409` | Duplicate or invalid state transition. |
| `422` | Semantically invalid answer/submission. |
| `429` | Rate limited. |
| `500` | Unexpected server error. |

## 6. Authentication flow

1. User signs up with email/password or OAuth provider.
2. Auth provider creates identity and returns access and refresh tokens.
3. Frontend stores session in secure HTTP-only cookies where possible.
4. Frontend calls API with `Authorization: Bearer <access_token>`.
5. API verifies token signature, issuer, audience, expiry, and user status.
6. API loads user profile and role.
7. API checks role permissions for protected routes.
8. Refresh token flow renews expired sessions without exposing long-lived tokens to application code.
9. Logout clears session cookies and revokes refresh tokens.

## 7. Authorization model

| Role | Capabilities |
| --- | --- |
| `LEARNER` | Read curriculum, submit code, take quizzes, view own progress. |
| `INSTRUCTOR` | View assigned learners/cohorts, create assignments, inspect analytics. |
| `ADMIN` | Manage content, users, badges, assessments, and platform settings. |
| `SYSTEM` | Internal workers updating submission verdicts, analytics, and badge awards. |

## 8. Submission lifecycle

1. Learner submits source code for a problem and language.
2. API validates problem availability and creates `submissions` row with `QUEUED`.
3. API publishes judge job with submission ID.
4. Judge worker loads problem tests and language runtime.
5. Worker executes code in sandbox with CPU, memory, and time limits.
6. Worker records verdict, runtime, memory, per-test summary, and stderr.
7. Progress service updates mastery and problem completion.
8. Badge service evaluates award rules.
9. Analytics job updates aggregates asynchronously.
