
# Yuki AI Assistant — Custom Skills Design

## Project Objective

Teach Yuki two reusable skills that support my AI
Engineering studies using existing OpenClaw tools.

The skills must follow Yuki's personality, user
preferences, security rules, and tool conventions.

---

## Skill 1: AI Learning Log

### 1. What does this skill do?

Convert informal notes from my AI Engineering
studies into structured learning documents.

This eliminates the need to repeatedly explain
how I want my study notes organized.

### 2. What input does the agent need?

Required:
- Study topic.
- A few notes about what I learned.

Optional:
- Code examples.
- Questions or concepts I found difficult.
- Topics to review later.

Existing context:
- USER.md: AI Engineering goals and preferences.
- SOUL.md: Concise, practical, bilingual communication.
- AGENTS.md: Privacy and verification rules.
- TOOLS.md: Google Drive and Zapier conventions.
- IDENTITY.md: Yuki's identity and communication style.

### 3. What does a good output look like?

A Google document containing:
- Date and topic.
- Brief summary.
- Key concepts.
- Practical examples.
- Open questions.
- Recommended next steps.

The document should have a descriptive title
and be created using the existing Zapier MCP tools.

Success criteria:
- The document exists in Google Drive.
- The requested content is present.
- Yuki returns the document link via Telegram.
- Yuki clearly reports any failure.

### Why this skill?

I am studying AI Engineering and regularly
learn new technical concepts.

A consistent learning journal will help me
review topics and track my progress.

---

## Skill 2: Weekly Study Planner

### 1. What does this skill do?

Transform my weekly learning goals into a
prioritized study plan with calendar events.

This removes repetitive scheduling instructions.

### 2. What input does the agent need?

Required:
- Topics or learning objectives.
- Available study days and times.

Optional:
- Deadlines.
- Topic priorities.
- Preferred session duration.

Existing context:
- USER.md: AI learning goals and Chihuahua timezone.
- SOUL.md: Practical recommendations and initiative.
- AGENTS.md: Approval and execution boundaries.
- TOOLS.md: Calendar settings and available actions.
- IDENTITY.md: Yuki's personalized communication style.

Yuki must ask for essential missing information
before creating events.

### 3. What does a good output look like?

A weekly study plan containing:
- Prioritized learning objectives.
- Study topics and estimated durations.
- Recommended order of study.
- Scheduled Google Calendar events.

Success criteria:
- The plan is clear and practical.
- Calendar events use America/Chihuahua.
- Events have correct dates and durations.
- Existing events are checked when possible.
- Yuki verifies the created events.
- A summary is returned through Telegram.

### Why this skill?

I am balancing professional work with ongoing
AI Engineering studies.

A repeatable planning workflow will help me
organize learning goals and protect study time.

---

## Implementation Decisions

- Implement both as proper OpenClaw skills.
- Each skill will contain a SKILL.md file.
- Use only existing Telegram and Zapier MCP integrations.
- Do not configure new APIs or OAuth connections.
- Verify tools before attempting external actions.
- Require approval for consequential changes.
- Test both skills with real study-related requests.
- Verify at least one result directly in Google.
- Never publish credentials or private memory files.

## Git History Requirement

Commit SKILLS_DESIGN.md before creating or
implementing either custom skill.
