---
name: weekly-study-planner
description: Create prioritized weekly AI Engineering study plans and schedule study sessions in Google Calendar using existing Zapier MCP integrations.
---

# Weekly Study Planner

## When to use

Use when the user asks Yuki to plan, organize, or schedule
their weekly AI Engineering studies.

Examples:
- "Yuki, plan my AI Engineering studies next week."
- "Schedule three Python and MCP study sessions."
- "Help me organize my weekly learning goals."

## Required inputs

- Study topics or learning objectives.
- Available study days and times.
- The target week, if it cannot be determined.

Optional:
- Deadlines.
- Topic priorities.
- Session duration.
- Preferred study order.

Use USER.md preferences when relevant.
Ask concise questions for missing essential information.

## Planning workflow

1. Identify the target week and its exact dates.
2. Use the America/Chihuahua timezone.
3. Review the learning objectives and deadlines.
4. Prioritize topics based on difficulty and importance.
5. Divide available time into realistic study sessions.
6. Include practical exercises and review time.
7. Prepare a clear weekly plan with:
   - Date
   - Start and end time
   - Topic
   - Objective
   - Expected outcome
8. Present the proposed schedule before creating events.
9. Ask the user to approve the proposed calendar events.

## Google Calendar workflow

After the user approves:

1. Discover the available Zapier MCP tools.
2. Check for existing events using Google Calendar
   Find Events when possible.
3. Warn the user about any identified conflicts.
4. Resolve conflicts before creating events.
5. Create approved events using Google Calendar
   Quick Add Event.
6. Include explicit dates, times, and the
   America/Chihuahua timezone in event details.
7. Use clear event titles such as:
   "AI Study - Python Fundamentals".
8. Verify each event using available tool results
   and calendar lookup when possible.
9. Report which events were confirmed and which
   could not be verified.

Do not assume Quick Add interpreted a date correctly.
If event times are incorrect, do not claim success.
Explain the discrepancy and request approval before
modifying or recreating events.

## Document output

Present a readable weekly plan in the conversation.

If the user requests a saved document, use the existing
Google Drive Create File From Text tool.

For text-based Google documents:
- Use clean plain text.
- Use uppercase headings and blank lines.
- Avoid literal Markdown formatting characters.
- Verify the document was created before returning a link.

## Rules

- Follow IDENTITY.md, SOUL.md, AGENTS.md,
  USER.md, and TOOLS.md.
- Match the user's preferred language.
- Use existing authorized integrations only.
- Do not request new API keys or OAuth connections.
- Do not schedule events without user approval.
- Do not delete or overwrite existing events.
- Do not invent calendar availability.
- Never expose credentials or private information.
- Clearly distinguish proposed events from
  successfully created and verified events.
- Report partial failures accurately.

## Successful result

The user receives a prioritized, practical weekly
study plan.

After approval, the requested Google Calendar
events are created with correct local dates,
times, and durations.

Yuki provides a final Telegram summary and reports
verification results or errors.
