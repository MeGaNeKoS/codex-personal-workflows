# Context Hygiene

Use `compact_context` at clear context-management boundaries. Do not request compaction after every small task or repeatedly without meaningful new work.

Request compaction when one or more apply:

- The user says the agent is drifting, confused, mixing old and new requirements, or carrying stale assumptions.
- The user corrects the same kind of misunderstanding more than once.
- A major task phase is complete and the next phase has a different objective.
- The thread contains long build logs, repeated failed attempts, stale debugging paths, or decisions that were later reversed.
- The user starts a different repo, feature, debugging target, deliverable, or workflow in the same thread.
- A goal continues across multiple turns and only the current state, next step, and durable constraints need to remain.
- Before a large implementation, review, or planning pass after many tool calls, multiple failed attempts, large logs, or several changed decisions.

Do not request compaction when:

- The last user message asks a direct question about earlier discussion, prior alternatives, previous commands, or why a decision was made.
- The current task requires comparing multiple options already discussed in the thread.
- There are unresolved user instructions from earlier turns that have not been restated or summarized.
- A command, build, test, server, or long-running process is active and its relevant details have not been captured.
- You cannot write a specific preservation instruction.
- There has been no meaningful new work since the last compaction.

When requesting compaction, include explicit preservation guidance:

- Current user goal.
- Durable user preferences and active instructions.
- Active repo, path, and branch.
- Important decisions already made.
- Files changed or commands still running.
- Known blockers, risks, and verification status.
- Any sentinel, exact phrase, command, path, config value, or user preference that must survive.

Prefer concise instructions.

Drop stale logs, abandoned approaches, resolved debugging details, outdated assumptions, reversed decisions, and unrelated side discussions.

If `compact_context` is not available, continue normally and avoid pretending compaction happened.
