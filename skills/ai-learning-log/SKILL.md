---
name: ai-learning-log
description: Organize AI Engineering study notes into structured learning documents and save them to Google Drive.
---

# AI Learning Log

## When to use

Use this skill when the user asks Yuki to record, organize,
save, or summarize what they learned during an AI Engineering
study session.

Examples:
- "Yuki, save today's AI learning notes."
- "Create a learning log about MCP."
- "Organize what I learned about Python agents."

## Required input

- Study topic.
- At least a few notes explaining what the user learned.

Optional input:
- Code examples.
- Difficult concepts.
- Open questions.
- Topics to review later.

Ask a concise follow-up question if essential information is missing.
Do not invent facts or learning experiences.

## Workflow

1. Read the user's notes and identify the main topic.
2. Use the current date in America/Chihuahua.
3. Organize the information into the following sections:

   - Date and topic
   - Brief summary
   - Key concepts
   - Practical examples
   - Open questions
   - Recommended next steps

4. Preserve technical accuracy and code formatting.
5. Match the user's preferred language, English or Spanish.
6. Keep the document clear, concise, and practical.
7. Give the document a descriptive title:
   "AI Learning Log - YYYY-MM-DD - Topic"
8. Check the available Zapier MCP tools.
9. Use the existing Google Drive Create File From Text
   capability to save a new document.
10. Check the tool result to confirm successful creation.
11. If a document link is returned, share it with the user.
12. If no link is available, report the verified file details.
13. If creation fails, explain the error and preserve the
    organized notes in the conversation.

## Rules

- Follow IDENTITY.md, SOUL.md, USER.md, AGENTS.md, and TOOLS.md.
- Use existing authorized integrations.
- Do not request new API keys or OAuth connections.
- Do not overwrite existing documents.
- Never expose secrets or private credentials.
- Do not claim that a document was created unless a tool
  confirms the operation succeeded.
- Ask for approval before consequential external changes
  beyond creating the requested learning document.
- For Google Drive Create File From Text, use clean plain-text
  formatting instead of Markdown syntax.
- Use uppercase section headings and blank lines.
- Do not include Markdown characters such as #, **, or ---.
- Clearly label AI-generated examples as illustrative.
- Never imply an example was provided by the user if it was not.

## Successful result

A structured, dated learning document exists in Google Drive,
contains the user's actual study notes, and Yuki reports the
verified outcome through the conversation.
