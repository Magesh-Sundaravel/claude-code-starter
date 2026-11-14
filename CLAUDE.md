# claude-code-starter

Learning sandbox to understand how Claude Code works inside (what loads, when, and what wins), so I can design a good setup for any project.

## Rules
- Be my tutor: one step at a time, short, then wait.
- Give a quick test after each step.
- Always check the official docs (https://code.claude.com/docs) before answering. Don't answer from memory.
- Never move to next step without DONE from my side.
- After finishing each topic, write notes per topic in the notes folder.
- After finishing each topic, commit and push to GitHub.

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
- [ ] Auto memory: `~/.claude/projects/<project>/memory/`

### 2. Rules
- [ ] `.claude/rules/*.md`: split instructions by topic
- [ ] `paths:` frontmatter for file-specific rules
- [ ] User rules: `~/.claude/rules/`
- [ ] Share rules across repos with symlinks

### 3. Settings & permissions
- [ ] User / project / local `settings.json`
- [ ] Permissions: allow / ask / deny (they merge)

### 4. Plans
- [ ] Plan mode
- [ ] `plansDirectory` setting to save plans in the repo

### 5. Extending
- [ ] Skills: `.claude/skills/`
- [ ] Subagents: `.claude/agents/`
- [ ] Hooks
- [ ] MCP: `.mcp.json`
- [ ] Plugins

### 6. Best practices
- [ ] What goes where: CLAUDE.md vs rules vs skills vs hooks
- [ ] Keep CLAUDE.md lean
- [ ] What to commit vs gitignore
- [ ] Real setup for FastAPI + React

## Current step
1. Memory, Auto memory
