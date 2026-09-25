---
name: git-command
description: "Apply Git operation preferences for status, diffs, staging, commits, amends, rebases, history edits, commit messages, changelog summaries, and commit hygiene."
---

# Git Command

Use these rules when running Git commands or editing Git history. The Safety and Quick Rules sections below always apply. Read only the reference files needed for the current operation:

- `references/conventional-commits.md` when creating commits, editing commit messages, reviewing commit subjects, or preparing PR-style history.
- `references/replay-commits.md` when initializing Git history from an already-finished tree or reconstructing a meaningful series of commits.
- `references/history-edits.md` when amending, rebasing, rewriting, cleaning history, or inspecting path-specific logs.

## Safety

- Respect the repository's configured commit signing (`commit.gpgsign`, `gpg.format`, `user.signingkey`). Never bypass it with `--no-gpg-sign` or `-c commit.gpgsign=false` unless the user explicitly asks.
- Prefer non-interactive Git commands.
- Do not use broad staging such as `git add .`, `git add -A`, or repository-wide pathspecs for normal commits. Stage explicit files or explicit narrow directories that match the intended commit scope.
- Do not run destructive history or working-tree commands unless the user clearly asked for that operation.
- Do not stage generated build output, local secrets, or transient tool output. Before staging in a newly initialized repo, verify `.gitignore` excludes them.

## Quick Rules

- Use Conventional Commit subjects for commits.
- Add commit bodies only when they explain reason, impact, migration, security, deployment, lifecycle, or non-obvious tradeoffs.
