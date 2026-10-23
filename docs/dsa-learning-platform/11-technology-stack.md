# Technology Stack

## 1. Frontend

| Need | Recommendation | Rationale |
| --- | --- | --- |
| Web framework | Next.js with App Router | Strong SSR/SSG, routing, API integration, deployment support. |
| Language | TypeScript | Shared types and safer refactoring. |
| UI | Tailwind CSS plus shadcn/ui or Radix primitives | Fast implementation with accessible components. |
| Server state | TanStack Query | Reliable caching, invalidation, and polling for submissions. |
| Forms | React Hook Form + Zod | Type-safe validation and good performance. |
| Code editor | Monaco Editor or CodeMirror 6 | Browser coding playground support. |
| Diagrams/content | MDX, Mermaid, rehype/remark plugins | Rich lessons and implementation-ready docs. |
| Visualizations | React + SVG/Canvas, D3 for complex graph layouts | Step-based DSA simulations. |
| Charts | Recharts or Tremor | Progress analytics and admin dashboards. |
| Testing | Vitest, Testing Library, Playwright | Unit, component, and end-to-end coverage. |

## 2. Backend

| Need | Recommendation | Rationale |
| --- | --- | --- |
| Runtime | Node.js 20+ | Mature ecosystem and TypeScript support. |
| API framework | NestJS or Fastify | NestJS for structured modules; Fastify for lean high-performance APIs. |
| Language | TypeScript | Shared schemas and safer service/repository boundaries. |
| Validation | Zod | Runtime validation and type inference. |
| ORM/query | Prisma, Drizzle, or Kysely | Typed database access; Kysely/Drizzle fit repository-heavy SQL control. |
| Queue | BullMQ with Redis | Submission judging, AI jobs, analytics aggregation. |
| Logging | Pino | Fast structured logs. |
| API docs | OpenAPI | Contract-first documentation and generated clients. |
| Testing | Vitest/Jest, Supertest, Testcontainers | Unit and integration tests with real dependencies. |

Recommended backend choice: **Fastify + TypeScript + Zod + Kysely** for explicit SQL, strong performance, and clean repository boundaries.

## 3. Database

| Need | Recommendation | Rationale |
| --- | --- | --- |
| Primary database | PostgreSQL | Relational integrity, indexing, JSONB, analytics-friendly queries. |
| Cache/queue | Redis | Queues, rate limiting, session metadata, leaderboard snapshots. |
| Vector search | pgvector or managed vector DB | AI tutor retrieval over lessons and problem explanations. |
| Migrations | dbmate, Prisma Migrate, Drizzle Kit, or Flyway | Reproducible schema evolution. |
| Backups | Managed PITR backups | Restore protection for learner progress and submissions. |

## 4. Cloud and infrastructure

| Need | Recommendation | Rationale |
| --- | --- | --- |
| Frontend hosting | Vercel | Excellent Next.js deployment path. |
| API hosting | AWS ECS/Fargate, Fly.io, Render, or Railway | Containerized Node API deployment. |
| Database | Supabase, Neon, RDS Postgres, or Crunchy Bridge | Managed PostgreSQL. |
| Redis | Upstash, Elasticache, or Railway Redis | Managed queue/cache dependency. |
| Object storage | S3-compatible storage | Lesson assets, exports, judge artifacts. |
| Sandbox workers | Isolated containers or microVMs on ECS/Kubernetes/Fly Machines | Safer untrusted code execution. |
| CDN | Cloudflare or Vercel Edge Network | Static assets and public lesson speed. |
| Observability | OpenTelemetry, Sentry, Grafana/Prometheus, managed logs | Error tracking, traces, metrics, and alerts. |

## 5. Authentication

| Need | Recommendation |
| --- | --- |
| Managed auth | Clerk, Auth0, Supabase Auth, or NextAuth/Auth.js |
| Session strategy | HTTP-only secure cookies for web plus short-lived access tokens |
| OAuth providers | Google, GitHub, Microsoft |
| Authorization | Role-based access control with `LEARNER`, `INSTRUCTOR`, `ADMIN`, `SYSTEM` |
| Security | MFA for admins, email verification, password reset, refresh token rotation |

Recommended auth choice: **Clerk or Supabase Auth** for speed of implementation; **Auth.js** if self-hosting and provider flexibility are more important.

## 6. AI integration

| Need | Recommendation | Rationale |
| --- | --- | --- |
| LLM provider | OpenAI, Anthropic, or Azure OpenAI | Strong reasoning and coding support. |
| Retrieval | pgvector or Pinecone/Qdrant | Ground tutor answers in platform content. |
| Prompt orchestration | Lightweight internal service | Keeps prompts, guardrails, and telemetry versioned. |
| Moderation | Provider safety APIs plus custom policy checks | Prevent hidden-test leaks and unsafe content. |
| Evaluation | Golden prompt set and regression tests | Detect prompt drift and poor hint behavior. |
| Cost controls | Per-user quotas, caching, token accounting | Predictable spend. |

## 7. Code execution stack

| Need | Recommendation |
| --- | --- |
| Isolation | Docker with strict seccomp/AppArmor for MVP; microVMs for higher assurance |
| Network | Disabled by default for submitted code |
| Limits | CPU time, wall time, memory, output size, process count |
| Languages | Python, JavaScript/TypeScript, Java for initial support |
| Job control | BullMQ job IDs mapped to submission IDs |
| Artifact storage | Store only safe summaries by default; redact large stderr/stdout |

## 8. Suggested MVP stack

- **Frontend:** Next.js, TypeScript, Tailwind CSS, shadcn/ui, TanStack Query, Monaco Editor.
- **Backend:** Node.js, Fastify, TypeScript, Zod, Kysely, OpenAPI.
- **Database:** PostgreSQL, Redis, pgvector.
- **Workers:** BullMQ, Docker-based judge workers, separate AI worker.
- **Cloud:** Vercel for frontend, AWS/Fly.io/Render for API and workers, managed Postgres and Redis.
- **Authentication:** Clerk or Supabase Auth.
- **AI:** OpenAI or Anthropic with retrieval over lesson/problem embeddings.
- **Observability:** Sentry, OpenTelemetry, structured logs, uptime checks.
