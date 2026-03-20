# Skill Template

Template for Agent Skills. See [docs](https://agentskills.io/home) for spec.

## File structure

```
---
name: skill-name
description: [What it does and when to trigger.]
---

# [Skill Display Name]

[What it does and how.]

## Instructions
[Step-by-step procedures for reuse. Prefer defaults over options. Include examples.]

## Gotchas
[Non-obvious facts agent would miss. Concrete and actionable.
Keep in SKILL.md for early read.]

## Edge Cases
[Boundary conditions. Omit obvious cases.]
```

## Frontmatter rules

- `name`: lowercase, numbers, hyphens. No consecutive/leading/trailing hyphens. Match directory name.
- `description`: what and when. Be specific.
