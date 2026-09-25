# Frontend Area

Use this branch for frontend product design, UI implementation, responsive behavior, accessibility, keyboard navigation, component states, information hierarchy, forms, tables, data visualization, and browser verification.

## Routing

| Changed concern | Load |
| --- | --- |
| Owner selection, allowed dependency direction | [Ownership And Dependencies](./ownership-and-dependencies.md) |
| Import checks, exceptions, forbidden edges | [Dependency Enforcement](./dependency-enforcement.md) |
| Trust or representation crossing, decoding external data | [Boundaries](./boundaries.md) |
| Client, port, adapter, retry/cache/cancel ownership | [Integrations](./integrations.md) |
| Mutable state, transitions, source of truth, lifecycle | [State Ownership](./state-ownership.md) |
| Feature UI versus neutral primitive placement | [Component Ownership](./component-ownership.md) |
| Moving a responsibility to a broader owner | [Promotion](./promotion.md) |
| Filenames, directories, path qualifiers | [Naming And File Structure](./naming-and-file-structure.md) |
| Splitting one owner across modules | [Responsibility Splits](./responsibility-splits.md) |
| Interaction states, forms, mutations, tables, data views | [UI Patterns](./ui-patterns.md) |
| Styling mechanism, global versus scoped, utility extraction | [Styling](./styling.md) |
| Layout structure, sizing, density, responsive composition | [Visual Quality](./visual-quality.md) |
| Controls, color semantics, focus, dialogs, app shell | [Accessibility](./accessibility-diagnostics.md) |
| Test responsibility or verification seam | [Testing](./testing.md) |

Reported problems, before editing:

| Symptom | Load |
| --- | --- |
| Overlap, overflow, alignment, sizing, visual weirdness | [Layout Diagnostics](./layout-diagnostics.md) |
| Animation, transition, collapse/expand, drawer, jank | [Transition Diagnostics](./transition-diagnostics.md) |

## Gates

- Every changed responsibility has one named owner and an allowed dependency direction before editing.
- Framework and route files adapt framework inputs; product workflow and domain policy stay with their owners.
- Product behavior starts feature-local. Neutral ownership requires promotion evidence, not a generic name.
- State transitions and boundary conversions each have one responsible owner.
- Destructive, irreversible, security-sensitive, or audit-sensitive commands require a deliberate safeguard, verified through the frontend-delivery workflow.
- Tests and import checks expose the same ownership graph as production code.

## Composition

Load only the rows matched by the change, plus any explicit **Also load**. Activating this branch does not load all of it.

For TypeScript, also load the matching [TypeScript references](../../languages/typescript/INDEX.md). Load the active framework reference for file and lifecycle mapping.

This area owns code ownership and structure. Product framing, interaction design, visual identity, and runtime visual verification belong to the frontend-delivery workflow.
