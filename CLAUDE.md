# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

A small Express (Node 22, CommonJS) REST API for users, used as the starter project for a Claude Code course.

## Commands

```bash
npm run dev                                   # start with auto-reload on http://localhost:3000 (PORT overrides)
npm test                                      # run all tests (node:test)
node --test tests/users.test.js               # run one test file
npm run lint                                  # ESLint; CI runs lint then test
```

## Architecture

- `server.js` builds the Express app, mounts one router per resource (`/users`, `/health`), and exports `app`. It only calls `listen()` when run directly (`require.main === module`), so tests import `app` without opening a port.
- `routes/<resource>.js` — each exports an `express.Router()`; register new ones in `server.js`.
- `db/store.js` — in-memory data (resets on restart). Routes access data only through its exported functions, never the arrays directly.
- `tests/` — `node:test` + `node:assert` + `supertest` against the exported `app`. The store is module-level state, so writes in one test are visible to later tests in the same file.

## Conventions

- Use `node:test` and `node:assert`, not Jest/Mocha; no extra test framework is installed.
- Error responses are JSON `{ error: "<message>" }` with the matching status (400 validation, 404 not found); successful creates return 201.
- Use double quotes and semicolons, matching existing files.
- Config comes from env vars (see `.env.example`); never read or commit `.env`.
