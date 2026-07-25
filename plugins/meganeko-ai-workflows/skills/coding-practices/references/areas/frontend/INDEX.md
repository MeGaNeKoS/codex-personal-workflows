# Frontend Area

Use this branch for frontend product design, UI implementation, responsive behavior, accessibility, keyboard navigation, component states, information hierarchy, forms, tables, data visualization, and browser verification.

## Workflow

- Understand the user workflow before designing components.
- Identify the primary action, secondary actions, required context, and failure states.
- Design desktop, tablet, and mobile behavior explicitly.
- Include loading, empty, error, permission-denied, disabled, stale, and validation states where relevant.
- Implement semantic HTML first; use ARIA only to fill semantic gaps.
- Verify meaningful UI changes in a browser with realistic viewport sizes.

## Architecture Routing

Select every row matched by the change. An explicit **Also load** instruction in a focused reference adds another reference; ordinary links do not.

| Changed concern | Load |
| --- | --- |
| Owner selection, application/feature/domain/design-system boundaries, dependency direction, or import enforcement | [Ownership And Dependencies](./ownership-and-dependencies.md) |
| Trust or representation boundary, external client, port, SDK, persistence/browser API, adapter, or external-operation lifecycle | [Boundaries And Integrations](./boundaries-and-integrations.md) |
| Mutable state, transition, concurrency, synchronization, lifecycle, or source of truth | [State Ownership](./state-ownership.md) |
| Product component placement, neutral primitive ownership, or promotion to another frontend owner | [Components And Promotion](./components-and-promotion.md) |
| Paths, filenames, directories, or splitting one owner across files/components | [File Organization](./file-organization.md) |
| Test responsibility or verification seam | [Testing](./testing.md) |

For TypeScript projects, also load the focused [TypeScript references](../../languages/typescript/INDEX.md) matching the type concerns. Load the active framework reference for framework file and lifecycle mapping.

## Frontend Architecture Gates

- Every changed responsibility must have one named owner and an allowed dependency direction before editing.
- Framework and route files adapt framework inputs; product workflow and domain policy remain with their owners.
- Product behavior starts feature-local. Neutral ownership requires explicit evidence, not a generic visual name.
- State transitions and boundary conversions each have one responsible owner.
- Destructive, irreversible, security-sensitive, or audit-sensitive commands require a deliberate confirmation or prevention safeguard selected and verified through the active frontend-design workflow.
- Tests and import checks must expose the same ownership graph as production code.

These are routing gates. Focused references own the detailed architecture policy and must not be copied back into this index.

## Styling System Selection

- Follow the project’s existing styling system when it is coherent and actively used.
- Treat changing styling systems as an architecture change; do it only when the user asks or explicitly approves it.
- If the existing styling approach creates clear maintainability limits, stop and propose options before changing direction. Do not migrate or introduce a new styling system without user approval.
- Examples of maintainability limits include duplicated style logic across many components, uncontrolled global CSS, repeated one-off values, brittle selector chains, conflicting styling systems, or markup/style separation that makes routine UI changes risky.
- For new frontends or projects without a clear standard, prefer Tailwind. It reduces custom class naming, keeps styling close to UI structure, and works well with centralized design tokens for spacing, color, typography, layout, and responsive rules.
- Keep global CSS narrow: reset/base rules, typography defaults, tokens, and truly app-wide primitives.
- Use scoped CSS, CSS Modules, SCSS, Sass, or the project’s existing stylesheet approach when it better fits complex selectors, pseudo-elements, keyframes, browser-specific selectors, third-party overrides, or styles that would be unclear as utilities.
- Avoid casual mixing. Mix styling systems only across clear boundaries, such as Tailwind for product UI, global CSS for base/tokens, and scoped/module CSS for component-specific behavior or third-party overrides.
- When utility lists become repeated or noisy, extract shared UI components, class constants, style helpers, or design-system primitives.
- Reserve inline styles for runtime dynamic values, not normal design decisions.

## Layout And Visual Quality

- Define stable dimensions for repeated UI elements such as tables, toolbars, boards, tiles, panels, and counters.
- Use responsive constraints such as grid tracks, `minmax`, spacing clamps, aspect ratios, and container-aware layout rules.
- Do not scale font size directly with viewport width.
- Ensure text fits its container at mobile and desktop widths.
- Prevent overlap between navigation, headers, toolbars, content, and overlays.
- Prefer scrolling regions only when the scroll boundary is obvious and keyboard reachable.
- For narrow screens, convert dense horizontal layouts into stacked sections, priority columns, drawers, or summary/detail flows.
- Match visual density to the task and audience.
- Use restrained color and reserve strong color for status, risk, and action.
- Keep typography hierarchy clear without oversized headings inside compact UI.
- Avoid nested cards, decorative background effects, and marketing-style composition inside task surfaces.
- Use icons for recognizable actions and labels when ambiguity would slow users down.

## Accessibility

- Use semantic landmarks for app shell, navigation, main content, forms, tables, dialogs, and complementary panels.
- Give icon-only controls accessible names and visible focus states.
- Ensure all primary workflows can be completed with a keyboard.
- Manage focus for dialogs, drawers, menus, popovers, and route changes.
- Do not communicate state by color alone.
- Tie form labels, descriptions, validation messages, and errors to their fields.
- Keep touch targets large enough for mobile interaction.
- Prefer native controls when they satisfy the interaction.

For UI states, forms, tables, data-view behavior, mutation feedback, or empty/loading/error states, read `ui-patterns.md`.
For accessibility-sensitive UI changes, including controls, color semantics, focus, forms, navigation, dialogs, tables, or app shell structure, read `accessibility-diagnostics.md` before finishing.
For reported UI/layout problems, overlap, overflow, alignment, sizing, or visual weirdness, read `layout-diagnostics.md` before editing.
For animation, transition, movement, collapse/expand, drawer, sidebar, or jank issues, read `transition-diagnostics.md` before editing.

Combine this branch with the relevant language and framework branches.
