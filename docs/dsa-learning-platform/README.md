# DSA Learning Platform Documentation

Implementation-ready documentation package for a Data Structures and Algorithms learning platform.

## Files

| File | Purpose |
| --- | --- |
| [01-prd.md](01-prd.md) | Product requirements, personas, scope, success metrics, releases, and risks. |
| [02-system-architecture.md](02-system-architecture.md) | Frontend, backend, database, API, and authentication architecture. |
| [03-learning-architecture.md](03-learning-architecture.md) | Topic dependency graph, roadmap, and beginner-to-advanced progression. |
| [04-database-schema.md](04-database-schema.md) | PostgreSQL schema for users, topics, lessons, problems, submissions, progress, assessments, and badges. |
| [05-feature-breakdown.md](05-feature-breakdown.md) | Feature requirements for lessons, simulations, playground, AI tutor, quizzes, analytics, and leaderboard. |
| [06-api-endpoints.md](06-api-endpoints.md) | REST API endpoint catalog grouped by authentication, lessons, progress, problems, submissions, and analytics. |
| [07-folder-structure.md](07-folder-structure.md) | Suggested Next.js frontend, Node.js backend, and PostgreSQL project structure. |
| [08-low-level-design.md](08-low-level-design.md) | Class diagrams, service layer boundaries, and repository pattern design. |
| [09-dsa-topic-guide.md](09-dsa-topic-guide.md) | Topic definitions, use cases, complexity notes, interview questions, and examples. |
| [10-diagrams.md](10-diagrams.md) | Mermaid architecture and learning flow diagrams. |
| [11-technology-stack.md](11-technology-stack.md) | Recommended frontend, backend, database, cloud, auth, and AI stack. |

## Recommended implementation order

1. Build authentication, user profiles, and topic catalog.
2. Implement lesson rendering, progress tracking, and quizzes.
3. Add coding playground, problem submissions, and judge execution.
4. Add simulations and topic visualizations.
5. Add AI tutor with retrieval over lessons, problems, and user progress.
6. Add analytics, badges, streaks, and leaderboard.
