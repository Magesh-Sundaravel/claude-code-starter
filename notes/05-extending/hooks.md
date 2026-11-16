# Hooks

A **hook** is a handler (usually a shell command) that Claude Code runs at a fixed point in its lifecycle. It is **deterministic**: it always runs. A CLAUDE.md instruction is only a request Claude may skip.

## The three pieces

| Piece | Meaning | Example |
|---|---|---|
| Event | When it fires | `PreToolUse`, `PostToolUse`, `Stop`, `SessionStart`, `Notification` |
| Matcher | Which occurrences to filter (tool name, case-sensitive) | `"Edit|Write"`, `"Bash"` |
| Handler | What runs | `command` (default), `http`, `prompt`, `agent` |

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [{ "type": "command", "command": "your-script" }]
      }
    ]
  }
}
```

## Common events

| Event | When | Can block? |
|---|---|---|
| `PreToolUse` | Before a tool call | Yes |
| `PostToolUse` | After a tool call succeeds | No (already happened) |
| `UserPromptSubmit` | Prompt submitted, before Claude sees it | Yes |
| `Stop` | Claude finishes responding | Yes |
| `SessionStart` | Session begins/resumes | No |
| `Notification` | Claude needs your input | No |

Events with **no matcher support** (always fire): `UserPromptSubmit`, `Stop`, `PostToolBatch`, and a few others.

## Where hooks live

| Location | Scope | Shareable |
|---|---|---|
| `~/.claude/settings.json` | All your projects | No |
| `.claude/settings.json` | This project | Yes (commit) |
| `.claude/settings.local.json` | This project | No (gitignored) |
| Managed policy | Organization | Yes |
| Plugin `hooks/hooks.json` | While plugin enabled | Yes |
| Skill / subagent frontmatter | While that skill/subagent is active | Yes |

- `/hooks` lists everything configured, grouped by event.
- `"disableAllHooks": true` turns them off.
- Use `"$CLAUDE_PROJECT_DIR"/.claude/hooks/x.sh` in `command` so the script is found from any directory.

## Input: JSON on stdin

Every event sends `session_id`, `cwd`, `hook_event_name`, plus event-specific fields.

| Event | Extra fields |
|---|---|
| `PreToolUse` / `PostToolUse` | `tool_name`, `tool_input` (Bash: `tool_input.command`; Edit/Write: `tool_input.file_path`) |
| `UserPromptSubmit` | `prompt` |
| `SessionStart` | `source` (`startup`, `resume`, `clear`, `compact`, `fork`) |

`jq` pulls out a field: `jq -r '.tool_input.command'` (`-r` = raw, no quotes).

## Output: exit code + stderr

| Exit | Meaning |
|---|---|
| **0** | No objection. On `PreToolUse` it does **not** approve; the normal permission flow (allow/ask/deny, prompts) still applies. |
| **2** | **Block.** Reason written to **stderr** (`echo "..." >&2`) goes to Claude. |
| any other (e.g. 1) | Script error: action **proceeds**, error only logged. |

Some events can't be blocked (`SessionStart`); there exit 2 only shows stderr to the user.

**JSON alternative:** exit 0 and print JSON, e.g. `{"hookSpecificOutput":{"hookEventName":"PreToolUse","permissionDecision":"deny","permissionDecisionReason":"..."}}`. `permissionDecision` = `deny`, `ask` or `allow`. Use exit 2 **or** JSON per hook, not both.

## Hands-on: block `rm -rf`

`.claude/hooks/block-rm-rf.sh` (needs `chmod +x`):

```bash
#!/bin/bash
INPUT=$(cat)
COMMAND=$(echo "$INPUT" | jq -r '.tool_input.command')
if echo "$COMMAND" | grep -q "rm -rf"; then
  echo "Blocked: rm -rf is not allowed in this project" >&2
  exit 2
fi
exit 0
```

`.claude/settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          { "type": "command", "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/block-rm-rf.sh" }
        ]
      }
    ]
  }
}
```

Live test: Claude tried `rm -rf <path>` and got `PreToolUse:Bash hook error: ... Blocked: rm -rf is not allowed in this project`. Nothing ran.

## Optional (not built): auto-run Ruff after edits

Same pattern, `PostToolUse` + `Edit|Write`, only on `.py` files:

```json
"command": "f=$(jq -r '.tool_input.file_path') && case \"$f\" in *.py) ruff check --fix \"$f\" && ruff format \"$f\" ;; esac"
```

- `ruff check --fix` fixes lint; `ruff format` formats (separate commands).
- `PostToolUse` is right because the edit must exist first; a non-zero Ruff exit does not undo the edit.
- Silent on success; check the file itself.
- Bash edits (`sed`, etc.) don't trigger it; use a `FileChanged` hook for that.

**Install Ruff for this project only (not global):**

```bash
uv init --bare          # minimal pyproject.toml (if none exists)
uv add --dev ruff       # into ./.venv, recorded in pyproject.toml
```

Then prefix with `uv run`: `uv run ruff check --fix "$f" && uv run ruff format "$f"`.

| | `uv add --dev ruff` | `uv tool install ruff` |
|---|---|---|
| Scope | This project | Global (all projects) |
| Repo files | `pyproject.toml`, `uv.lock`, `.venv/` | none |
| Reproducible for teammates | Yes | No |

Skipped in this repo: it's a notes sandbox with no Python code. Right choice for a real FastAPI project.

## Quiz (answered)

1. Why a hook over "always run the formatter" in CLAUDE.md? Deterministic. ✅
2. Block edits to `.env`: `PreToolUse` + matcher `Edit|Write`; the path check happens in the script (or with `if`, e.g. `Edit(*.env)`). I forgot the matcher at first.
3. Settings files holding hooks: `~/.claude/settings.json`, `.claude/settings.json`, `.claude/settings.local.json` (not committed). ✅
4. Command field holding the Bash command: `tool_input.command` (I said `tool_input`, one level short).
5. Does exit 0 auto-approve? **No**, only "no objection"; normal permissions still apply. (I said yes, wrong.)
6. Block code and reason channel: exit **2**, reason to **stderr**. (I said "in the claude code", wrong.)
7. Would `exit 1` block? No, only 2 blocks. ✅
8. Why `PreToolUse` not `PostToolUse`? Must prevent the action before it happens. ✅

Source: https://code.claude.com/docs/en/hooks-guide (full reference: https://code.claude.com/docs/en/hooks)
