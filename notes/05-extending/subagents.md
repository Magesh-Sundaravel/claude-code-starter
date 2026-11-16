# Subagents (`.claude/agents/`)

A specialized assistant that runs in **its own context window**, with its own system prompt, tools and permissions. Only its **final report** returns to the main conversation; the noisy work (searches, logs, file reads) stays inside it.

## File format

A Markdown file. The **body becomes the system prompt**. Only `name` and `description` are required.

```markdown
---
name: code-reviewer
description: Reviews code for quality and best practices
tools: Read, Glob, Grep
model: sonnet
---
You are a code reviewer. ...
```

| Field | Purpose |
|---|---|
| `tools` / `disallowedTools` | Allowlist / denylist of tools |
| `model` | `sonnet`, `opus`, `haiku`, `fable`, full ID, or `inherit` |
| `permissionMode` | e.g. `default`, `acceptEdits`, `plan`, `dontAsk` |
| `maxTurns` | Turn limit |
| `skills` | Skills preloaded at startup |
| `mcpServers`, `hooks`, `memory` | Scoped to this subagent |
| `isolation: worktree` | Isolated git worktree copy |
| `omitClaudeMd` | `true` = start without user/project/local CLAUDE.md (v2.1.271+) |

Multi-word fields are camelCase. Unknown fields are ignored silently.

## Where they live (higher wins on a name clash)

| Priority | Location | Scope |
|---|---|---|
| 1 | Managed settings | Organization |
| 2 | `--agents` CLI flag (JSON) | Current session |
| 3 | `.claude/agents/` | This project |
| 4 | `~/.claude/agents/` | All your projects |
| 5 | Plugin `agents/` | Where plugin is enabled |

**Reverse of skills:** for skills, personal beat project. For subagents, **project beats user**.

## What a subagent's context contains

| Gets | Does NOT get |
|---|---|
| Its own system prompt (file body) | Your conversation history |
| The task message Claude writes when delegating | Skills you already invoked |
| **CLAUDE.md files** (custom agents; not Explore/Plan) | Files Claude already read |
| Git status snapshot (not Explore/Plan) | Auto memory, output style |
| Skills in its `skills:` field | |

- "Zero context" is not quite right: it has CLAUDE.md and its own prompt, just not your chat.
- Claude must put the details in the delegation message. "Fix the bug we just discussed" fails unless Claude spells the bug out.
- A **fork** is the exception: it inherits the parent conversation.

## Built-in subagents

| | Explore | Plan |
|---|---|---|
| Purpose | File discovery, code search | Research during plan mode, before presenting the plan |
| Tools | Read-only (Write/Edit denied) | Read-only (Write/Edit denied) |
| Model | Parent's model | Inherits from parent |
| CLAUDE.md | **Not loaded** | **Not loaded** |
| Git status | **Not loaded** | **Not loaded** |

Others: `general-purpose` (all tools), `claude` (catch-all), `statusline-setup`, `claude-code-guide`.
In plan mode, Claude delegates research to **Plan**, so exploration output stays in a separate context and the main conversation stays read-only.

### Why Explore and Plan skip CLAUDE.md
"To keep research fast and inexpensive." They only search and read.

- The main conversation still has your full CLAUDE.md when it reads their results, so most rules don't need to reach them. Example: my `🚀` style rule works because the **main** Claude writes the final answer.
- It matters only when a rule must shape the **search itself**, e.g. "ignore `generated/`".
- **Fix: restate the rule in the prompt** you give Claude when delegating. Claude passes it into the delegation message.
- Subdirectory CLAUDE.md files and matching path-scoped rules **still load** when they work on files there.
- Alternative (inferred from docs, not stated outright): define your own `Explore` or `Plan` in `.claude/agents/`; it overrides the built-in and, being custom, loads CLAUDE.md.
- Disable both built-ins: `CLAUDE_CODE_DISABLE_EXPLORE_PLAN_AGENTS=1` (v2.1.198+).

## How they run

| How | Effect |
|---|---|
| Automatic delegation | Claude matches the task to `description` ("use proactively" helps) |
| Natural language | "Use the test-runner subagent to fix failing tests" |
| `@"code-reviewer (agent)" ...` | Guarantees that subagent runs |
| `claude --agent code-reviewer` or `"agent"` in `.claude/settings.json` | Whole session runs as that agent |

- Nesting: subagents can spawn subagents, up to 3 layers by default (`CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH`, `1` turns it off).
- Disable one: `"deny": ["Agent(Explore)"]` in permissions.

## Quiz (answered)

1. "Fix the bug we just discussed" → subagent won't know it. It sees only the task message. ✅
2. Project CLAUDE.md says "end with 🚀": a **custom** subagent loads it (should follow); **Explore/Plan** do not. I said "no" for both, which was wrong for custom agents.
3. CLAUDE.md says "never read `generated/`", Claude uses Explore → it will **not** skip on its own. Restate the rule in the delegation prompt. ✅

Source: https://code.claude.com/docs/en/sub-agents
