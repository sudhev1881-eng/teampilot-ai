# Database Schema

PostgreSQL schema for the DSA learning platform. The required tables are included directly: users, topics, lessons, coding problems, submissions, progress tracking, assessments, and badges.

## 1. Extensions

```sql
create extension if not exists pgcrypto;
create extension if not exists citext;
```

## 2. Enums

```sql
create type user_role as enum ('LEARNER', 'INSTRUCTOR', 'ADMIN');
create type difficulty_level as enum ('BEGINNER', 'EASY', 'MEDIUM', 'HARD', 'ADVANCED');
create type topic_status as enum ('LOCKED', 'AVAILABLE', 'IN_PROGRESS', 'PRACTICE_READY', 'MASTERED', 'NEEDS_REVIEW');
create type problem_status as enum ('DRAFT', 'PUBLISHED', 'ARCHIVED');
create type submission_status as enum ('QUEUED', 'RUNNING', 'ACCEPTED', 'WRONG_ANSWER', 'TIME_LIMIT_EXCEEDED', 'MEMORY_LIMIT_EXCEEDED', 'RUNTIME_ERROR', 'COMPILE_ERROR', 'INTERNAL_ERROR');
create type assessment_type as enum ('QUIZ', 'TOPIC_TEST', 'MILESTONE_TEST', 'MOCK_INTERVIEW');
```

## 3. Users

```sql
create table users (
  id uuid primary key default gen_random_uuid(),
  email citext not null unique,
  display_name text not null,
  avatar_url text,
  role user_role not null default 'LEARNER',
  auth_provider text not null default 'email',
  auth_subject text unique,
  timezone text not null default 'UTC',
  preferred_language text not null default 'javascript',
  is_active boolean not null default true,
  last_login_at timestamptz,
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now()
);

create index users_role_idx on users (role);
create index users_active_idx on users (is_active);
```

## 4. Topics

```sql
create table topics (
  id uuid primary key default gen_random_uuid(),
  slug text not null unique,
  title text not null,
  description text not null,
  difficulty difficulty_level not null,
  sequence_order int not null,
  estimated_minutes int not null default 60,
  prerequisites uuid[] not null default '{}',
  learning_objectives text[] not null default '{}',
  is_published boolean not null default false,
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now(),
  constraint topics_sequence_order_unique unique (sequence_order),
  constraint topics_estimated_minutes_positive check (estimated_minutes > 0)
);

create index topics_published_order_idx on topics (is_published, sequence_order);
```

## 5. Lessons

```sql
create table lessons (
  id uuid primary key default gen_random_uuid(),
  topic_id uuid not null references topics(id) on delete cascade,
  slug text not null,
  title text not null,
  summary text not null,
  body_mdx text not null,
  sequence_order int not null,
  estimated_minutes int not null default 15,
  difficulty difficulty_level not null default 'BEGINNER',
  simulation_key text,
  quiz_required boolean not null default false,
  is_published boolean not null default false,
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now(),
  unique (topic_id, slug),
  unique (topic_id, sequence_order),
  constraint lessons_estimated_minutes_positive check (estimated_minutes > 0)
);

create index lessons_topic_order_idx on lessons (topic_id, sequence_order);
```

## 6. Coding Problems

```sql
create table coding_problems (
  id uuid primary key default gen_random_uuid(),
  topic_id uuid not null references topics(id) on delete restrict,
  slug text not null unique,
  title text not null,
  statement_mdx text not null,
  difficulty difficulty_level not null,
  constraints_mdx text not null,
  input_format_mdx text,
  output_format_mdx text,
  examples jsonb not null default '[]',
  hidden_tests_ref text,
  supported_languages text[] not null default array['javascript', 'python', 'java'],
  starter_code jsonb not null default '{}',
  official_solution_mdx text,
  hints text[] not null default '{}',
  tags text[] not null default '{}',
  time_limit_ms int not null default 2000,
  memory_limit_mb int not null default 256,
  points int not null default 10,
  status problem_status not null default 'DRAFT',
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now(),
  constraint coding_problems_limits_positive check (time_limit_ms > 0 and memory_limit_mb > 0 and points >= 0)
);

create index coding_problems_topic_difficulty_idx on coding_problems (topic_id, difficulty);
create index coding_problems_status_idx on coding_problems (status);
create index coding_problems_tags_gin_idx on coding_problems using gin (tags);
```

## 7. Submissions

```sql
create table submissions (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references users(id) on delete cascade,
  problem_id uuid not null references coding_problems(id) on delete cascade,
  language text not null,
  source_code text not null,
  status submission_status not null default 'QUEUED',
  runtime_ms int,
  memory_kb int,
  test_results jsonb not null default '[]',
  compiler_output text,
  stderr text,
  score numeric(5,2) not null default 0,
  idempotency_key text,
  judged_at timestamptz,
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now(),
  unique (user_id, idempotency_key),
  constraint submissions_score_range check (score >= 0 and score <= 100)
);

create index submissions_user_created_idx on submissions (user_id, created_at desc);
create index submissions_problem_status_idx on submissions (problem_id, status);
create index submissions_queue_idx on submissions (status, created_at) where status in ('QUEUED', 'RUNNING');
```

## 8. Progress Tracking

```sql
create table progress_tracking (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references users(id) on delete cascade,
  topic_id uuid not null references topics(id) on delete cascade,
  status topic_status not null default 'AVAILABLE',
  lessons_completed int not null default 0,
  problems_attempted int not null default 0,
  problems_solved int not null default 0,
  quiz_average numeric(5,2) not null default 0,
  mastery_score numeric(5,2) not null default 0,
  current_streak_days int not null default 0,
  total_time_spent_minutes int not null default 0,
  last_activity_at timestamptz,
  completed_at timestamptz,
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now(),
  unique (user_id, topic_id),
  constraint progress_non_negative check (
    lessons_completed >= 0 and
    problems_attempted >= 0 and
    problems_solved >= 0 and
    current_streak_days >= 0 and
    total_time_spent_minutes >= 0
  ),
  constraint progress_percent_ranges check (
    quiz_average >= 0 and quiz_average <= 100 and
    mastery_score >= 0 and mastery_score <= 100
  )
);

create index progress_user_status_idx on progress_tracking (user_id, status);
create index progress_topic_mastery_idx on progress_tracking (topic_id, mastery_score desc);
```

## 9. Assessments

```sql
create table assessments (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references users(id) on delete cascade,
  topic_id uuid references topics(id) on delete set null,
  type assessment_type not null,
  title text not null,
  questions jsonb not null,
  answers jsonb not null default '{}',
  score numeric(5,2) not null default 0,
  max_score numeric(5,2) not null default 100,
  passed boolean not null default false,
  started_at timestamptz not null default now(),
  submitted_at timestamptz,
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now(),
  constraint assessments_score_valid check (score >= 0 and max_score > 0 and score <= max_score)
);

create index assessments_user_created_idx on assessments (user_id, created_at desc);
create index assessments_topic_type_idx on assessments (topic_id, type);
```

## 10. Badges

```sql
create table badges (
  id uuid primary key default gen_random_uuid(),
  slug text not null unique,
  title text not null,
  description text not null,
  icon_key text not null,
  rule_key text not null,
  rule_config jsonb not null default '{}',
  points int not null default 0,
  is_active boolean not null default true,
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now()
);

create table user_badges (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references users(id) on delete cascade,
  badge_id uuid not null references badges(id) on delete cascade,
  awarded_at timestamptz not null default now(),
  award_context jsonb not null default '{}',
  unique (user_id, badge_id)
);

create index user_badges_user_awarded_idx on user_badges (user_id, awarded_at desc);
```

## 11. Supporting tables

```sql
create table lesson_completions (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references users(id) on delete cascade,
  lesson_id uuid not null references lessons(id) on delete cascade,
  completed_at timestamptz not null default now(),
  time_spent_minutes int not null default 0,
  unique (user_id, lesson_id)
);

create table ai_tutor_interactions (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references users(id) on delete cascade,
  topic_id uuid references topics(id) on delete set null,
  problem_id uuid references coding_problems(id) on delete set null,
  prompt text not null,
  response text not null,
  tokens_in int not null default 0,
  tokens_out int not null default 0,
  safety_flags text[] not null default '{}',
  created_at timestamptz not null default now()
);

create table leaderboard_snapshots (
  id uuid primary key default gen_random_uuid(),
  period_key text not null,
  user_id uuid not null references users(id) on delete cascade,
  points int not null,
  accepted_submissions int not null,
  streak_days int not null,
  rank int not null,
  created_at timestamptz not null default now(),
  unique (period_key, user_id)
);
```
