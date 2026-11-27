---
name: git-command
description: "Use before running a Git command that changes a repository (staging, committing, amending, rebasing, rewriting history, or building a commit series from a finished tree) and when writing or reviewing a commit message. Covers Conventional Commit subjects and scopes, commit bodies, explicit staging, commit signing and hooks, and the commands that need the user's explicit request."
---

# Git Command

Use these rules when running Git commands or editing Git history. Safety always applies. Load only the reference the operation needs:

| Operation | Load |
| --- | --- |
| Creating a commit, or writing or reviewing a commit message | `references/conventional-commits.md` |
| Amending, rebasing, or otherwise rewriting commits | `references/history-edits.md` |
| Turning an already finished tree into a series of commits | `references/replay-commits.md` and `references/conventional-commits.md` |

## Safety

- Commit when the user or the task asks for a commit. Push only when asked.
- Add a new commit instead of changing an existing one. Amend, squash, or rebase a commit that exists only in this clone when the user or the task asks for it. A commit that has been pushed falls under the last rule of this list.
- Respect the repository's commit signing (`commit.gpgsign`, `gpg.format`, `user.signingkey`) and its hooks. Never pass `--no-gpg-sign`, `-c commit.gpgsign=false`, or `--no-verify` unless the user explicitly asks. When a hook rejects a commit, fix what it reports and commit again.
- Use non-interactive commands. A command that opens an editor or a prompt waits for input that never comes: pass a commit message with `-m` or `-F`, and never run `git add -i`, `git add -p`, or a bare `git rebase -i`.
- Stage by naming each file, or a directory in which every changed file belongs to this commit. Never `git add .`, `git add -A`, or `git commit -a`. Run `git status --short` before staging and `git diff --cached --stat` after, and unstage anything the commit does not own.
- Never stage build output, secrets (`.env` files, keys, credentials), or tool scratch files. In a newly initialized repository, check that `.gitignore` excludes them before the first `git add`.
- These commands discard work or rewrite what others already have: `git reset --hard`, `git checkout -- <path>` and `git restore <path>` on uncommitted changes, `git clean`, `git stash drop` and `git stash clear`, `git branch -D`, `git push --force` in any form, and any amend, rebase, or filter of a commit that has been pushed. Run one only when the user asked for that operation by name. Otherwise report the command you would run and why, and wait.
