# Reference UI Research

Use this procedure when a source codebase, live product, design file, recording, screenshot, written description, or combination is used as a reference. Create `docs/<feature>-ui-research.md`. This is a research artifact, not an implementation plan. If the user requested only research, stop after producing it.

## Evidence Rules

Label every conclusion **Observed**, **Inferred**, **Proposed**, or **Unknown**:

- **Observed:** directly established by cited source, configuration, test, runtime, or user-provided artifact/input.
- **Inferred:** reasoned from cited observations, with the reasoning stated; not presented as direct fact.
- **Proposed:** a target-design recommendation or choice, not reference behavior.
- **Unknown:** not established; record what was inspected, the concrete blocker, and the evidence needed.

Prefer evidence in this order: source/tests, runtime, design files, recordings/screenshots, descriptions. A visual resemblance does not prove component composition, ownership, semantics, state, or persistence.

For each runtime claim, record revision/build, environment and viewport, inputs, feature flags, permissions, fixtures/data, and exact verification procedure. For each source claim, record repository revision and file/symbol or test. Every factual claim must point to evidence. Subjective transfer recommendations are **Proposed**.

Apply this guarantee: identical accessible source/config/tests/runtime, revision, environment, inputs, flags, fixtures, and procedure must yield identical factual behavior conclusions. Resolve conflicting factual conclusions with source, configuration, tests, or runtime. If resolution is blocked, record the conflict and concrete blocker. An **Unknown** must say what was inspected, why it could not be established, and what evidence would resolve it.

## Research Record

### Evidence Scope and Conditions

- Feature/reference and target:
- Evidence inventory and hierarchy:
- Source revision or URL/build:
- Runtime environment, viewport, inputs, flags, permissions, and fixtures:
- Procedure, tools, and date:
- Access limitations:

### Composition

Describe page regions, component boundaries, hierarchy, dimensions, alignment, density, fixed/sticky behavior, overflow and scroll ownership, and first-viewport composition. Trace composition from source where available. Label each conclusion.

### Controls and Interactions

For every control, record trigger, handler, target state, feedback, keyboard behavior, pointer/touch behavior, dismissal, cancellation, retry, confirmation, navigation, and persistence. Include menus, dialogs, drawers, tooltips, drag/drop, selection, and resize behavior. Trace representative interactions end to end.

### System States

Record initial and incremental loading, skeleton stability, empty, no-results, partial, stale, error, offline/reconnecting, permission denied, session expiry, disabled, busy, validation, and destructive states. Include recovery and whether existing content remains visible.

### Responsive Behavior

Compare behavior under available-space and content-pressure changes. Record reordering, collapse, wrapping, clipping, overflow, scroll ownership, toolbar/table/chart transformations, pointer versus touch differences, keyboard/safe-area behavior, and long/localized content.

### Accessibility and Motion

Trace landmarks, headings, roles, names, descriptions, focus order, visible focus, keyboard semantics, live announcements, error association, contrast/non-color cues, touch targets, zoom/reflow, motion purpose and interruption, and reduced-motion behavior. Use runtime and tests where accessible.

### Source Trace Matrix

| Behavior/conclusion | Evidence class | Source file/symbol, test, runtime step, or artifact | Revision/conditions | Confidence or gap |
| --- | --- | --- | --- | --- |
| | | | | |

Trace component composition, handlers, state transitions, API/provider boundaries, routing/handoffs, persistence, permissions/flags, and tests. Do not replace missing traces with visual guesses.

### Conflicts and Gaps

| Claim or conflict | Inspected evidence | Blocker | Evidence needed | Resolution |
| --- | --- | --- | --- | --- |
| | | | | |

### Transfer Decisions

| Reference behavior/composition | Evidence | Target decision | Preserve/adapt/omit | Rationale |
| --- | --- | --- | --- | --- |
| | | | | |

Keep reference-specific branding, terminology, permissions, routes, data assumptions, and implementation details out of the target unless explicitly required.

### Target Recommendations

Record only recommendations for the target, labeled **Proposed**, with the user/audience need, local convention, or evidence that motivates each decision. Do not present unresolved behavior as a recommendation or fact.

## Completion Gate

Research is complete only when the evidence conditions, trace matrix, interactions, states, responsive behavior, accessibility/motion findings, conflicts/gaps, transfer decisions, and target recommendations are recorded. Mark unresolved items **Unknown** with the required blocker and evidence.
