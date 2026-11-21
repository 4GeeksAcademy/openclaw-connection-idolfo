---
name: 4geeks-api-auth
description: "Authenticate with the 4Geeks Academy / BreatheCode Student API using a stored token from OpenClaw's secret store. Never exposes the token value."
allowed-tools:
  - exec
  - secrets
---

# 4Geeks API Authentication

## When to use

Use when the agent needs to authenticate with the BreatheCode Student API
or the 4Geeks Academy platform. This skill is the authentication layer:
other skills (assignments, projects, etc.) call this first to obtain
or validate credentials.

Run this skill before any other 4Geeks API skill.

## Security model

The student token is stored in OpenClaw's secret store as
`FOURGEEKS_STUDENT_TOKEN` with allowed host `breathecode.herokuapp.com`.
The gateway injects the secret sentinel into every `exec` command
as the environment variable `FOURGEEKS_STUDENT_TOKEN`. The OpenClaw
secret egress proxy substitutes the sentinel with the real credential
value during outbound HTTPS requests.

This skill:
- Reads the token from the injected env var — never from chat, files,
  or tool arguments.
- Passes it as `Authorization: Token <value>` in HTTPS headers.
- NEVER prints, logs, or echoes the token value or authorization header.
- Uses `curl --max-time 15` to prevent hanging.
- Returns structured JSON with auth result and user identity,
  but never the raw credential.

## Verified API configuration

- **Method:** `GET`
- **Endpoint:** `https://breathecode.herokuapp.com/v1/admissions/user/me`
- **Base URL:** `https://breathecode.herokuapp.com`
- **Framework:** Django REST Framework
- **Auth format:** `Authorization: Token <student_token>` (DRF TokenAuthentication)
- **Token type:** DRF Token (not JWT, not Bearer)

The endpoint was verified through Postman (HTTP 200 with student profile)
and through OpenClaw's egress proxy (HTTP 200 with student profile).

## Auth format details

The BreatheCode Student API uses Django REST Framework's
`TokenAuthentication` backend. The correct header format is:

```
Authorization: Token <4g_tok_value_here>
```

Note: the scheme literal is `Token` (capital T), NOT `Bearer`.

## Workflow

### 1. Authenticate

```
curl -sL --max-time 15 \
  "https://breathecode.herokuapp.com/v1/admissions/user/me" \
  -H "Authorization: Token $FOURGEEKS_STUDENT_TOKEN" \
  -H "Accept: application/json"
```

Expected responses:
- **200** — Authenticated. Response contains user profile data.
- **401** — Authentication rejected. Token may be expired or invalid.
- **503** — Temporary server error. Retry once.

### 2. Verify result

Check the HTTP status code and response body. If authenticated,
return structured user data without the raw token value.

| Code | Meaning | Action |
|------|---------|--------|
| 200 | Authenticated | Extract user identity and proceed |
| 401 | Bad/expired token | Report, recommend refresh |
| 503 | Server-side error | Log, retry, or report |
| 000 | Unreachable | Check network, retry later |

### 3. Return structured result

Return a JSON object with:

```
{
  "authenticated": true/false,
  "status_code": 200,
  "user": {
    "email": "...",
    "first_name": "...",
    "last_name": "...",
    "username": "...",
    "github_username": "..."
  },
  "endpoint": "https://breathecode.herokuapp.com/v1/admissions/user/me",
  "api_base": "https://breathecode.herokuapp.com",
  "message": "Authenticated as <first_name> <last_name>"
  "detail": "..."
}
```

Never include the token value in the output.

## Reuse by other skills

Future skills (assignments, projects, etc.) should include this
authentication step in their workflow:

1. Source / call `4geeks-api-auth` to validate credentials.
2. Use `Authorization: Token ${FOURGEEKS_STUDENT_TOKEN}` for subsequent API calls.
3. Use the same `api_base` URL for all BreatheCode API calls.

## Error handling

- If the env var is empty or unset: report "Student token not found.
  Store it with `openclaw secrets store FOURGEEKS_STUDENT_TOKEN`."
- If the API returns 5xx: report "API is unreachable. Retry later."
- If authentication fails: report "Authentication rejected. Token may
  be expired or invalid."

## Verification

After a successful auth call, verify by re-running the same curl command
and confirming HTTP 200 with user data. The egress proxy handles
sentinel substitution automatically — no special configuration needed.