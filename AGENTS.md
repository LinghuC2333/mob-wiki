# Mob-Wiki Repository Rules

This is the team shared knowledge base. All wiki operations go through the MCP tools.

## File Permissions
- `raw/` — read only. Never modify source documents.
- `wiki/` — read/write. You are responsible for maintaining these pages.
- `schema.md` — read only. Follow its conventions when writing pages.
- `db/` — managed by the server. Do not touch directly.

## Before Writing
Always `git pull` before any write operation to avoid merge conflicts.

## After Writing
Always `git add . && git commit && git push` after wiki changes.

## Direct Main Push Authorization
- The repository owner authorizes agents maintaining `cdotlock/mob-wiki` to commit and push completed changes directly to `main`.
- A pull request or an additional merge/push confirmation is not required for work within the user's requested scope, unless the user explicitly asks for a PR or review first.
- Before pushing, sync with `origin/main`, review the diff, and run checks appropriate to the change. Stage only the intended changes and preserve unrelated user work.
- Push normally; never force-push `main` or bypass repository protections. If the remote advances, incorporate its changes and rerun affected checks before retrying.
- This authorization covers this repository's maintenance workflow; it does not authorize unrelated changes, destructive operations, or changes to GitHub access controls.

## Commit Messages
- `wiki: ingest <source>` — new source ingested
- `wiki: update <page>` — existing page updated
- `wiki: lint fix` — automated lint fixes
- `chore: <desc>` — tooling/server changes
