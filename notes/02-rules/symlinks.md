# Share rules across repos with symlinks

## What is a symlink?

A **symlink** (symbolic link) is a **shortcut** to another file or folder. The real file lives in one place,
and the symlink only points to it. Anything that reads the symlink reads the real file.

```
~/shared-claude-rules/clock.md      ← the REAL file (only one copy)
        ▲            ▲
        │            │
repo-A/.claude/rules/shared   repo-B/.claude/rules/shared   ← symlinks (shortcuts)
```

```bash
ln -s <real thing> <where to put the shortcut>
#  ln = make a link, -s = symbolic (shortcut)
```

| Without symlinks | With symlinks |
|---|---|
| Copy the file into every repo | 1 real file + shortcuts |
| A change means editing every copy | A change means editing 1 file, and every repo sees it |
| Copies drift apart | Always in sync |

### Demo

```bash
cd /tmp
echo "hello from real file" > real.txt
ln -s real.txt shortcut.txt
ls -l shortcut.txt        # shortcut.txt -> real.txt
cat shortcut.txt          # hello from real file
echo "changed!" > real.txt
cat shortcut.txt          # changed!  (follows the real file)
rm real.txt
cat shortcut.txt          # error: No such file or directory
```

### ⚠️ Deleting the real file

When the real file is deleted, the symlink is left broken (also called "dangling"). It still exists, but it points at nothing:
A shortcut holds only an address, not a copy of the content. If the real file is gone, there's nothing left to read.

→ If you delete or move `~/shared-claude-rules`, every repo linking to it silently loses those rules.

## Symlinks in Claude Code

### 1. Share rules across chosen repos

```bash
ln -s ~/shared-claude-rules .claude/rules/shared          # whole folder
ln -s ~/company-standards/security.md .claude/rules/security.md   # one file
```

| Point | Detail |
|---|---|
| Target **outside** the project | Treated like an external `@import`, so Claude shows a **one-time approval dialog per project** |
| After approval | Only rules **without `paths:`** load. Path-scoped rules through outside links don't. |
| Network paths (`/net`, `/Network`, `\\server\share`) | Never loaded (`\\wsl$` is fine) |
| Circular links | Detected and handled safely |
| Want no dialog? | Put the rules in `~/.claude/rules/` instead (applies to all projects) |

**Why a dialog?** Files in `~/.claude/rules/` are on your system and you wrote them, so they're trusted.
A project can come from someone else (a teammate or a cloned repo), and a link inside it could pull in
instructions you never saw. Claude asks before loading anything from outside the project.

### 2. `CLAUDE.md` → `AGENTS.md`

```bash
ln -s AGENTS.md CLAUDE.md
```
One instructions file for Claude and other AI tools. Caveats:
- Claude's Edit/Write tools won't write through the symlink. Claude edits `AGENTS.md` directly.
- On **Windows**, use `@AGENTS.md` inside `CLAUDE.md` instead. Symlinks there need admin rights or Developer Mode.

### 3. Back up user rules in git (dotfiles)

```bash
ln -s ~/dotfiles/claude-rules ~/.claude/rules
```
User scope, so no dialog appears (Cowork sessions are the exception).

## Which to use?

| Need | Use |
|---|---|
| A rule in **every** project | `~/.claude/rules/` |
| A rule in **some** projects | Symlink into those repos' `.claude/rules/` |
| Claude + other AI tools share instructions | `CLAUDE.md` → `AGENTS.md` (or `@AGENTS.md`) |
| A rule in **one** project | A plain file in `.claude/rules/` |

## Quiz (answered ✅)

**Symlink basics**
1. Edit the real file: do symlinks show the change? **Yes.**
2. Delete the real file: what happens to the symlink? **It's broken (dangling).** It still exists but points at nothing. (My first answer, "it stays the same", was wrong.)
3. In `ln -s A B`, which is real? **A is the real file, B is the shortcut.**

**Symlinks in Claude**
1. FastAPI rules needed in 3 of 10 repos? **Symlinks:** one real rules file, linked into those 3 projects.
2. Why a dialog for symlinks but not for `~/.claude/rules/`? **Rules on my system are mine. Rules from someone else's project could be suspicious, so Claude asks for permission first.**
3. Outside-target linked rule with `paths: ["**/*.py"]`: does it load? **No.**

Source: https://code.claude.com/docs/en/memory#share-rules-across-projects-with-symlinks
