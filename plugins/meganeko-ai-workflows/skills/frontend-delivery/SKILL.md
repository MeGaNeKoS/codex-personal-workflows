---
name: frontend-delivery
description: "Research, plan, implement, port, and runtime-verify substantial frontend and UI work with traceable evidence. Use for product and audience framing, interface research, responsive behavior, interaction and system states, accessibility, motion, reference-porting, build-ready implementation plans, or browser/runtime verification of websites, web applications, dashboards, portals, tools, and component systems. This skill owns the delivery workflow and evidence contract, not visual style direction."
---

# Frontend Delivery

Treat frontend design as product behavior expressed through composition, interaction, visual hierarchy, and runtime state. Keep factual conclusions evidence-backed and distinguish them from design recommendations.

## Scope Test

This skill's full workflow applies to **non-trivial** frontend work, using one shared definition: a change that crosses files or owners, introduces a contract or abstraction, or changes state, persistence, transport, framework, or domain behavior.

Below that threshold, skip the plan artifact and the full evidence pass. Still verify the runtime and still label any factual claim you make about existing behavior.

## Phase Contract

Respect the phase requested by the user. If the user asks only for research, a draft, a plan, or a review, produce that artifact and stop before implementation. Do not silently turn research into a build or a plan into code.

## Workflow

1. **Understand the product.** Identify users, repeated tasks, information being scanned or compared, decisions, success criteria, and the target product's local framework, routes, tokens, primitives, state boundaries, and verification tooling.
2. **Inspect the target.** Read the relevant source, tests, configuration, and runtime entry points. Preserve coherent local conventions and product identity.
3. **Research references when useful.** For detailed source-available or visual reference work, load [references/reference-ui-research.md](references/reference-ui-research.md). Trace behavior from source, tests, and runtime; do not infer implementation solely from visuals.
4. **Create the build-ready plan.** For non-trivial implementation, load [references/ui-ux-implementation-plan.md](references/ui-ux-implementation-plan.md) and produce `docs/<feature>-ui-ux-plan.md` before coding. The plan specifies behavior, not an engineering schedule.
5. **Implement approved behavior.** Build in vertical workflow slices, including composition, state, interaction, accessibility, responsive behavior, and verification. Reuse existing primitives and libraries where appropriate.
6. **Verify the runtime.** Exercise representative desktop and mobile viewports plus pointer, keyboard, focus, dismiss, retry, cancel, and other material interactions. Check all material states, overflow, overlap, layout stability, motion, and reduced motion. Record revision, environment, inputs, flags, fixtures, and procedure.

## Evidence Contract

Label every conclusion exactly as **Observed** (directly established by cited source, configuration, test, runtime, or user-provided artifact), **Inferred** (reasoned from cited observations, with the reasoning stated), **Proposed** (a target-design recommendation, not reference behavior), or **Unknown** (not established; record what was inspected, the concrete blocker, and the evidence needed).

Before producing a research record, implementation plan, or verification report, load [references/evidence-contract.md](references/evidence-contract.md). It owns the full contract: evidence hierarchy, per-claim recording requirements, the worked example, and the process guarantee.

Source-available research must trace component composition, handlers, state transitions, API/provider boundaries, routing and handoffs, persistence, loading/empty/partial/stale/error/offline states, retry/cancel, permissions/feature flags, focus and accessibility, motion/reduced motion, and tests. Runtime verification remains required whenever the target is accessible.

## Engineering Handoff

For frontend work that crosses files or owners, introduces a contract or abstraction, or changes state, persistence, transport, framework, or domain behavior, load the base [coding-practices skill](../coding-practices/SKILL.md), its [frontend index](../coding-practices/references/areas/frontend/INDEX.md), the focused references selected by the change, and the active language and framework indexes before editing.

Use `frontend-delivery` for product framing, interaction, responsive behavior, accessibility, motion, copy, and runtime/visual evidence. Use `coding-practices` for code ownership, dependency direction, module structure, type flow, abstractions, and test seams.

If the runtime provides a separate visual-design skill for aesthetic direction, typography, and visual identity, use it for those choices and keep this skill as the owner of workflow phases, the evidence contract, and runtime verification. Aesthetic recommendations from that skill are **Proposed** until verified here.

Record the change map required by the frontend ownership reference. If a design requires an undefined owner or forbidden dependency, mark it **Proposed** and resolve the architecture decision before implementation. Design evidence does not override repository boundaries.

## Design Standards

- Design the usable product first unless a landing page is explicitly requested.
- Choose composition from workflow and information priority. Keep operational interfaces dense, scannable, and predictable.
- Use established local tokens, controls, icon libraries, and accessible primitives. Use tooltips for unfamiliar icon-only controls.
- Define stable dimensions and responsive transformations for grids, boards, media, toolbars, and dynamic content.
- Specify loading, empty, partial, stale, error, offline, permission, no-result, disabled, and busy states with the happy path.
- Verify keyboard access, visible focus, semantics, announcements, touch targets, zoom/reflow, dismissal, focus return, and reduced motion.
