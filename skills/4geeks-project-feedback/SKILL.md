---
name: 4geeks-project-feedback
description: "Retrieve detailed feedback for a specific 4Geeks Academy project submission, including mentor comments, approval status, submission/review dates, and GitHub links."
allowed-tools:
  - exec
  - secrets
---

# 4Geeks Project Feedback

## When to use

Use when the user wants to check the feedback, grading status, and
review details for a specific submitted or graded project. Ask the
user which project they want to inspect, or use Skill 2 to list
projects first.

## Prerequisites

The `FOURGEEKS_STUDENT_TOKEN` secret must be stored with allowed host
`breathecode.herokuapp.com`.

## API endpoint

- **Method:** `GET`
- **URL:** `https://breathecode.herokuapp.com/v1/assignment/task/{task_id}`
- **Auth:** `Authorization: Token $FOURGEEKS_STUDENT_TOKEN`

Replace `{task_id}` with the numeric project ID (e.g., `1019025`).

## Auth format

```
Authorization: Token <student_token>
```

## Workflow

### 1. Identify the project to inspect

Use Skill 2 (`4geeks-assigned-projects`) to list all projects and
their IDs, or ask the user directly:

- "Which project do you want feedback on?"
- "What's the project name or ID?"

### 2. Fetch the project detail

```bash
curl -sL --max-time 15 \
  "https://breathecode.herokuapp.com/v1/assignment/task/{task_id}" \
  -H "Authorization: Token $FOURGEEKS_STUDENT_TOKEN" \
  -H "Accept: application/json"
```

Replace `{task_id}` with the project's numeric ID.

### 3. Parse the response

The endpoint returns a single project object with these fields:

| Field | Type | Meaning |
|-------|------|---------|
| `id` | int | Project ID |
| `title` | string | Project name |
| `task_status` | string | `PENDING` or `DONE` |
| `revision_status` | string | `PENDING` or `APPROVED` |
| `description` | string | **Mentor feedback comment** (empty if not reviewed) |
| `delivered_at` | ISO datetime or null | When the student submitted |
| `reviewed_at` | ISO datetime or null | When the mentor reviewed |
| `opened_at` | ISO datetime or null | When the project was assigned |
| `created_at` | ISO datetime | Record creation |
| `updated_at` | ISO datetime | Last update |
| `read_at` | ISO datetime or null | When student read the feedback |
| `github_url` | string or null | GitHub repo/PR link |
| `live_url` | string or null | Deployment URL |
| `associated_slug` | string | Internal project slug |
| `cohort` | object | `{id, name, slug}` |
| `task_type` | string | Always `PROJECT` |
| `assignment_telemetry` | object or null | Currently always null (not used) |

### 4. Distinguish mentor feedback from descriptions

**Important:** The `description` field serves double duty:
- For **pending/unsubmitted** projects: it may contain the project's
  original instructions or be empty.
- For **submitted projects awaiting review**: it is typically empty
  (the mentor hasn't written feedback yet).
- For **approved/graded projects**: it contains the **mentor's
  feedback comment** (e.g., "Perfecto!!!", "Excelente trabajo!").

The API provides **no separate grade field**, **no rubric scores**,
and **no structured improvement notes**. The feedback is always
free-text in the `description` field.

### 5. Format the output

```
📁 Project: {title}
========================

📊 Status: [PENDING | DONE]
✅ Revision: [PENDING | APPROVED]

💬 Mentor feedback: "{description or 'Not yet reviewed'}"

🕐 Timeline:
  Assigned:     YYYY-MM-DD HH:MM UTC
  Submitted:    YYYY-MM-DD HH:MM UTC (or Not yet submitted)
  Reviewed:     YYYY-MM-DD HH:MM UTC (or Awaiting review)

🔗 Links:
  GitHub: {url or None}
  Live:   {url or None}

📚 Cohort: {cohort name}
```

### 6. Determine project status

| Status | `task_status` | `revision_status` | `delivered_at` | `reviewed_at` | Interpretation |
|--------|:------------:|:-----------------:|:--------------:|:-------------:|----------------|
| Not started | PENDING | PENDING | null | null | Not submitted yet |
| Submitted | DONE | PENDING | set | null | Awaiting mentor review |
| Approved | DONE | APPROVED | set | set | Graded and approved |
| Feedback available | DONE | APPROVED | set | set | `description` has mentor's comment |

### 7. Important limitations

- **No detailed grade** — Revision is APPROVED or PENDING only.
  No A/B/C, scoring, or rubric breakdown.
- **No improvement notes** — If the mentor didn't write actionable
  feedback in the description, there's nothing else to show.
- **No re-submission tracking** — Only one submission/review cycle
  is tracked per project.
- **Same data as Skill 2** — The individual task endpoint returns
  the same fields as the list endpoint. The value is in fetching
  a specific project's details on demand rather than scanning a
  full project list.