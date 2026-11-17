# claude-code-starter

Learning sandbox to understand how Claude Code works inside (what loads, when, and what wins), so I can design a good setup for any project.

## Rules
- Be my tutor: one step at a time, short, then wait.
- Give a quick test after each step.
- Always check the official docs (https://code.claude.com/docs) before answering. Don't answer from memory.
- Never move to next step without DONE from my side.
- After finishing each topic, write notes in notes/<NN-section>/<topic>.md (tables, short sections, answered quiz, docs source).
- After finishing writing the notes, commit and push to GitHub.

## Style Guide
 Style guide: @docs/style.md



## Roadmap

### 1. Memory (CLAUDE.md)
- [x] Scopes (load order, all concatenated): Managed → User → Project → Local
- [x] User: `~/.claude/CLAUDE.md`
- [x] Project: `CLAUDE.md` or `.claude/CLAUDE.md`
- [x] Local: `CLAUDE.local.md`
- [x] Nested CLAUDE.md in subfolders (lazy-loaded)
- [x] `@path` imports
- [x] `/memory` command
- [x] Auto memory: `~/.claude/projects/<project>/memory/`

### 2. Rules
- [x] `.claude/rules/*.md`: split instructions by topic
- [x] `paths:` frontmatter for file-specific rules
- [x] User rules: `~/.claude/rules/`
- [x] Share rules across repos with symlinks

### 3. Settings & permissions
- [x] User / project / local `settings.json`
- [x] Permissions: allow / ask / deny (they merge)

### 4. Plans
- [x] Plan mode
- [x] `plansDirectory` setting to save plans in the repo

### 5. Extending
- [x] Skills: `.claude/skills/`
- [x] Subagents: `.claude/agents/`
- [x] Hooks
- [x] MCP: `.mcp.json`
- [ ] Plugins

### 6. Best practices
- [ ] What goes where: CLAUDE.md vs rules vs skills vs hooks
- [ ] Keep CLAUDE.md lean
- [ ] What to commit vs gitignore
- [ ] Real setup for FastAPI + React

## Current step
5. Extending, Plugins
