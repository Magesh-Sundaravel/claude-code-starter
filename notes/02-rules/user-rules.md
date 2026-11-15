# User rules (`~/.claude/rules/`)

Personal rules that apply to **every project** on your machine.

```text
~/.claude/rules/
├── preferences.md    # Your personal coding preferences
└── workflows.md      # Your preferred workflows
```

## Facts

| Point | Detail |
|---|---|
| Location | `~/.claude/rules/*.md` |
| Scope | Every project on this machine |
| Use for | Personal preferences that aren't tied to one project |
| Load order | User rules load **before** project rules, so project rules appear later in context |
| Conflicts | Neither one overrides the other. Claude may follow either, so keep them consistent. |
| `paths:` | Supported. Globs are relative to the session's **primary working directory**. |
| Trust | Your own config, so imports/symlinks need no approval dialog (except in Cowork sessions) |

## User rules vs project rules

| | User `~/.claude/rules/` | Project `.claude/rules/` |
|---|---|---|
| Applies to | All projects | This repo only |
| Committed to git | No | Yes (shared with the team) |
| Order in context | Earlier | Later |
| `paths:` base dir | Primary working dir | Folder holding `.claude/` |

## Same filename in both?

`~/.claude/rules/greeting.md` and `.claude/rules/greeting.md` **don't replace each other**. Both load,
and their contents are concatenated into context.

## Experiment (optional, skipped)

```bash
mkdir -p ~/.claude/rules
echo 'When I say "marco", reply "Polo from USER rules"' > ~/.claude/rules/greeting.md
```
Restart, then type `marco` in any repo. I chose not to change my user rules for now.

## Quiz (answered ✅)

1. Same filename in user and project rules: does one replace the other? **No**, both are added to context.
2. Which appears later? **Project rules.** If they conflict, which one wins isn't guaranteed, so keep them consistent.
3. Does a user rule work in a different repo? **Yes**, it applies in every project.

Source: https://code.claude.com/docs/en/memory#user-level-rules
