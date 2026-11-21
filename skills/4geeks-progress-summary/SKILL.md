---
name: 4geeks-progress-summary
description: "Retrieve all 4Geeks Academy assignments across types and cohorts and report completion progress: total, pending, submitted, and approved counts with percentages."
allowed-tools:
  - exec
  - secrets
---

# 4Geeks Progress Summary

## When to use

Use when the user wants to know how far along they are in their
4Geeks Academy courses: overall completion rate, progress by cohort,
and counts of pending, approved, and submitted-but-unreviewed work.

## Prerequisites

The `FOURGEEKS_STUDENT_TOKEN` secret must be stored with allowed host
`breathecode.herokuapp.com`.

## API endpoint

- **Method:** `GET`
- **URL:** `https://breathecode.herokuapp.com/v1/assignment/user/me/task`
- **Auth:** `Authorization: Token $FOURGEEKS_STUDENT_TOKEN`

No status filter — retrieve all tasks to compute accurate totals.

## Auth format

```
Authorization: Token <student_token>
```

## Workflow

### 1. Fetch all tasks

```bash
curl -sL --max-time 20 \
  "https://breathecode.herokuapp.com/v1/assignment/user/me/task?limit=200" \
  -H "Authorization: Token $FOURGEEKS_STUDENT_TOKEN" \
  -H "Accept: application/json"
```

Use `limit=200` to get all tasks in one page (actual total is typically
well under 200).

### 2. Categorize each task

Each task has these status-related fields:

| Field | Possible values | Meaning |
|-------|----------------|---------|
| `task_status` | `PENDING` or `DONE` | Whether the student has submitted |
| `revision_status` | `PENDING` or `APPROVED` | Whether the mentor has approved |
| `delivered_at` | ISO datetime or null | When the student submitted |
| `reviewed_at` | ISO datetime or null | When the mentor reviewed |

**Classification logic** (in order):

```
if task_status == "PENDING":
    → PENDING (not started)

elif task_status == "DONE" and revision_status == "APPROVED":
    → APPROVED (graded)

elif task_status == "DONE" and delivered_at is not null and reviewed_at is null:
    → SUBMITTED (awaiting review)

else:
    → OTHER (completed but unusual status)
```

### 3. Compute formulas

```
total_tasks = count(all tasks)

pct_pending  = count(PENDING)  / total_tasks * 100
pct_approved = count(APPROVED) / total_tasks * 100
pct_done     = (total_tasks - count(PENDING)) / total_tasks * 100
pct_graded   = count(APPROVED) / total_tasks * 100  (= pct_approved)
```

### 4. Group by cohort

```
For each cohort:
  total = tasks in that cohort
  pending = PENDING tasks
  approved = APPROVED tasks
  pct_done = (total - pending) / total * 100
```

### 5. ⚠️ Important: What this does NOT measure

- **Not official course completion.** The API tracks individual
  assignment submissions, not course-level graduation. Never report
  "course completion" or "graduation progress" from this data alone.
- **No partial progress.** Tasks are PENDING or DONE; there's no
  "in progress" or "started" status.
- **No deadlines.** The API does not provide due dates.

### 6. Format the output

```
=== Progress Summary ===

OVERALL (N total):
  PENDING:  N (X.X%) — not started
  APPROVED: N (X.X%) — graded by mentor
  AWAITING: N (X.X%) — submitted, not yet reviewed
  DONE:     N (X.X%) — any non-pending status

  ★ GRADED: N (X.X%) — assignments approved by a mentor

--- By type ---
EXERCISES (N):   PENDING N • APPROVED N • AWAITING N
LESSONS (N):     PENDING N • APPROVED N • AWAITING N
PROJECTS (N):    PENDING N • APPROVED N • AWAITING N

--- By cohort ---
Cohort Name (N tasks):
  ███░░░░░░░░░░░░░░░░░  XX% complete
  Pending: N | Approved: N | Awaiting: N
```

### 7. Pagination

The API returns paginated results. Use `limit=200`. If `next` is not
null, fetch the next page and combine results before computing stats.

### 8. Error handling

| HTTP Code | Meaning | Action |
|-----------|---------|--------|
| 200 | Success | Parse and display |
| 401 | Token expired/invalid | Re-run 4geeks-api-auth |
| 503 | Server error | Retry with backoff |