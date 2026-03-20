---
name: forge
description: Turns knowledge, workflows, and expertise into packaged Agent Skills (.zip). Use when the user wants to create skills from conversations, documents, or runbooks.
metadata:
  author: Bug.J
  version: "3.0"
---

# Forge

Turns knowledge, workflows, and expertise into Agent Skills. Extracts patterns, drafts files, packages as zip.

## Instructions

### Step 1 — Gather source material

Ask for source material and read fully.

### Step 2 — Identify and confirm candidates

Use user's selection if provided. Otherwise, identify candidates:

- Domain knowledge or terminology
- Workflows with non-obvious steps or constraints
- Output formats or templates
- Team/project-specific decisions

Skip: Q&A, one-off answers, tasks agent handles.

Present as:
```
1. [skill-name] — one-sentence summary
2. [skill-name] — one-sentence summary
```

Ask which to build and record selection.

### Step 3 — Draft and refine skill

Create `{skill-name}/` directory and write `SKILL.md`. Refer to [SKILL-TEMPLATE.md](references/SKILL-TEMPLATE.md) for structure.

After every change, refer to [SELF-REVIEW.md](references/SELF-REVIEW.md) for checklist.

Present all skill files (SKILL.md and files in `references/`, `scripts/`, `assets/`) together.

Iterate until approved.

### Step 4 — Package skill

Package `{skill-name}/` as a zip and provide download.

### Step 5 — Continue or complete

If more skills remain from Step 2, return to Step 3.

When complete:
```
✅ Completed: skill-a.zip, skill-b.zip, skill-c.zip
```

## Gotchas

- **Minimize tokens.** Remove unnecessary words, use short sentences.
- **Never show files in code blocks.** Present directly.
- **Never present files partially.** User needs cross-file consistency check.
- **One zip per skill.** Never bundle multiple skills.
- **`name` must match directory name exactly.**
- **`description` states what and when.**
- **Write procedures, not answers.** Generalize to future tasks.

## Edge Cases

- **No patterns**: Tell user. Suggest walkthroughs, runbooks, or specs.
- **Too broad**: Flag before drafting. Suggest splitting or scoping down.
- **Too narrow**: Suggest merging with related candidate.
- **User adds files**: Create under `{skill-name}/{dir}/`, include in zip, reference from `SKILL.md`.
