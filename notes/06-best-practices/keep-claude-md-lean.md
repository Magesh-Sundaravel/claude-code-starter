# Keep CLAUDE.md lean

CLAUDE.md loads into context **every request**, so every line costs tokens. It is delivered as a user message: Claude tries to follow it, but compliance is not guaranteed, especially for vague or conflicting lines.

## The three rules

| Rule | Meaning |
|---|---|
| Size | Under **200 lines** per file. Longer files cost more context and reduce adherence |
| Structure | Group related instructions under headers and bullets, not dense paragraphs |
| Consistency | Contradicting instructions make Claude pick one arbitrarily; review all CLAUDE.md files and rules periodically |

## Be specific and checkable

| Vague | Specific |
|---|---|
| "Format code properly" | "Use 2-space indentation" |
| "Test your changes" | "Run `npm test` before committing" |
| "Keep files organized" | "API handlers live in `src/api/handlers/`" |

## How to trim

| Move this | To |
|---|---|
| Rules for part of the codebase | Path-scoped rules (`paths:`) |
| Reference material, playbooks | Skills (on demand) |
| Things that must always happen | Hooks |
| Notes for humans only | `<!-- HTML comments -->` (stripped before reaching Claude) |

- `@imports` organize a file but do **not** reduce context cost: imported files load with the file that imports them.
- Nested CLAUDE.md in subfolders lazy-load, so they do save context.
- `/doctor prompt-audit` finds outdated instructions, missing files/commands and contradictions. It reports and proposes edits; nothing changes until you ask.

## Naming: user vs global

"Global" in chat meant the **User** scope: `~/.claude/CLAUDE.md` (home folder, applies to every project). Project is `CLAUDE.md` in the repo root. Load order: Managed, User, Project, Local.

## Side topic: `@file` mentions vs files Claude reads

| Case | What enters context | Notes |
|---|---|---|
| `@file` in your prompt | File content, no Read tool call | Only if it fits the Read limit (25,000 tokens default); text files over 256KB are not included |
| `@folder` | A listing, not contents | |
| `@file` also adds | CLAUDE.md in that file's folder and parents | |
| Claude reads a file (Read tool) | Result added to the conversation | Stays in the conversation |

- `@` makes no tool call, so `PreToolUse` hooks don't run for it. Block paths with a `Read` deny rule or set `CLAUDE_CODE_DISABLE_FILE_MENTIONS=1`.
- After compaction, a mentioned/read file is summarized away; Claude Code re-reads up to 5 recent files, and a file over 5,000 tokens returns as a path reference only. CLAUDE.md is re-injected from disk.

| | CLAUDE.md | File you `@` or Claude reads |
|---|---|---|
| Role | Standing instructions, delivered as a user message | Material for the task you asked about |
| Persistence | Re-injected after compaction | In the conversation, can be summarized away |
| Following | Claude tries to follow, no guarantee | Used as your prompt directs |

The last two rows are my reading; the docs don't state it outright. For standing rules use CLAUDE.md, or an `@import` inside it (persists, but costs every request). To make Claude follow a mentioned file, say so in the prompt.

## Quiz (answered)

1. Max size: 200 lines; beyond it costs more and reduces adherence. ✅
2. "Write clean code" rewritten: "code must pass all tests before committing". ✅ (name the command, e.g. `pytest`; a hook if it must be guaranteed)
3. 300 lines split with `@imports`: no drop, everything still loads. ✅
4. 40-line deploy checklist: skill. Type-hints rule: rule file with `paths: ["**/*.py"]`. ✅
5. Find outdated/conflicting instructions: `/doctor prompt-audit` (I said `/doctor`). Almost.

Sources: https://code.claude.com/docs/en/memory, https://code.claude.com/docs/en/common-workflows, https://code.claude.com/docs/en/context-window
