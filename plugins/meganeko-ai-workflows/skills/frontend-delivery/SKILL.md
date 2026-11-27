---
name: frontend-delivery
description: "Research, plan, implement, port, and runtime-verify substantial frontend and UI work with traceable evidence. Use for product and audience framing, interface research, responsive behavior, interaction and system states, accessibility, motion, reference-porting, build-ready implementation plans, or browser/runtime verification of websites, web applications, dashboards, portals, tools, and component systems. This skill owns the delivery workflow and evidence contract, not visual style direction."
---

# Frontend Delivery

Treat frontend design as product behavior expressed through composition, interaction, visual hierarchy, and runtime state. Keep factual conclusions evidence-backed and distinguish them from design recommendations.

## Scope Test

The full workflow applies to **non-trivial** frontend work. Non-trivial is defined once, by the Scope Test in the [coding-practices skill](../coding-practices/SKILL.md).

Below that threshold, skip the plan artifact and the full evidence pass. Still verify the runtime and still label any factual claim you make about existing behavior.

## Phase Contract

Respect the phase requested. If the request is only for research, a draft, a plan, or a review, produce that artifact and stop before implementation. Do not silently turn research into a build or a plan into code.

## Workflow

1. **Understand the product.** Identify users, repeated tasks, information being scanned or compared, decisions, success criteria, and the target product's local framework, routes, tokens, primitives, state boundaries, and verification tooling.
2. **Inspect the target.** Read the relevant source, tests, configuration, and runtime entry points. Preserve coherent local conventions and product identity.
3. **Research references when the work ports or imitates an existing interface.** Load [references/reference-ui-research.md](references/reference-ui-research.md). Trace behavior from source, tests, and runtime; do not infer implementation solely from visuals.
4. **Plan before building.** A non-trivial implementation is built from a plan that leaves no UX behavior to invent. When you were given that plan, in a brief, a ticket, or a file, build from it and write no second one. When the request asks for a plan, load [references/ui-ux-implementation-plan.md](references/ui-ux-implementation-plan.md) and write it at the path the request names, or `docs/<feature>-ui-ux-plan.md` when it names none. A lead whose instructions keep it from writing files has a specialist write the plan. When there is no plan and nothing asked for one, ask before coding.
5. **Implement approved behavior.** Build in vertical workflow slices, including composition, state, interaction, accessibility, responsive behavior, and verification. Reuse existing primitives and the libraries the project already has.
6. **Verify the runtime.** Run the changed interface in a browser, at the project's own breakpoints, or at 1440 by 900 and 390 by 844 where it defines none. Exercise every interaction the change adds or alters by pointer and by keyboard, and check focus order, dismissal, and focus return. Put every state the change adds or alters on screen, from the list under Design Standards, and check each for overflow, overlap, and layout shift. Check motion with reduced motion off and on. Record revision, environment, viewport, inputs, flags, fixtures, and the steps taken. Where no browser can be run, record each item you could not check as **Unknown**, with the blocker, and say so in your report: an unverified interface is not reported as verified.

## Evidence Contract

Label every conclusion **Observed**, **Inferred**, **Proposed**, or **Unknown**.

Before producing a research record, an implementation plan, or a verification report, load [references/evidence-contract.md](references/evidence-contract.md). It defines the four labels and owns the rest of the contract: the evidence hierarchy, what to record for each claim, a worked example, and the process guarantee.

## Engineering Handoff

For non-trivial frontend work, before editing, load the [coding-practices skill](../coding-practices/SKILL.md) and follow its routing: [Core Practices](../coding-practices/references/core.md), the [frontend index](../coding-practices/references/areas/frontend/INDEX.md) with the references it selects for the change, and the index of the active language and framework.

Use `frontend-delivery` for product framing, interaction, responsive behavior, accessibility, motion, copy, and runtime/visual evidence. Use `coding-practices` for code ownership, dependency direction, module structure, type flow, abstractions, and test seams.

If the runtime provides a separate visual-design skill for aesthetic direction, typography, and visual identity, use it for those choices and keep this skill as the owner of workflow phases, the evidence contract, and runtime verification. Aesthetic recommendations from that skill are **Proposed** until verified here.

The change map for the work comes from whoever decides structure, as Core Practices describes under Who Decides: build inside the map you were given, and ask for one when a non-trivial task came without it. If a design requires an owner or a dependency the map does not allow, mark the design **Proposed** and ask before implementing it. Design evidence does not override repository boundaries.

## Design Standards

- Design the usable product first unless a landing page is explicitly requested.
- Choose composition from workflow and information priority. Keep operational interfaces dense, scannable, and predictable.
- Use established local tokens, controls, icon libraries, and accessible primitives. Use tooltips for unfamiliar icon-only controls.
- Define stable dimensions and responsive transformations for grids, boards, media, toolbars, and dynamic content.
- Specify loading, empty, partial, stale, error, offline, permission, no-result, disabled, and busy states with the happy path.
- Verify keyboard access, visible focus, semantics, announcements, touch targets, zoom/reflow, dismissal, focus return, and reduced motion.
