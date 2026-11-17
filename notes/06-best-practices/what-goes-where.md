# What goes where

Ask for each instruction: **how often does Claude need it, and must it be guaranteed?**

## Comparison

| Feature | Loads | Use for | Guaranteed? |
|---|---|---|---|
| CLAUDE.md | Every session | Always-true facts: build commands, conventions, "never do X" | No |
| Rules (`.claude/rules/`) | Every session, or only when matching files open (`paths:`) | Language/folder-specific guidance, keeps CLAUDE.md small | No |
| Skill | Description at start, full text when used | Reference material, `/name` workflows | No |
| Subagent | When spawned, isolated context | Noisy/large tasks, only summary returns | No |
| MCP | Tool names at start, schemas on demand | Connecting external systems | n/a |
| Hook | On lifecycle event | Things that must happen every time | **Yes** |
| Plugin | Packages the above | Reuse across repos/teams | n/a |

## Decision rules

- Claude should always know it: CLAUDE.md.
- Only for certain files: rule with `paths:`.
- Needed sometimes, or a workflow you trigger: skill.
- Must hold every time: hook. "Never edit `.env`" in CLAUDE.md is a request; a `PreToolUse` hook that blocks it is enforcement.
- Floods your conversation: subagent.
- Needs an outside service: MCP, paired with a skill that teaches how to use it.

## Context cost

| Feature | Cost |
|---|---|
| CLAUDE.md | Every request |
| Skill | Description every request, full text when used (`disable-model-invocation: true` = zero until invoked) |
| MCP | Low (tool search defers schemas) |
| Subagent | Isolated from main session |
| Hook | Zero unless it returns output |

Keep CLAUDE.md under 200 lines; move reference material to skills or rules.

## When several levels define the same thing

| Feature | Behavior |
|---|---|
| CLAUDE.md | Additive, all levels contribute |
| Skills, subagents, MCP | Override by name (MCP: local > project > user) |
| Hooks | Merge, all fire |

## Build up over time

| Trigger | Add |
|---|---|
| Claude gets a convention wrong twice | CLAUDE.md |
| Same prompt typed repeatedly / playbook pasted 3 times | Skill |
| Copying data from a system Claude can't see | MCP server |
| Side task floods the conversation | Subagent |
| Want it to happen every time | Hook |
| Second repo needs the same setup | Plugin |

## Combos

- Skill + MCP: MCP connects, skill teaches schema/patterns.
- CLAUDE.md + skill: short rule in CLAUDE.md, full guide in the skill.
- PostToolUse hook + skill: hook runs linter, skill teaches how to fix errors.

## Quiz (answered)

1. Format after every Python edit: hook, `PostToolUse` (`Edit|Write`); CLAUDE.md is only a request. ✅
2. 150-line API style guide, needed sometimes: skill. I said rules/nested CLAUDE.md; those work but a skill costs nothing until used. Partly right.
3. Rules for `*.tsx` only: rule file with `paths:` frontmatter. ✅
4. Read Postgres: MCP connects, a skill teaches the schema. I mixed them up. Partly right.
5. Zero idle context: hook. ✅

Source: https://code.claude.com/docs/en/features-overview
