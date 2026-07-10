# Subagents

Subagents extend execution capacity in normal turns and in goal mode. They do not replace main-thread understanding or final judgment.

Subagents are not goal-mode-only. Use them for ordinary coding, inspection, verification, and review work when the task is bounded and delegation would improve throughput or context hygiene.

When the user has explicitly requested subagent-based workflow, delegate bounded procedural execution by default. The main thread must state a concrete reason before doing that work directly.

Classify the next unit of work before running tools or editing files:

- Main-thread work: intent, scope, prioritization, architecture, product direction, risk decisions, ambiguous requirements, integration, and final acceptance.
- Subagent work: procedural execution, tool-specific operation, repetitive mechanical changes, bounded source inspection, bounded verification, cleanup after a chosen direction, implementation of a clearly defined slice, and review of a bounded diff or artifact.

If the next unit is subagent work, spawn a subagent unless a skip condition applies.

Main-thread judgment does not absorb adjacent procedural work. If the main thread decides the rule, pattern, product behavior, or architecture, subagents can still gather matches, inspect broad file sets, run checks, perform mechanical edits, or verify the result.

Skip conditions:

- The work is a single quick read needed for a main-thread decision.
- The subagent tool is unavailable.
- The task requires main-thread judgment.
- Delegation would require secrets, destructive operations, production exposure, or external side effects without explicit approval.
- The user explicitly asks the main thread to do it directly.

If skipping delegation for subagent work, state: `Skipping subagent because: <specific reason>.`

Codex subagents are explicit delegation. Follow the active AGENTS and developer delegation policy as the source of truth for whether bounded work should be delegated by default or requires user approval.

## Lifecycle

Close completed agents after their result has been integrated, rejected, or is no longer needed.

Before skipping delegation due to a full subagent pool or thread limit, first close every completed, failed, rejected, superseded, or no-longer-needed subagent whose result has been captured or intentionally discarded, then retry delegation.

If delegation is still impossible after cleanup, the skip reason must say that cleanup was attempted and whether any agents were closed.

## Model Selection

Prefer `gpt-5.3-codex-spark` for ordinary subagents when available because it uses a separate quota pool and fits bounded delegated work.

Use this preference for source inspection, search, simple audits, command execution, formatting checks, documentation consistency checks, mechanical edits, small implementation slices, focused verification, and bounded review.

If `gpt-5.3-codex-spark` is unavailable, quota-limited, rate-limited, unsupported, or launch fails because of the model override, retry with `gpt-5.4-mini`.

If `gpt-5.4-mini` is unavailable, quota-limited, rate-limited, unsupported, or its explicit override fails, retry with `gpt-5.6-luna`.

If `gpt-5.6-luna` is unavailable, quota-limited, rate-limited, unsupported, or its explicit override fails, retry with default subagent/model settings and no model override only when delegation is still useful. Note the fallback in main-thread status or the final response.

Do not use a stronger explicit model for routine subagent work before exhausting this fallback order. Escalate beyond it only when the delegated task requires substantial reasoning, the low-cost model produced incomplete or unreliable work, or user/system/developer instructions require it.

## Reasoning Selection

Use low reasoning for ordinary subagent work by default.

Use low reasoning for source search, bounded inspection, simple audits, command execution, formatting checks, documentation consistency checks, mechanical edits, small implementation slices, focused verification, and bounded review.

Escalate to medium reasoning when the task requires non-trivial implementation judgment, multi-file causal debugging, careful review, or when a low-reasoning pass is incomplete, inconsistent, or unreliable.

If low reasoning appears to reduce quality, mention that in main-thread status or the final response so this preference can be revisited.

If the reasoning override is unsupported or launch fails because of the effort override, retry with default reasoning settings and note the fallback.

## When To Propose Subagents

Propose subagents when a task is bounded, independently verifiable, and benefits from isolated context. This applies to regular turns as well as long-running goal-mode work.

Good uses:

- inspect a bounded code area
- trace how a feature is implemented
- summarize API or UI coverage from source files
- implement one clearly scoped module, page, route, or test
- reproduce a specific bug from logs or steps
- review a diff
- run bounded verification or tool-specific checks
- perform mechanical cleanup after the main thread chooses the direction
- compare docs against implementation
- forward-test a skill on a realistic task

Avoid subagents for:

- product vision
- ambiguous UX or architecture direction
- final acceptance decisions
- broad tasks without clear boundaries
- work requiring hidden context only the main thread has

## Approval Rule

Codex may propose subagents for bounded code inspection, implementation, review, or research.

When active AGENTS or developer instructions require delegation by default, delegate bounded work without asking for extra approval unless a skip condition or higher risk approval case applies. Otherwise, ask for explicit user approval before spawning subagents unless the user has already explicitly asked for subagents or parallel agent work.

Always ask for explicit user approval before delegating work that may:

- touch production systems
- use secrets or credentials
- perform destructive operations
- alter deployment exposure
- run expensive or long-running jobs
- make irreversible external changes

## Main Thread First

The main thread should gather the initial user intent, constraints, source artifacts, and decision context before delegating.

Delegate narrow tasks with enough context to execute. Do not send a subagent on broad discovery unless the expected result is concrete source-derived knowledge.

## Responsibility Split

Main thread owns:

- product understanding
- architecture and UX judgment
- tradeoff decisions
- task decomposition
- final integration
- final verification
- user-facing status

Subagents may own:

- bounded file inspection
- source-derived summaries
- localized implementation
- targeted debugging
- test additions
- independent review findings

## Prompt Requirements

A subagent prompt should include:

- exact task
- relevant paths, artifacts, logs, or diffs
- whether edits are allowed
- boundaries and files not to touch
- structural expectations for implementation work, including responsibilities that should stay separate
- expected output format
- verification command, when applicable

Prefer raw artifacts over conclusions. Do not leak the expected answer unless the subagent is implementing an already-decided change.

For implementation tasks, describe the result's expected project fit, not only the files the subagent may edit. Ask the subagent to report changed paths and each changed file's role when structure matters.

## Prompt Examples

Inspection:

```text
Inspect the API contract and frontend pages. List backend operations that have no visible UI coverage. Do not edit files. Return findings grouped by workflow.
```

Implementation:

```text
Implement only the Runtime Clients loading, empty, and error states using existing project patterns. Do not modify auth, deployment, or navigation. Run the frontend check and report results.
```

Review:

```text
Review this diff as a senior engineer. Findings first, ordered by severity. Focus on bugs, missing verification, maintainability risks, and user-facing regressions.
```

Skill forward-test:

```text
Use the skill at <path> to perform this task: <task>. Treat it like a normal user request. Do not review the skill directly. Produce the resulting artifact and note any ambiguity.
```

## Integration Rule

After a subagent returns:

1. Read the result critically.
2. Verify important claims against source.
3. Review structure, constraints, and project fit for delegated implementation.
4. Decide what to accept, reject, or revise.
5. Integrate changes in the main thread or assign another bounded task.
6. Run final verification in the main thread.
7. Close the subagent when no further input is needed.

Do not present subagent output as final truth without validation. Passing checks confirms executability; it does not confirm maintainability.

## Parallelism

Use parallel subagents only when tasks are independent.

Good parallel split:

- one agent audits API coverage
- one agent audits UI coverage
- one agent reviews docs

Bad parallel split:

- multiple agents editing the same files
- agents making conflicting architecture decisions
- agents changing deployment configuration independently
