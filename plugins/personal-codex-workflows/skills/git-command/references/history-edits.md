# History Edits

Use this when amending, rebasing, rewriting, cleaning history, or inspecting path-specific logs.

- Preserve author dates and committer dates when practical.
- Prefer non-interactive commands or scripted filters over interactive terminals.
- Verify with `git log --oneline` and any relevant path-specific logs.
- Remove temporary rewrite backup refs only when the user explicitly wants sensitive or unwanted files gone from history.
- Do not rewrite shared or remote history unless the user clearly asks for that operation and accepts the coordination risk.
