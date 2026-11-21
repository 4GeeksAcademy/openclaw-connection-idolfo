---
name: 4geeks-learning-resources
description: "Search and discover 4Geeks Academy learning resources (exercises, lessons, projects, quizzes, articles) with filtering by type, difficulty, language, category, and keywords."
allowed-tools:
  - exec
  - secrets
---

# 4Geeks Learning Resources

## When to use

Use when the user wants to discover learning resources from the 4Geeks
Academy curriculum — find exercises, lessons, projects, or articles by
type, difficulty, language, or topic. Useful for planning study sessions
or finding supplementary material for pending coursework.

## Prerequisites

The `FOURGEEKS_STUDENT_TOKEN` secret must be stored with allowed host
`breathecode.herokuapp.com`.

## API endpoint

- **Method:** `GET`
- **URL:** `https://breathecode.herokuapp.com/v1/registry/asset`
- **Auth:** `Authorization: Token $FOURGEEKS_STUDENT_TOKEN`

## Auth format

```
Authorization: Token <student_token>
```

## Query parameters

| Parameter | Values | Behavior |
|-----------|--------|----------|
| `asset_type` | `EXERCISE`, `LESSON`, `PROJECT`, `QUIZ`, `ARTICLE`, `STARTER` | Filter by resource type |
| `difficulty` | `BEGINNER`, `EASY`, `INTERMEDIATE` | Filter by difficulty level |
| `lang` | `us` (English), `es` (Spanish) | Filter by language |
| `category` | `technical-skills`, `habilidades-technicas`, `blog-us`, `blog-es`, etc. | Filter by content category |
| `slug` | exact slug name | Filter by exact slug (fuzzy search not supported) |
| `limit` | integer (default 20) | Results per page |
| `page` | integer | Page number for pagination |

**Text search limitation:** The API does not support free-text search
parameters (`search`, `keyword`, `q`, `text`, `title` all return unfiltered
results). Use client-side matching against `title` and `description`
fields after fetching filtered results.

## Workflow

### 1. Build the query

Determine which filters the user needs:

```
# Example: find beginner Python exercises
asset_type=EXERCISE&difficulty=BEGINNER&limit=50

# Example: find intermediate lessons in English
asset_type=LESSON&difficulty=INTERMEDIATE&lang=us&limit=50

# Example: find all projects
asset_type=PROJECT&limit=50
```

### 2. Fetch results

```bash
curl -sL --max-time 20 \
  "https://breathecode.herokuapp.com/v1/registry/asset?asset_type=EXERCISE&difficulty=BEGINNER&limit=50" \
  -H "Authorization: Token $FOURGEEKS_STUDENT_TOKEN" \
  -H "Accept: application/json"
```

### 3. Apply client-side keyword filter (if needed)

The API returns all results matching the structural filters. To find
resources about a specific topic (e.g., Python, AI, MCP), filter the
results by checking `title` and `description` fields:

```python
keyword = "python"
filtered = [a for a in results
            if keyword in a.get('title','').lower()
            or keyword in (a.get('description','') or '').lower()]
```

### 4. Parse each resource

| Field | Type | Description |
|-------|------|-------------|
| `title` | string | Resource name |
| `asset_type` | string | EXERCISE, LESSON, PROJECT, QUIZ, ARTICLE, STARTER |
| `difficulty` | string or null | BEGINNER, EASY, INTERMEDIATE |
| `lang` | string | `us` (English) or `es` (Spanish) |
| `url` | string or null | GitHub repository or content URL |
| `readme_url` | string or null | README/documentation URL |
| `description` | string | Short description/summary |
| `slug` | string | Unique identifier |
| `category` | string or object | Content category |
| `duration` | int or null | Estimated duration in minutes |
| `id` | int | Unique ID |

### 5. Handle pagination

The response is a paginated dict:

```json
{
  "count": 29,
  "first": "...",
  "next": null,
  "previous": null,
  "last": "...",
  "results": [...]
}
```

If `count` exceeds the fetched results and more are needed, use the
`page` parameter to request additional pages.

### 6. Format the output

```
🔍 Learning Resources — {filter_summary}

{count} resources found

📝 EXERCISES (or 📖 LESSONS / 📦 PROJECTS / ❓ QUIZ / 📰 ARTICLE)

  1. Title
     Difficulty: BEGINNER | Language: English | Duration: N min
     {description[:120]}
     🔗 {url}

  2. Title
     ...

---

💡 Tip: Use asset_type=EXERCISE&difficulty=BEGINNER for practice,
  or asset_type=LESSON for conceptual learning.
```

### 7. Response field details

All visible from the API response; no sensitive fields exposed.

### 8. Example queries

| User asks | API call |
|-----------|----------|
| "Find beginner Python exercises" | `asset_type=EXERCISE&difficulty=BEGINNER&limit=50` + client filter "python" |
| "Show me AI/agent lessons" | `asset_type=LESSON&limit=100` + client filter AI terms |
| "Intermediate projects in English" | `asset_type=PROJECT&difficulty=INTERMEDIATE&lang=us&limit=50` |
| "Find quizzes about any topic" | `asset_type=QUIZ&limit=50` |
| "Show me starter templates" | `asset_type=STARTER&limit=50` |