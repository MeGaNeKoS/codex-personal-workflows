# Context Hygiene

Use `compact_context` at clear context-management boundaries. Do not request compaction after every small task, merely because the conversation is long, or repeatedly without meaningful new work.

Compaction reduces stale or noisy context while preserving the information needed to continue correctly. It is not a substitute for asking clarification when the user intent is unclear.

## When To Compact

Request compaction when one or more apply:

- The user says the agent is drifting, confused, mixing old and new requirements, or carrying stale assumptions.
- The user corrects the same kind of misunderstanding more than once.
- A major task phase is complete and the next phase has a different objective.
- The conversation has moved several turns into a different self-contained subject.
- The thread contains long build logs, repeated failed attempts, stale debugging paths, or decisions that were later reversed.
- The user starts a different repo, feature, debugging target, deliverable, plugin, tool, or workflow in the same thread.
- A goal continues across multiple turns and only the current state, next step, and durable constraints need to remain.
- Before a large implementation, review, or planning pass after many tool calls, multiple failed attempts, large logs, or several changed decisions.

## Topic Shift Checkpoints

After several turns on a changed subject, especially around 3-5 turns, check whether the previous subject can be reduced to a concise handoff.

Use this test:

> Can the next few turns be handled from a concise handoff plus durable files, links, commits, notes, or summarized decisions?

If yes, request compaction with explicit preservation guidance. If no, continue until the needed context is captured or the dependency on raw history is resolved.

Examples of good topic-shift checkpoints:

- Moving from implementation, build, or debugging work to issue drafting, social writing, workflow design, or policy discussion.
- Moving from writing or planning back to code, tests, security review, or runtime setup.
- Starting a different repo, feature, deliverable, plugin, tool, or operational workflow.
- Recovering after the user says the agent is drifting, mixing topics, carrying stale assumptions, or losing track of the current subject.

Do not compact only because the topic changed for one message. A short detour is not automatically a context boundary.

## Goal Mode Compaction

Goal-mode completion, blocker, and acceptance decisions remain governed by goal-mode guidance; this section only identifies compaction checkpoints.

For long-running goal mode, treat completed work slices as strong compaction checkpoints when the next slice can be pursued from durable state.

A completed work slice is a good compaction boundary when:

- The implementation or research result is committed, recorded in a durable tracker, or otherwise captured in a stable artifact.
- Verification evidence has been recorded or explicitly summarized.
- Subagent outputs have been integrated, rejected, or captured.
- The next step no longer needs raw logs, failed attempts, or detailed intermediate reasoning from the previous slice.

Durable trackers, git history, committed code, issue notes, or recorded evidence make compaction safer by providing source material for the handoff, but they do not by themselves require compaction.

Do not compact solely because a small command ran or a trivial edit was made. A committed and verified goal slice is a normal context boundary for sustained goal work.

## When Not To Compact

Do not request compaction when:

- The last user message asks a direct question about earlier discussion, prior alternatives, previous commands, or why a decision was made.
- The current task requires comparing multiple options already discussed in the thread.
- There are unresolved user instructions from earlier turns that have not been restated or summarized.
- A command, build, test, server, subagent, deployment, or long-running process is active and its relevant details have not been captured.
- The next task depends on exact wording, draft text, command output, or reasoning that exists only in raw conversation history.
- You cannot write a specific preservation instruction.
- There has been no meaningful new work or topic movement since the last compaction.

## Preservation Guidance

When requesting compaction, include explicit preservation guidance.

Preserve:

- Current user goal and immediate next question.
- Durable user preferences and active instructions.
- Active repo, path, branch, runtime, deployment, or environment constraints.
- Important decisions already made.
- Older subjects likely to be resumed, summarized at headline level.
- Files changed, commands still running, and artifacts created.
- Known blockers, risks, and verification status.
- Latest committed slice name and commit hash, when relevant.
- Important rejected approaches that still affect future work.
- Any sentinel, exact phrase, draft text, command, path, config value, link, or user preference that must survive.

Drop:

- Raw build logs already summarized as pass/fail.
- Stale failed attempts.
- Resolved debugging details.
- Repeated command output.
- Previous implementation details already captured in commits, tests, trackers, or issue notes.
- Abandoned approaches that are no longer relevant.
- Unrelated side discussions.

Prefer concise instructions. If older context may still matter, preserve it at headline level instead of keeping raw history.

## Clarification vs Compaction

If intent is unclear, ask. If intent is clear but stale context is likely to cause errors, compact.

Ask for clarification when the next action is ambiguous, risky, or depends on a user preference that cannot be inferred safely.

Do not use clarification as a replacement for compaction, and do not use compaction to avoid asking a necessary question.

## Fallback

If `compact_context` is not available, continue normally and avoid pretending compaction happened.
