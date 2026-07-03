# Git Safety

- Follow the global Git signing rules.
- Prefer non-interactive commands over interactive terminals.
- Prefer explicit staging. Do not use `git add .`, `git add -A`, or broad repository-wide staging for normal commits; use specific files or narrow pathspecs tied to the intended commit.
- Do not run destructive history or working-tree commands unless the user clearly asked for that operation.
- Before staging in a newly initialized repo, verify `.gitignore` excludes generated build output, local secret files, and transient tool output.
