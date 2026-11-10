# Nested CLAUDE.md (subfolders)

A `CLAUDE.md` (or `CLAUDE.local.md`) inside a subfolder is **lazy-loaded**.
It is NOT loaded at startup. It loads the first time Claude reads, writes, or
edits a file in that subfolder (a Bash `cat`/`head` of a file counts too).

Example in this repo: `backend/CLAUDE.md` loads only after Claude touches
something like `backend/app.py`.

`CLAUDE.local.md` follows the same rules as `CLAUDE.md`:
- Root `./CLAUDE.local.md` loads at startup and comes back after `/compact`,
  just like root `CLAUDE.md`. Within a folder it's added **after** that
  folder's `CLAUDE.md`, so it's read last.
- `backend/CLAUDE.local.md` lazy-loads, exactly like `backend/CLAUDE.md`.

| File                       | At startup | After `/compact` or `/clear`               |
|----------------------------|------------|--------------------------------------------|
| `./CLAUDE.local.md` (root) | Loaded     | Loaded again                               |
| `backend/CLAUDE.local.md`  | Not loaded | Dropped until a `backend/` file is touched |

## Lifecycle

| Event                    | Root `CLAUDE.md`         | `backend/CLAUDE.md`                      |
|--------------------------|--------------------------|------------------------------------------|
| Session start            | Loaded                   | Not loaded                               |
| First file touched in `backend/` | Already there    | Loaded                                   |
| Later turns              | Stays                    | Stays                                    |
| After `/compact`         | Re-read from disk, re-injected | Dropped, reloads on next `backend/` file touch |
| After `/clear`           | Loaded                   | Dropped, reloads on next `backend/` file touch |
| New session              | Loaded                   | Not loaded                               |

## `/compact` vs `/clear`

Both behave the **same** for nested files: the nested file is gone until a
file in that subfolder is touched again. Neither one breaks it.

The only difference is the conversation:
- `/compact` keeps a summary of the chat.
- `/clear` wipes the chat completely.

## How to see it

- When a nested file loads, the terminal shows a `Loaded <path>` line.
- Nested files do **not** appear under "Memory files" in `/memory`.
- The `InstructionsLoaded` hook can log which CLAUDE.md files load and when.

## If an instruction "disappears" after `/compact`

It was probably one of these:
1. Given only in chat, never written to a file.
2. In a nested CLAUDE.md that hasn't reloaded yet.
3. In a path-scoped rule (`paths:` frontmatter) that hasn't matched a file since.

Fix: put it in the root `CLAUDE.md` if it must always apply.

## Quiz (answered ✅)

1. Is `backend/CLAUDE.md` loaded at session start? **No**
2. After `/compact`, does `backend/CLAUDE.md` come back by itself? **No**, only when a `backend/` file is touched.
3. Does `/clear` fix that? **No**, it behaves the same. Only the chat history differs.

Source: https://code.claude.com/docs/en/memory
