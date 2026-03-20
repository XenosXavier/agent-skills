# Self-Review

Run after every change.

## Step 1 — Extract content

For each SKILL.md section:

| Question | Move to |
|---|---|
| Executable logic agent would re-invent? | `scripts/` |
| Reference material or detailed docs? | `references/` |
| Static resource (schema, config, data)? | `assets/` |

Create file under `{skill-name}/{dir}/` and replace inline content (e.g., "Refer to [X.md](references/X.md) when Y").

## Step 2 — Verify quality

- [ ] Instructions generalize to future tasks?
- [ ] Cut anything agent already knows?
- [ ] `description` states what it does and when to trigger?
- [ ] Gotchas concrete and actionable?
- [ ] SKILL.md under 300 lines?
- [ ] Reference file H1 matches filename? (e.g. `BACKGROUND.md` → `# Background`)

## Step 3 — Present all files

Present **all files** (SKILL.md and `references/`, `scripts/`, `assets/`) together — never in code blocks or partially. User needs consistency check.
