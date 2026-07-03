---
name: git-command
description: "Apply Git operation preferences for status, diffs, staging, commits, amends, rebases, history edits, commit messages, changelog summaries, and commit hygiene."
---

# Git Command

Use these rules when running Git commands or editing Git history. Read only the reference files needed for the current operation.

Always read:

- `references/safety.md`

Then read based on the task:

- `references/conventional-commits.md` when creating commits, editing commit messages, reviewing commit subjects, or preparing PR-style history.
- `references/replay-commits.md` when initializing Git history from an already-finished tree or reconstructing a meaningful series of commits.
- `references/history-edits.md` when amending, rebasing, rewriting, cleaning history, or inspecting path-specific logs.

## Quick Rules

- Follow the global Git signing rules.
- Prefer non-interactive Git commands.
- Do not use broad staging such as `git add .`, `git add -A`, or repository-wide pathspecs for normal commits. Stage explicit files or explicit narrow directories that match the intended commit scope.
- Use Conventional Commit subjects for commits.
- Add commit bodies only when they explain reason, impact, migration, security, deployment, lifecycle, or non-obvious tradeoffs.
- Do not stage generated build output, local secrets, or transient tool output.
