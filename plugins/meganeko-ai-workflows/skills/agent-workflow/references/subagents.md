# Subagents

Subagents extend execution capacity in normal turns and in goal mode. They do not replace main-thread understanding or final judgment.

Subagents are not goal-mode-only. Use them for ordinary coding, inspection, verification, and review work when the task is bounded and delegation would improve throughput or context hygiene.

When the user has explicitly requested subagent-based workflow, delegate bounded procedural execution by default. The main thread must state a concrete reason before doing that work directly.

Classify the next unit of work before running tools or editing files:

- Main-thread work: intent, scope, prioritization, architecture, product direction, risk decisions, ambiguous requirements, integration, and final acceptance.
- Subagent work: procedural execution, tool-specific operation, repetitive mechanical changes, bounded source inspection, bounded verification, cleanup after a chosen direction, implementation of a clearly defined slice, and review of a bounded diff or artifact.

Assign each subagent one bounded workstream. Reuse the same subagent for related follow-up work when its retained context is useful. Use a new subagent when the work belongs to a different workstream or requires independent context.

If the next unit is subagent work, spawn a subagent unless a skip condition applies.

Main-thread judgment does not absorb adjacent procedural work. If the main thread decides the rule, pattern, product behavior, or architecture, subagents can still gather matches, inspect broad file sets, run checks, perform mechanical edits, or verify the result.

Skip conditions:

- The work is a single quick read needed for a main-thread decision.
- The subagent tool is unavailable.
- The task requires main-thread judgment.
- Delegation would require secrets, destructive operations, production exposure, or external side effects without explicit approval.
- The user explicitly asks the main thread to do it directly.

If skipping delegation for subagent work, state: `Skipping subagent because: <specific reason>.`

Subagents are explicit delegation. Follow the active project and runtime policy as the source of truth for whether bounded work should be delegated by default or requires user approval.

## Lifecycle

A completed subagent turn does not close the subagent. After reviewing its result, either continue its workstream with related follow-up input or close it when that workstream is complete, rejected, superseded, or no longer needed.

Reuse the same subagent for clarification, correction, deeper inspection, implementation revision, or verification related to its assigned workstream. Do not reuse a subagent for an unrelated workstream merely because it remains available.

Runtimes differ in whether a subagent is a persistent addressable worker or a one-shot call that can be continued by message. Where subagents persist and occupy a limited pool, release agents that failed, were rejected or superseded, or finished a workstream with no follow-up remaining, before concluding that delegation is impossible; preserve agents whose retained context is still useful unless a higher-priority workstream needs the slot. Where subagents do not persist, this section is a no-op and reuse means addressing the same agent again rather than keeping a slot open.

If delegation remains impossible after cleanup, the skip reason must say so explicitly.

## Cost Selection

Delegated work should run at the cheapest setting that still produces a reliable result. Runtimes expose this differently: model tier, reasoning effort, both, or neither. Apply the principle through whatever levers exist and do not assume a lever is available.

Run at the low setting by default for source search, bounded inspection, simple audits, command execution, formatting and documentation consistency checks, mechanical edits, small implementation slices, focused verification, and bounded review.

Escalate when the task requires non-trivial implementation judgment, multi-file causal debugging, or careful review, or when a low pass came back incomplete, inconsistent, or unreliable.

Do not hard-code provider-specific model names or fallback chains into this shared guidance. Follow the active runtime's documented capability and availability signals.

If a cost override is unsupported or the launch fails because of it, retry with runtime defaults and note the fallback. If the low setting appears to be reducing quality, say so in main-thread status so this preference can be revisited.

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

The agent may propose subagents for bounded code inspection, implementation, review, or research.

Delegate bounded work without asking for extra approval unless a skip condition or higher-risk approval case applies.

This skill is the user's delegation policy. Where a runtime ships a blanket default such as "do not delegate unless asked", treat that as the runtime's baseline for users who have expressed no preference, and this skill as the expressed preference that replaces it. Higher-risk approval cases below still require asking, and an explicit in-session instruction from the user still wins over both.

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

- bounded workstream
- exact task
- the skills or guidance references to load, named explicitly. Delegated workers only reliably load a skill when the prompt names it.
- relevant paths, artifacts, logs, or diffs
- whether edits are allowed
- boundaries and files not to touch
- structural expectations for implementation work, including responsibilities that should stay separate
- expected output format
- expected completion evidence or verification

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
4. Decide whether the next task belongs to the same workstream.
5. Continue with the same subagent when related follow-up benefits from its retained context.
6. Use a new subagent when the next task belongs to a different workstream or requires independent context.
7. Integrate accepted work and confirm that the combined result satisfies the user's request, using delegated verification where appropriate.
8. Close the subagent when its workstream no longer needs follow-up.

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
