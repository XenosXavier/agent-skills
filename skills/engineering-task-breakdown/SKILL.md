---
name: engineering-task-breakdown
description: Provides knowledge for breaking down engineering requirements into structured, actionable tasks. Use when requirements, architecture, or design context needs to be converted into a task breakdown.
metadata:
  author: Bug.J
  version: "3.0"
---

# Engineering Task Breakdown

A good task is independently deliverable — one developer, one pull request, one reviewable unit of work. It has concrete acceptance criteria that describe verifiable outcomes, not implementation steps. Dependencies between tasks must be explicit so execution order is unambiguous.

---

## Instructions

1. Read all provided source documents to establish the requirements, constraints, and architecture decisions that the breakdown must align with
2. Identify the top-level grouping structure from the documents — use the most natural unit (versions, milestones, domains, or feature areas) and produce a high-level breakdown of each group
3. For each group, break down into individual tasks using the Task Template below
4. Identify dependencies between tasks and fill in the `Blocking` and `Waiting on` sections using task names as placeholders — if task URLs are available, replace with markdown links

---

## Task Template

### Content Format

```
## Context
[1-2 sentences placing task in overall architecture.]

## Acceptance Criteria
- [ ] [Concrete, verifiable outcome]

## Technical Notes  (omit if not needed)
- [Link spec pages by URL — never plain text like "see Section X"]
- [Rationale for non-obvious constraints]

## Blocking  (omit if none)
- [RD] Task Name              ← placeholder; replace with link when URL available
- [[RD] Task Name](task-url)  ← with URL

## Waiting on  (omit if none)
- [RD] Task Name              ← placeholder; replace with link when URL available
- [[RD] Task Name](task-url)  ← with URL
```

### Fields

| Field | Rule |
|-------|------|
| **Priority** | `urgent` — blocks others if delayed; `high` — core feature; `normal` — required; `low` — deferrable |
| **Assignee** | MUST NOT assign arbitrarily — only set if explicitly provided |
| **Reporter** | Default to current user if service supports it |
| **Time Estimate** | Express in hours (`2h`, `0.5h`). MUST NOT invent — only set if explicitly provided |
| **Due Date** | MUST NOT invent — only set if explicitly provided; use spec target date if available |
| **Status** | Use default list status — do not override unless requested |

---

## Gotchas

- **Tasks that span multiple components or require multiple review cycles must be split** — a task too trivial to stand on its own should be merged into a related task
- **Acceptance criteria describe outcomes, not steps** — "User can reset password via email" is verifiable; "Implement password reset flow" is not
- **Task dependency fields cannot be set via API in most services** — dependency links must be set manually in the UI after all tasks are created
- **Always link spec pages by full URL** — never reference plain text like "see TDD Section X"
- **Never invent assignees, time estimates, or due dates** — only set what was explicitly provided

---

## Edge Cases

- **No documents provided** — use conversation context as the requirements source; summarize understanding before proceeding
- **No version list in documents** — group by functional areas or modules instead
- **Architecture already defined** — acknowledge existing decisions and proceed without re-determining architecture
- **Scope limited to one version or group** — apply breakdown only to that scope
