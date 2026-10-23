# API Endpoints

Base path: `/api/v1`

All authenticated endpoints require:

```http
Authorization: Bearer <access_token>
```

## 1. Authentication

| Method | Path | Auth | Description |
| --- | --- | --- | --- |
| `POST` | `/auth/register` | Public | Create learner account. |
| `POST` | `/auth/login` | Public | Exchange credentials for session tokens. |
| `POST` | `/auth/oauth/callback` | Public | Complete OAuth provider callback. |
| `POST` | `/auth/refresh` | Refresh token | Rotate access token. |
| `POST` | `/auth/logout` | User | Revoke refresh token and clear session. |
| `GET` | `/auth/me` | User | Return current user profile and role. |
| `PATCH` | `/auth/me` | User | Update display name, avatar, timezone, preferred language. |

### Register request

```json
{
  "email": "learner@example.com",
  "password": "correct-horse-battery-staple",
  "displayName": "Learner"
}
```

## 2. Lessons

| Method | Path | Auth | Description |
| --- | --- | --- | --- |
| `GET` | `/topics` | Optional | List published topics in roadmap order. |
| `GET` | `/topics/:topicSlug` | Optional | Get topic details, prerequisites, lessons, and problems. |
| `GET` | `/topics/:topicSlug/lessons` | Optional | List lessons for a topic. |
| `GET` | `/lessons/:lessonSlug` | Optional | Get lesson content and linked simulation metadata. |
| `POST` | `/lessons/:lessonId/complete` | User | Mark lesson complete and update progress. |
| `GET` | `/lessons/:lessonId/quiz` | User | Get lesson quiz questions. |
| `POST` | `/lessons/:lessonId/quiz/attempts` | User | Submit quiz answers. |

## 3. Progress

| Method | Path | Auth | Description |
| --- | --- | --- | --- |
| `GET` | `/progress` | User | Get learner roadmap progress. |
| `GET` | `/progress/topics/:topicId` | User | Get detailed progress for one topic. |
| `PATCH` | `/progress/topics/:topicId` | System/Admin | Update computed mastery state. |
| `GET` | `/progress/recommendations` | User | Get next lessons/problems. |
| `GET` | `/progress/streak` | User | Get current and historical streak data. |

### Progress response

```json
{
  "data": {
    "overallCompletion": 42,
    "topics": [
      {
        "topicId": "uuid",
        "slug": "arrays",
        "status": "IN_PROGRESS",
        "masteryScore": 65,
        "lessonsCompleted": 3,
        "problemsSolved": 8
      }
    ]
  }
}
```

## 4. Problems

| Method | Path | Auth | Description |
| --- | --- | --- | --- |
| `GET` | `/problems` | Optional | List published problems with filters. |
| `GET` | `/problems/:problemSlug` | Optional | Get problem statement, examples, constraints, and starter code. |
| `GET` | `/problems/:problemId/hints` | User | Get progressive hints. |
| `GET` | `/problems/:problemId/submissions` | User | List current user's submissions for a problem. |
| `POST` | `/admin/problems` | Admin | Create problem. |
| `PATCH` | `/admin/problems/:problemId` | Admin | Update problem metadata/content. |
| `POST` | `/admin/problems/:problemId/publish` | Admin | Publish problem. |

### Problem filters

```http
GET /api/v1/problems?topic=arrays&difficulty=EASY&tag=two-pointers&status=unsolved
```

## 5. Submissions

| Method | Path | Auth | Description |
| --- | --- | --- | --- |
| `POST` | `/submissions/run` | User | Run sample tests without grading. |
| `POST` | `/submissions` | User | Create graded submission. |
| `GET` | `/submissions/:submissionId` | User | Get submission status and result. |
| `GET` | `/submissions` | User | List user's submission history. |
| `POST` | `/internal/submissions/:submissionId/start` | System | Mark judge job running. |
| `POST` | `/internal/submissions/:submissionId/result` | System | Persist final judge verdict. |

### Create submission request

```json
{
  "problemId": "uuid",
  "language": "python",
  "sourceCode": "def solve(nums):\n    return sum(nums)",
  "idempotencyKey": "client-generated-key"
}
```

### Submission response

```json
{
  "data": {
    "id": "uuid",
    "status": "QUEUED",
    "createdAt": "2026-10-21T22:00:00Z"
  }
}
```

## 6. Assessments and quizzes

| Method | Path | Auth | Description |
| --- | --- | --- | --- |
| `GET` | `/assessments` | User | List available assessments. |
| `POST` | `/assessments/:assessmentId/start` | User | Start attempt. |
| `POST` | `/assessments/:assessmentId/submit` | User | Submit answers and score attempt. |
| `GET` | `/assessments/:assessmentId/results` | User | View own assessment result. |
| `GET` | `/admin/assessments/analytics` | Admin | Aggregate assessment performance. |

## 7. AI tutor

| Method | Path | Auth | Description |
| --- | --- | --- | --- |
| `POST` | `/ai/tutor/messages` | User | Ask contextual lesson/problem question. |
| `POST` | `/ai/tutor/hints` | User | Request progressive hint for a problem. |
| `POST` | `/ai/tutor/review-submission` | User | Get feedback on own submission. |
| `GET` | `/ai/tutor/usage` | User | View usage limits and remaining quota. |

## 8. Analytics

| Method | Path | Auth | Description |
| --- | --- | --- | --- |
| `GET` | `/analytics/me` | User | Personal learning analytics. |
| `GET` | `/analytics/topics/:topicId` | User | Topic-specific metrics for the current learner. |
| `GET` | `/analytics/leaderboard` | User | Public/opt-in leaderboard snapshot. |
| `GET` | `/admin/analytics/overview` | Admin | Platform-wide dashboard metrics. |
| `GET` | `/admin/analytics/topics` | Admin | Topic completion and weak-area analytics. |
| `GET` | `/admin/analytics/problems` | Admin | Problem acceptance and failure rates. |

## 9. Pagination

```http
GET /api/v1/problems?limit=25&cursor=eyJpZCI6...
```

```json
{
  "data": [],
  "meta": {
    "nextCursor": "eyJpZCI6...",
    "hasMore": true
  }
}
```

## 10. Rate limits

| Endpoint group | Suggested limit |
| --- | --- |
| Auth attempts | 10 per IP per minute |
| AI tutor | 30 messages per user per hour |
| Code run | 30 runs per user per hour |
| Graded submissions | 60 submissions per user per hour |
| Public reads | 300 requests per IP per minute |
