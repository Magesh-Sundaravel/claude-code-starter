# `plansDirectory` setting

Chooses where plan mode writes its plan files.

> **Remember:** with no setting, plans go to **`~/.claude/plans`** in your **home folder**, **not** `.claude/plans` in the project. That is why the setting is useful: it brings plans into the repo.

## Facts

| | |
|---|---|
| Type | String, a path **relative to the project root** |
| Default | Unset → `~/.claude/plans` |
| Scope | Any settings file |
| Example | `{ "plansDirectory": "./plans" }` |

The folder name is your choice. `.claude/plans` is reasonable, but the docs don't require it.

## Share or keep private

| Goal | Settings file | Plans folder in git |
|---|---|---|
| Share plans with the team | `.claude/settings.json` (committed) | Commit it (`git add`), don't ignore it |
| Plans in the repo, only for me | `.claude/settings.local.json` | Add the folder to `.gitignore` |
| No setting | n/a | Lives in `~/.claude/plans`, outside the repo |

- The local settings file is ignored by git, but the **plans folder is not**. Without a `.gitignore` entry, plans show as untracked and can be committed by accident (standard git behavior, not from the Claude docs).
- Sharing needs **both**: the shared setting and the committed plans.
- Docs say plan files are exempt from the protected `.claude/` folder rule. Not tested yet for `.claude/plans`.

## Quiz (answered ✅)

1. `"plansDirectory": "docs/plans"` in `.claude/settings.json`, started from repo root → plan saved in **`docs/plans`**. (My fallback guess `.claude/plans` was wrong, the default is `~/.claude/plans`.)
2. Plans in the repo, only for me → **`.claude/settings.local.json`**, plus `.gitignore` for the folder (I missed this part).

Source: https://code.claude.com/docs/en/settings-reference#plansdirectory
