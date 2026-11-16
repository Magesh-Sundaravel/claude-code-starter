# Permissions: allow / ask / deny

Rules in `settings.json` under `permissions`. Each rule names a tool and what it may do, e.g. `Bash(npm run test *)`, `Read(./.env)`.

## The three rules

| Rule | Effect |
|---|---|
| `allow` | Runs without asking |
| `ask` | Prompts every time |
| `deny` | Blocked |

## ORDER (the key point)

Rules are checked in this order. **The first match decides.**

```
1. deny   ← checked FIRST, always wins
2. ask    ← checked second
3. allow  ← checked LAST, weakest
```

**deny > ask > allow**

- Specificity doesn't matter. Broad `deny: Bash(aws *)` beats narrow `allow: Bash(aws s3 ls)`. An allow can't carve an exception out of a deny.
- Scope doesn't matter. Deny from user, project, local or managed beats an allow from any other scope.
- The rule **type** decides, not the file it lives in.

## Two different orders, don't mix them up

| | Scope order (which FILE wins) | Rule-type order (which RULE wins) |
|---|---|---|
| Applies to | Normal keys like `model` | Permission rules |
| Order | Managed → CLI → Local → Shared → User | **deny → ask → allow** |
| Lists | `permissions.*` lists **merge** across all files first | Then deny/ask/allow order is applied to the merged list |

Only exception: **managed** permission rules can't be overridden by anything, including command line.

## Hooks

- A PreToolUse hook can't override deny or ask rules. A matching deny still blocks, a matching ask still prompts, even if the hook returns "allow".
- A blocking hook (exit code 2) runs **before** permission rules and stops the call even if an allow matches.
- Pattern: allow `Bash` broadly, then block a few commands with a hook.

## Quiz (answered ✅)

1. User `allow: Bash(git push *)`, project `deny: Bash(git push *)` → `git push origin main` is **denied** (deny wins, from any scope).
2. Project `ask: Bash(npm *)`, local `allow: Bash(npm run test)` → **asks**. Ask beats allow because of the rule type, not the file. Swap the files and it still asks.

Source: https://code.claude.com/docs/en/permissions
