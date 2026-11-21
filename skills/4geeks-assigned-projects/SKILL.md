---
name: 4geeks-assigned-projects
description: "Retrieve assigned 4Geeks Academy projects for the authenticated student, showing project name, task status, revision status, submission dates, and cohort."
allowed-tools:
  - exec
  - secrets
---

# 4Geeks Assigned Projects

## When to use

Use when the user wants to see which 4Geeks Academy projects are assigned
to them, their current status (pending, submitted, or graded), and details
such as cohort, GitHub URL, and submission dates.

## Prerequisites

The authentication skill `4geeks-api-auth` must be installed and the
`FOURGEEKS_STUDENT_TOKEN` secret must be stored with allowed host
`breathecode.herokuapp.com`.

## API endpoint

- **Method:** `GET`
- **URL:** `https://breathecode.herokuapp.com/v1/assignment/user/me/task`
- **Parameter:** `task_type=PROJECT`
- **Auth:** `Authorization: Token $FOURGEEKS_STUDENT_TOKEN`

## Auth format

```
Authorization: Token <student_token>
```

The token is read from the injected environment variable
`FOURGEEKS_STUDENT_TOKEN`. Never hardcode or log the token.

## Workflow

### 1. Fetch assigned projects

```
curl -sL --max-time 20 \
  "https://breathecode.herokuapp.com/v1/assignment/user/me/task?task_type=PROJECT&limit=100" \
  -H "Authorization: Token $FOURGEEKS_STUDENT_TOKEN" \
  -H "Accept: application/json"
```

### 2. Parse the response

The endpoint returns a paginated dictionary:

- `count`: total number of projects
- `next` / `previous`: pagination URLs (null if no more pages)
- `results`: array of project objects

If `next` is not null, fetch the next page by repeating the request
with the URL in the `next` field.

### 3. Categorize by status

Each project has these relevant fields:

| Field | Type | Meaning |
|-------|------|---------|
| `title` | string | Project name |
| `task_status` | string | `PENDING` or `DONE` |
| `revision_status` | string | `PENDING`, `APPROVED`, or other |
| `delivered_at` | datetime or null | When student submitted |
| `reviewed_at` | datetime or null | When mentor reviewed |
| `associated_slug` | string | Internal slug for the project |
| `github_url` | string or null | GitHub repo/PR link |
| `live_url` | string or null | Deployment URL |
| `cohort` | object | `{id, name, slug}` — the cohort this project belongs to |

**Status determination logic:**

```
If task_status == "PENDING":
    → PENDING (not started)

Elif task_status == "DONE" and revision_status == "APPROVED":
    → APPROVED (graded)

Elif task_status == "DONE" and delivered_at is set and reviewed_at is None:
    → SUBMITTED (awaiting review)

Elif task_status == "DONE":
    → DONE but ungraded
```

### 4. Return structured result

Summarize by category:

```
PENDING (not started):
  • Project Title — Cohort: Cohort Name
  • Project Title — Cohort: Cohort Name

SUBMITTED (awaiting review):
  • Project Title — Submitted: YYYY-MM-DD — Cohort: Cohort Name

APPROVED (graded):
  • Project Title — ✅ APPROVED — Cohort: Cohort Name

Total: X projects
  Pending: X
  Approved: X
  Awaiting review: X
```

### 5. Pagination

If the response has more than one page (`next` is not null), fetch all
pages. The default page size is 20; use `limit=100` to reduce page count.

```
GET /v1/assignment/user/me/task?task_type=PROJECT&limit=100
```

### 6. Error handling

| HTTP Code | Meaning | Action |
|-----------|---------|--------|
| 200 | Success | Parse and display |
| 401 | Token expired/invalid | Re-run 4geeks-api-auth |
| 403 | Insufficient permissions | Report access issue |
| 503 | Server error | Retry with backoff |
| 000 | Unreachable | Report and retry later |

## Response field details (per project)

```
{
  "id": int,
  "title": "Project Name",
  "task_status": "DONE|PENDING",
  "revision_status": "APPROVED|PENDING|REJECTED",
  "delivered_at": "ISO datetime or null",
  "reviewed_at": "ISO datetime or null",
  "github_url": "URL or null",
  "live_url": "URL or null",
  "associated_slug": "slug-name",
  "cohort": {
    "id": int,
    "name": "Cohort Display Name",
    "slug": "cohort-slug"
  }
}
```

## Example output

```
=== Your 4Geeks Academy Projects ===

📋 PENDING (not started):
  • Milestone 0 — Choose Your Company — AI Engineering Fundamentals

📤 SUBMITTED (awaiting review):
  • My Project — Submitted: 2026-11-16 — Cohort: Cohort Name

✅ APPROVED (graded):
  • Approved Project — Cohort: Cohort Name

Summary: 10 total — 4 pending · 1 submitted · 5 approved
```

## Reuse

Other skills (progress tracking, study planning, etc.) can call this
skill to obtain the user's project list and status.