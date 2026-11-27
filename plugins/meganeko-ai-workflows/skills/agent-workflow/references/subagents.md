# Subagents

Subagents extend execution capacity in normal turns and in goal mode. They do not replace main-thread understanding or final judgment.

Subagents are not goal-mode-only. Use them for ordinary coding, inspection, verification, and review work when the task is bounded and delegation would improve throughput or context hygiene.

When the user has explicitly requested subagent-based workflow, delegate bounded procedural execution by default. The main thread must state a concrete reason before doing that work directly.

Classify the next unit of work before running tools or editing files:

- Main-thread work: understanding intent, taking scope, architecture, product, and risk decisions to the user, resolving ambiguous requirements, decomposition, accepting results, and the final acceptance check.
- Subagent work: procedural execution, tool-specific operation, repetitive mechanical changes, bounded source inspection, bounded verification, cleanup after a chosen direction, implementation of a clearly defined slice, and review of a bounded diff or artifact.

Give each subagent a scope it owns: a set of files for one that edits, a subject for one that only reads. Send the next task in that scope to the same subagent, whatever the task's topic: its knowledge of the scope is the reason to reuse it. Start a new subagent only when no existing one owns the scope. Two subagents never edit the same files, and a task that spans two owners is two briefs.

If the next unit is subagent work, spawn a subagent unless a skip condition applies.

Main-thread judgment does not absorb adjacent procedural work. Once the rule, pattern, product behavior, or architecture is decided, subagents can still gather matches, inspect broad file sets, run checks, perform mechanical edits, or verify the result.

Skip conditions:

- The work is a single quick read needed for a main-thread decision.
- The subagent tool is unavailable.
- The task requires main-thread judgment.
- Delegation would require secrets, destructive operations, production exposure, or external side effects without explicit approval.
- The user explicitly asks the main thread to do it directly.

If skipping delegation for subagent work, state: `Skipping subagent because: <specific reason>.`

Subagents are explicit delegation. Follow the active project and runtime policy as the source of truth for whether bounded work should be delegated by default or requires user approval.

## Lifecycle

A completed subagent turn does not close the subagent. After reviewing its result, send it the next task in its scope, or send back a defect in what it built, or close it when its scope has no work left or another owner has taken the scope over.

Reuse the same subagent for clarification, correction, deeper inspection, implementation revision, or verification inside its scope, and send a defect back to the subagent that built it. Do not hand a subagent files or a subject outside its scope merely because it remains available.

Runtimes differ in whether a subagent is a persistent addressable worker or a one-shot call that can be continued by message. Where subagents persist and occupy a limited pool, release agents that failed, were rejected or superseded, or have no work left in their scope, before concluding that delegation is impossible; preserve agents whose retained context is still useful unless higher-priority work needs the slot. Where subagents do not persist, this section is a no-op and reuse means addressing the same agent again rather than keeping a slot open.

If delegation remains impossible after cleanup, the skip reason must say so explicitly.

## Cost Selection

Delegated work should run at the cheapest setting that still produces a reliable result. Runtimes expose this differently: model tier, reasoning effort, both, or neither. Apply the principle through whatever levers exist and do not assume a lever is available.

Where a role or agent definition pins its own setting, that pin is the decision: pass no override. Otherwise run at the low setting by default for source search, bounded inspection, simple audits, command execution, formatting and documentation consistency checks, mechanical edits, small implementation slices, focused verification, and bounded review.

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
- raising architecture, UX, scope, and tradeoff decisions with the user
- task decomposition
- accepting each result, and confirming that the results together meet the request
- final verification
- user-facing status

Subagents may own:

- bounded file inspection
- source-derived summaries
- localized implementation
- targeted debugging
- test additions
- independent review findings

## The Brief

A subagent has none of your conversation. Beyond its role definition and the project's instructions, it knows what the brief says. Write the brief in three parts, in this order:

1. **Must.** Numbered requirements and nothing else, each one checkable by running a command or opening a named artifact: what has to be true when the work is done, and the verification to run.
2. **Context.** Everything the subagent needs in order to meet the Musts:
   - its own name and scope, who else is working, and what each of them owns;
   - what it must not touch, and whether edits are allowed at all;
   - the paths, logs, or diffs to work from;
   - for implementation, the change map the `coding-practices` skill defines;
   - the skills or guidance references to load, named explicitly, such as `coding-practices` for any code change. Delegated workers only reliably load a skill when the brief names it.
3. **Report.** What to send back: an answer to each Must by its number, the evidence for it, what could not be checked, and what was ambiguous.

Prefer raw artifacts over conclusions. Do not leak the expected answer unless the subagent is implementing an already-decided change: a subagent handed your hypothesis tends to return it confirmed.

One brief carries one chunk of work. Change a brief by sending it again whole, never by a message that amends the earlier one. Put a long brief, and any brief to a subagent that has already worked several tasks, in a file the subagent re-reads: a context summary keeps the gist of a brief and drops its numbered requirements.

## Brief Examples

Implementation, in full:

```text
## Must
1. The Runtime Clients page shows a loading, an empty, and an error state, built from the state components the project already has.
2. `npm run check` passes.

## Context
You are `clients-ui`, the implementer that owns `src/clients/`. Nobody else is editing the frontend. Do not touch `src/auth/`, `src/navigation/`, or `deploy/`.
Change map: the clients feature owns the three states; it may import the shared state components and nothing from another feature; no boundary changes; the states are tested through the page component; it reuses LoadingState.tsx, EmptyState.tsx, and ErrorState.tsx in src/shared/states/.
Load the `coding-practices` skill before any change.

## Report
Answer each Must by its number, with the output of the check. List each file you changed and its role.
```

What the other kinds of work ask for:

```text
Inspection: List the backend operations in the API contract that have no visible UI coverage in the frontend pages, grouped by workflow. Read only.

Review: Review this diff under two lenses: correctness, and readability and structure. Findings first, ordered by severity, each with file and line. The change map the code was built to is attached.

Skill forward-test: Use the skill at <path> to perform this task: <task>. Treat it like a normal user request. Do not review the skill directly. Produce the resulting artifact and note any ambiguity.
```

## Integration Rule

After a subagent returns:

1. Read the result critically.
2. Verify at least one requirement yourself, by running the command or opening the artifact the report names, and every claim the next step depends on.
3. Review structure, constraints, and project fit for delegated implementation.
4. Send a defect back to the subagent that built it, with the evidence.
5. Send the next task in the same scope to the same subagent.
6. Start a new subagent only when no existing one owns the next task's scope.
7. Confirm that the accepted work, taken together, satisfies the user's request, using delegated verification where appropriate. A fix found at this step goes back to the owner of the files.
8. Close the subagent when its scope has no work left.

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
