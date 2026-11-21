---
name: 4geeks-pending-work
description: "Retrieve all pending 4Geeks Academy assignments (exercises, lessons, and projects) for the authenticated student, grouped by type and cohort."
allowed-tools:
  - exec
  - secrets
---

# 4Geeks Pending Work

## When to use

Use when the user wants to see everything they still need to complete
in their 4Geeks Academy courses: pending exercises, lessons, and
projects. This skill helps identify untouched work vs work awaiting
review, organized by task type and cohort.

## Prerequisites

The authentication skill `4geeks-api-auth` must be installed and the
`FOURGEEKS_STUDENT_TOKEN` secret must be stored with allowed host
`breathecode.herokuapp.com`.

## API endpoint

- **Method:** `GET`
- **URL:** `https://breathecode.herokuapp.com/v1/assignment/user/me/task`
- **Filter parameter:** `task_status=PENDING` (returns tasks not yet started)
- **Auth:** `Authorization: Token $FOURGEEKS_STUDENT_TOKEN`

## Auth format

```
Authorization: Token <student_token>
```

The token is read from the injected environment variable
`FOURGEEKS_STUDENT_TOKEN`. Never hardcode or log the token.

## Workflow

### 1. Fetch pending tasks

```
curl -sL --max-time 20 \
  "https://breathecode.herokuapp.com/v1/assignment/user/me/task?task_status=PENDING&limit=200" \
  -H "Authorization: Token $FOURGEEKS_STUDENT_TOKEN" \
  -H "Accept: application/json"
```

Use `limit=200` to get all pending tasks in one page.

### 2. Also check submitted-but-unreviewed tasks

Tasks with `task_status=DONE`, a non-null `delivered_at`, and a null
`reviewed_at` have been submitted by the student but not yet reviewed
by a mentor. Fetch these with:

```
curl -sL --max-time 20 \
  "https://breathecode.herokuapp.com/v1/assignment/user/me/task?task_status=DONE&limit=200" \
  -H "Authorization: Token $FOURGEEKS_STUDENT_TOKEN" \
  -H "Accept: application/json"
```

Then filter where `delivered_at is not null AND reviewed_at is null`.

### 3. Parse and categorize

**Three categories of actionable work:**

| Category | API criteria | Meaning |
|----------|--------------|---------|
| **Pending (not started)** | `task_status=PENDING` | Not yet started or submitted |
| **Submitted (awaiting review)** | `task_status=DONE` + `delivered_at` set + `reviewed_at` null | Submitted, waiting for mentor feedback |
| **In progress (partially done)** | Not available from API — API only shows PENDING or DONE | N/A (API limitation) |

### 4. Group by task type

Each task has a `task_type` field:

| Type | Example | Action needed |
|------|----------|--------------|
| `EXERCISE` | Coding exercises, tutorials, readings | Complete and submit |
| `LESSON` | Instructional modules | Complete and submit |
| `PROJECT` | Milestones, projects | Build, submit, get reviewed |

### 5. Format the output

```
=== Pending Work Overview ===

📋 EXERCISES ({count} pending)— {cohort_name}

📖 LESSONS ({count} pending)— {cohort_name}

📦 PROJECTS ({count} pending)
   • {project_name — cohort: cohort_name

📤 SUBMITTED (awaiting review)— {project_name — Submitted: YYYY-MM-DD—— Cohort: cohort_name}

Summary: {total} pending + {total} awaiting review = {total_total} actionable
```

### 6. Pagination

The API returns paginated results. Use `limit=200` to get all tasks.
Check the `next` field in the response and fetch additional pages
if present.

### 7. Important notes

- **No due dates are available.** The API does not provide any deadline,
  end_date, due_date, or expiry field on task objects. Never invent
  or assume deadlines.
- **No progress indicator.** Tasks are either PENDING or DONE with
  no partial completion tracking.
- **Opened_at timestamp** indicates when the task was first assigned
  or made available, not when it's due.
- **Cohort filtering** is not required because the user's profile
  already scopes tasks to their active cohorts.

### 8. Error handling

| HTTP Code | Meaning | Action |
|-----------|---------|--------|
| 200 | Success | Parse and display |
| 401 | Token expired/invalid | Re-run 4geeks-api-auth |
| 403 | Insufficent permissions | Report access issue |
| 503 | Server error | Retry with backoff |