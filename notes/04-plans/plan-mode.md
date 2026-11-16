# Plan mode

Claude researches and writes a plan **before** touching code. In the docs' words, it "reads files, runs shell commands to explore, and writes a plan, but does not edit your source."

## What's allowed

| | In plan mode |
|---|---|
| Reading and exploring files | Allowed |
| Editing source files | **Blocked** until you approve the plan |
| Shell commands | Depends on setup; often only read-only ones run without a prompt |

## How to turn it on

| How | Scope |
|---|---|
| `Shift+Tab` (cycle modes) | Current session |
| `/plan <prompt>` | One prompt |
| `claude --permission-mode plan` | Whole session |
| `"permissions": {"defaultMode": "plan"}` in `.claude/settings.json` | Every terminal session in the project |

- `defaultMode` is a **permissions setting**, so the settings scopes from step 3 apply (shared file = teammates get it too).
- VS Code ignores project settings for this; use `claudeCode.initialPermissionMode` in user settings.
- Leave without approving: press `Shift+Tab` again. At the approval prompt press `Esc` first, because `Shift+Tab` there **approves** the plan.

## When the plan is ready

| Choice | Result |
|---|---|
| Yes, and use auto mode (or "auto-accept edits" if auto isn't available) | Approve, start editing |
| Yes, manually approve edits | Approve, review each edit |
| No, keep planning | Stay in plan mode, say what to change |

`Ctrl+G` opens the plan in your editor to edit before approving.

## Where plans are saved
Plan files live in `~/.claude/plans/` by default, or in the folder set by `plansDirectory` (next topic).

## Quiz (answered ✅)

1. Make every session in the repo start in plan mode, shared with the team → `permissions.defaultMode: "plan"` in `.claude/settings.json`.
2. Plan mode on, Claude wants to edit `app.py` → **blocked** until the plan is approved.

Source: https://code.claude.com/docs/en/permission-modes
