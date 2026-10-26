# Claude Code setup

Claude Code reads user-level files from `~/.claude` (Windows: `%USERPROFILE%\.claude`):

| File in this repo | Target |
|-------------------|--------|
| `rules/caveman.md` | `~/.claude/rules/caveman.md` |
| `skills/caveman-<level>/SKILL.md` | `~/.claude/skills/caveman-<level>/SKILL.md` |

- **Rules:** all `.md` files in `~/.claude/rules/` apply to every project. Rules without `paths` front matter load at launch.
- **Skills:** `~/.claude/skills/<name>/SKILL.md` becomes the slash command `/<name>`. These files set `disable-model-invocation: true`, so a level only runs when you type it.

Single-file alternative: paste the rule text into `~/.claude/CLAUDE.md`, or a project's `CLAUDE.md` for one project only.

## Import via skill manager (skills only)

Tools that import skills from GitHub can use `https://github.com/arboeh/cABeman` (single skill: `.../tree/main/skills/caveman-full`). Skill managers install skills only; copy `rules/caveman.md` by hand. After import, check that `disable-model-invocation: true` survived, otherwise Claude may load a level on its own. Not tested with a specific tool.

## Change default or scope

- Replace the body of `~/.claude/rules/caveman.md` with another level (rename the file if the name no longer fits).
- Limit the rule to some files:

```markdown
---
paths:
  - "src/**"
---
```

How long a level stays active within a session is up to the model. Repeat the level or say "normal mode" if it drifts.
