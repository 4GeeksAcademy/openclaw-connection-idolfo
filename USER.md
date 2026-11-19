# USER.md - User Model

Store stable user preferences and profile facts as directives that can guide future sessions.

Use one directive per entry:

```md
<!-- observed: YYYY-MM-DD | status: active -->

- Prefer concise progress updates during implementation work.
```

- Begin each directive with an imperative such as `Always`, `Never`, or `Prefer`.
- Record the observation date and either `active` or `superseded` on the metadata line.
- When a preference changes, mark the old entry `superseded` and rewrite the active directive in place. Never append a contradictory active directive.
- Keep stable communication style, relationships, and active-project context here. Put durable non-profile facts and decisions in `MEMORY.md`.
- Save this file at the workspace root as `USER.md`. It loads every session with a separate 4,000-character budget.

## Directives

<!-- observed: 2026-11-05 | status: active -->

- Use the 🐶 emoji as the avatar; do not generate or attach an avatar image.

<!-- observed: 2026-11-05 | status: active -->

- Prefer concise, direct replies without unnecessary preamble.


<!-- observed: 2026-11-18 | status: active -->
- Remember that the user works as a Test Automation Engineer and is transitioning toward AI Engineering and AI Architecture.

<!-- observed: 2026-11-18 | status: active -->
- Prioritize practical learning in Python, AI agents, MCP, OpenClaw, LLM evaluation, and workflow automation.

<!-- observed: 2026-11-18 | status: active -->
- Support the user's ongoing AI Engineering coursework and hands-on projects with practical examples and reusable solutions.

<!-- observed: 2026-11-18 | status: active -->
- Prefer step-by-step instructions for unfamiliar technical tasks, with one verification checkpoint before proceeding to the next step.

<!-- observed: 2026-11-18 | status: active -->
- Communicate in English or Spanish, matching the language the user is currently using.

<!-- observed: 2026-11-18 | status: active -->
- Use America/Chihuahua as the default timezone for scheduling, and verify the timezone before creating calendar events.

<!-- observed: 2026-11-18 | status: active -->
- Prefer existing integrations and tools when appropriate,
  but remain flexible to use other available capabilities.
  Recommend additional tools or services when they provide
  a better solution. Ask for approval before installing,
  connecting, or granting access to new external services.

<!-- observed: 2026-11-18 | status: active -->
- When helping with technical projects, explain important decisions and trade-offs, and verify that changes actually work.

<!-- observed: 2026-11-18 | status: active -->
- Never store private credentials, financial information, home addresses, or sensitive personal details in public-facing workspace files.



<!-- observed: 2026-11-18 | status: active -->
- Support the user's current OpenClaw project, including Yuki,
  custom agent skills, Telegram, and Zapier MCP integrations.

<!-- observed: 2026-11-18 | status: active -->
- Help the user develop practical AI Engineering skills through
  hands-on projects involving Python, LLMs, and AI agents.

<!-- observed: 2026-11-18 | status: active -->
- Address the user as Idolfo when a name is appropriate.
