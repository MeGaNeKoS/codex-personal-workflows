---
name: codex-workflow
description: "Use for Codex operating workflows: goal mode, subagents, delegated implementation or review, context hygiene, UAC, and skill forward testing. Generic guidance, not project implementation."
---

# Codex Workflow

Use this skill as the single global entry point for Codex operating workflow guidance.

Keep this file small. Load only the reference that matches the task.

## Base Rules

- Treat goal mode as an explicit operating contract with the user.
- Treat subagents as delegated workers or evaluators for both normal turns and goal mode, not final decision makers.
- Preserve the main thread for judgment work.
- Follow the active AGENTS and developer delegation policy as the source of truth for when delegation is required, optional, or skipped.
- Propose stronger workflow modes when they would materially improve the work.
- Do not start goal mode unless the user explicitly asks for it or approves the proposal.
- Main-thread Codex owns user intent, product understanding, architecture and UX judgment, acceptance criteria, integration, verification, and final status.
- Read source artifacts before making project-specific claims.

## Reference Routing

Read only the branch that applies:

- Goal mode, long-running objectives, durable status, user acceptance criteria, completion/blocker rules: `references/goal-mode.md`
- Subagents, parallel agent work, model and reasoning selection, bounded implementation/review/research prompts, skill forward-testing: `references/subagents.md`
- Context hygiene, context compaction timing, drift recovery, and preservation instructions: `references/context-hygiene.md`

If a task may need context management, read `references/context-hygiene.md`. This includes long-running work, resumed work, goal mode, topic shifts, subagent-heavy work, or any task where stale context may reduce accuracy.

If a task involves multiple workflow areas, read each matching reference. Use goal-mode guidance for overall ownership, subagent guidance only for delegation, and context-hygiene guidance only for compaction decisions.
