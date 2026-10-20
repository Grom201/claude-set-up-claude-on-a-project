# Notes: setting up Claude Code on this project

## CLAUDE.md — what I kept and what I cut

**Kept:**
- A one-line description, so Claude knows this is a small Express (Node 22, CommonJS) REST API.
- **Commands** — `npm run dev`, `npm test`, running a single test file, and `npm run lint` (with a note that CI runs lint then test), so Claude can verify its own changes the same way CI does.
- **Architecture** — the non-obvious parts: `server.js` exports `app` and only calls `listen()` when run directly (that's why tests don't open a port), routes go through `db/store.js` functions only, and the store is shared module-level state across tests in one file.
- **Conventions** — concrete "use X, not Y" rules: `node:test` + `node:assert` instead of Jest/Mocha, the `{ error: "<message>" }` JSON shape with 400/404/201 statuses, double quotes and semicolons, and config from env vars only.

**Left out:**
- A file-by-file listing of the repo — Claude can see that itself with `ls`, and it goes stale.
- Generic advice ("write clean code", "add tests") that doesn't change behaviour.
- Anything from `.env` or other secrets, and one-off tasks for this course — CLAUDE.md is loaded every session, so every line has to stay true and useful long-term.
- Lines that mostly added context noise without changing how Claude works on the project — every extra line costs context in every session, so only rules that affect Claude's behaviour stayed.

## Permission rules

`.claude/settings.json` (committed, shared with the team):

| Rule | Type | Why |
|------|------|-----|
| `Bash(npm test:*)` | allow | Safe, read-only, run constantly — no reason to be prompted every time. |
| `Bash(git push:*)` | ask | Pushing is outward-facing, so I want to confirm each one. |
| `Read(./.env)` | deny | Keeps real secrets out of Claude's context. |
| `Bash(git push --force:*)` | deny | Force-push can overwrite other people's history on the remote. |

**What could go wrong without the deny rules:**

Without `Read(./.env)`, Claude could open `.env` while "checking the config", and real secrets would end up in the conversation context or get pasted into a commit or PR. Without denying `git push --force`, Claude could "fix" a rejected push after a rebase by force-pushing, silently deleting teammates' commits on the remote. A deny rule holds even when I'm tired and approve prompts without reading them, which is why these two are deny, not ask.

`.claude/settings.local.json` holds only my personal output-style preference and is git-ignored.

## Verification

- `/memory` shows `CLAUDE.md` as loaded.
- `/permissions` lists the allow / ask / deny rules above.
- Asking "How do I run the tests here?" in a fresh session gets answered from `CLAUDE.md` (`npm test`, or `node --test tests/users.test.js` for one file).
