# Feature Breakdown

## 1. Interactive Lessons

### Requirements

- Render MDX lessons with headings, callouts, diagrams, code blocks, and inline exercises.
- Link every lesson to a topic, prerequisites, difficulty, and estimated duration.
- Track reading progress, completion, time spent, and quiz checkpoints.
- Provide runnable code snippets where relevant.
- Support editorial preview before publication.

### Acceptance criteria

- Learner can open a topic, continue the next incomplete lesson, and mark completion.
- Lesson completion updates topic progress and recommendation state.
- Admin can publish/unpublish lessons without deployment.

## 2. Visual DSA Simulations

### Requirements

- Step forward/backward through algorithm states.
- Control speed, pause, reset, and input data.
- Explain each transition with a short textual annotation.
- Support at least arrays, sorting, searching, stacks, queues, recursion trees, binary trees, heaps, tries, graphs, and DP tables.

### Simulation state model

```ts
type SimulationStep = {
  index: number;
  title: string;
  description: string;
  highlights: string[];
  data: unknown;
};
```

### Acceptance criteria

- Learner can replay an algorithm with custom input.
- Each step has visible state and explanation.
- Simulation links back to lessons and practice problems.

## 3. Coding Playground

### Requirements

- In-browser editor with language selection.
- Starter code per problem and language.
- Run sample tests without creating a graded submission.
- Submit to hidden tests for grading.
- Show verdict, runtime, memory, failed test summary, and safe stderr.
- Preserve submission history.

### Supported initial languages

- JavaScript/TypeScript
- Python
- Java

### Acceptance criteria

- Submission transitions from `QUEUED` to final verdict.
- Accepted submission updates progress and awards points.
- Unsafe code is isolated from the API and database.

## 4. AI Tutor

### Requirements

- Answer concept questions using lesson and problem context.
- Provide progressive hints instead of full solutions by default.
- Explain wrong answers and complexity issues.
- Generate learner-specific review plans from progress data.
- Enforce rate limits and token budgets.
- Store interaction metadata for analytics and abuse detection.

### Guardrails

- Do not reveal hidden tests.
- Do not provide final code for active graded assessments.
- Cite retrieved lesson/problem sections when possible.
- Prefer Socratic guidance for interview-prep mode.

### Acceptance criteria

- AI responses include topic/problem context.
- Learner can request another hint level.
- Admin can inspect usage volume and safety flags.

## 5. Quiz System

### Requirements

- Multiple choice, multi-select, short answer, and complexity classification.
- Immediate feedback for lesson quizzes.
- Scored attempts for topic assessments.
- Question banks tagged by topic, difficulty, and concept.
- Randomized ordering where appropriate.

### Acceptance criteria

- Quiz completion contributes to mastery score.
- Assessment attempts are immutable after submission.
- Retake policy can be configured per assessment type.

## 6. Progress Analytics

### Learner dashboard

- Roadmap completion percentage.
- Topic mastery heatmap.
- Streaks and time spent.
- Recently failed problem patterns.
- Recommended next lesson/problem.
- Badges earned and available.

### Instructor/admin dashboard

- Active learners.
- Topic completion rates.
- Problem acceptance rates.
- Common wrong-answer clusters.
- Assessment distribution by cohort.
- Judge queue health and AI tutor usage.

### Acceptance criteria

- Learner sees only their own data.
- Instructor sees only assigned cohorts where applicable.
- Admin sees aggregate platform analytics.

## 7. Leaderboard

### Requirements

- Rank by weekly/monthly/all-time points.
- Optional participation and privacy controls.
- Segment by level to avoid discouraging beginners.
- Exclude suspicious or admin-created activity.
- Cache snapshots for efficient reads.

### Scoring inputs

| Action | Points |
| --- | --- |
| Complete lesson | 5 |
| Pass quiz | 10 |
| Solve easy problem | 10 |
| Solve medium problem | 20 |
| Solve hard problem | 35 |
| Complete assessment | 50 |
| Maintain daily streak | 5 |

### Acceptance criteria

- Leaderboard updates from aggregate snapshots, not expensive live queries.
- Users can hide display name from public rankings.
- Ties sort by fewer failed submissions, then earliest achievement time.
