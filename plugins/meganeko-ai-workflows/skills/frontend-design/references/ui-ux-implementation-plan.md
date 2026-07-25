# UI/UX Implementation Plan

Use this procedure for substantial frontend implementation. Create `docs/<feature>-ui-ux-plan.md` as a build-ready behavior specification, not an engineering schedule. If the user requested only a plan, stop after producing it. The implementer must not need to invent UX behavior.

Label factual claims **Observed**, **Inferred**, **Proposed**, or **Unknown**, and attach traceable evidence. Resolve factual conflicts or record concrete blockers and evidence needed.

## Product and Scope

- Users, repeated tasks, information and decisions:
- Primary and supporting workflows:
- Target routes and entry/handoff points:
- In scope, out of scope, success criteria:
- Local framework, tokens, primitives, conventions, and source evidence:
- Revision, environment, flags, permissions, fixtures, and research conditions:

## Page Composition and Scroll Ownership

Describe the page regions in reading order, including purpose, content priority, exact or bounded dimensions, alignment, density, fixed/sticky/static behavior, overflow, and the region that owns each scroll axis. Specify first-viewport composition and what remains visible below it.

| Region | Composition/content | Dimensions and layout | Scroll owner | Wide-to-narrow transformation |
| --- | --- | --- | --- | --- |
| | | | | |

## Responsive Transformation

Specify behavior by available width and content pressure, not device labels alone. Cover navigation, ordering, collapse, wrapping, clipping, overflow, tables/charts/toolbars, pointer/touch differences, virtual keyboard, safe areas, zoom/reflow, and longest labels/values.

| Condition | Composition | Navigation/controls | Overlays | Scroll behavior |
| --- | --- | --- | --- | --- |
| | | | | |

## Component Specifications

Every component needs anatomy, content, data source/shape, dimensions, layout constraints, and all applicable states.

| Component | Anatomy and content | Data/API boundary | Dimensions/layout | States and transitions | Evidence |
| --- | --- | --- | --- | --- | --- |
| | | | | | |

Include default, hover, focus-visible, active/selected, disabled, busy, validation, destructive, loading, empty, partial, stale, error, offline, permission, and no-result states where applicable. Define stable dimensions and behavior for dynamic content.

## Interactions and Outcomes

For every actionable control and gesture, specify pointer/touch, keyboard, focus entry, visible feedback, state transition, outcome, cancellation, retry, confirmation, undo, debounce/throttle, and failure behavior. Define escape, outside click, close buttons, disabled/busy behavior, and focus return.

| Trigger/control | Pointer/touch | Keyboard | State transition and outcome | Cancel/retry/dismiss/focus |
| --- | --- | --- | --- | --- |
| | | | | |

## Cross-Component Sequences

Document sequences across components: navigation and history/deep links, routing handoffs, overlays and nesting, optimistic versus confirmed updates, persistence scope (transient, URL, session, local, server), refresh/stale handling, unsaved changes, and focus movement or restoration.

## System States and Recovery

| State/trigger | Composition and message | Action/recovery | Existing content | Announcement/semantics |
| --- | --- | --- | --- | --- |
| Initial/incremental loading | | | | |
| Empty/no results | | | | |
| Partial/stale | | | | |
| Error/offline | | | | |
| Permission/session | | | | |

Specify skeleton stability, retry/cancel semantics, error association, and whether refresh preserves usable content.

## Accessibility and Motion

Define landmarks, heading structure, roles, names/descriptions, focus order, focus-visible styling, keyboard semantics, live-region announcements, errors, contrast/non-color cues, touch targets, zoom/reflow, and reduced-motion behavior. For each motion, specify purpose, trigger, properties, duration/easing policy, interruption, and the reduced-motion alternative.

## Implementation Mapping

Map each behavior to existing or new routes, components, state/store boundaries, API/provider interfaces, tokens, assets, and tests. Record deliberate deviations from research or local conventions with evidence and rationale. This is an ownership map, not a time estimate or task schedule.

| Behavior/slice | Route/component/state/API/test mapping | Dependencies and ownership | Completion evidence |
| --- | --- | --- | --- |
| | | | |

## Runtime Acceptance and Verification Matrix

Record the exact revision, environment, viewports, inputs, flags, permissions, fixtures, and procedure. Verify runtime behavior whenever accessible.

| Scenario | Viewport/input | Expected factual behavior | Procedure/test | Evidence | Status |
| --- | --- | --- | --- | --- | --- |
| Primary workflow | Desktop pointer | | | | |
| Primary workflow | Desktop keyboard/focus | | | | |
| Primary workflow | Narrow/touch | | | | |
| Loading/empty/error/permission | Representative sizes | | | | |
| Long content and values | Wide and narrow | | | | |
| Dismiss/cancel/retry/focus return | Pointer and keyboard | | | | |
| Reduced motion | Supported runtime | | | | |

Acceptance fails for overlap, clipping, unintended overflow, layout shift, incorrect scroll ownership, broken focus/dismissal/return, acting disabled or busy controls, unusable system states, or motion that ignores reduced-motion settings. Record unresolved failures as **Unknown** only with the concrete blocker and evidence needed.
