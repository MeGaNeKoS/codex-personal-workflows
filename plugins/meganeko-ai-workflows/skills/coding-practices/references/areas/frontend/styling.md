# Frontend Styling System

Use this reference when a change introduces styles, adds a styling mechanism, or raises whether the current styling approach still fits. It owns styling-system selection and mixing rules. Layout sizing and responsive composition belong to [Visual Quality](./visual-quality.md); reported visual defects belong to [Layout Diagnostics](./layout-diagnostics.md).

## Selection

- Follow the project's existing styling system when it is coherent and actively used.
- Treat changing styling systems as an architecture change; do it only when the user asks or explicitly approves it.
- If the existing styling approach creates clear maintainability limits, stop and propose options before changing direction. Do not migrate or introduce a new styling system without user approval.
- Examples of maintainability limits include duplicated style logic across many components, uncontrolled global CSS, repeated one-off values, brittle selector chains, conflicting styling systems, or markup/style separation that makes routine UI changes risky.

## Default For New Projects

For new frontends, or projects without a clear standard, prefer Tailwind. It reduces custom class naming, keeps styling close to UI structure, and works well with centralized design tokens for spacing, color, typography, layout, and responsive rules.

This is an author default, not an architecture rule. An existing coherent styling system always outranks it, and a project that has deliberately chosen otherwise needs no justification to keep that choice.

## Boundaries

- Keep global CSS narrow: reset/base rules, typography defaults, tokens, and truly app-wide primitives.
- Use scoped CSS, CSS Modules, SCSS, Sass, or the project's existing stylesheet approach when it better fits complex selectors, pseudo-elements, keyframes, browser-specific selectors, third-party overrides, or styles that would be unclear as utilities.
- Avoid casual mixing. Mix styling systems only across clear boundaries.
- Reserve inline styles for runtime dynamic values, not normal design decisions.

A deliberate mix looks like this:

```text
Tailwind          -> product UI composition
global CSS        -> reset, typography defaults, design tokens
scoped/module CSS -> component-specific behavior, keyframes, third-party overrides
inline style      -> runtime values only (computed position, measured size)
```

Casual mixing is the same file reaching for two systems to express one decision.

## Extraction

When utility lists become repeated or noisy, extract shared UI components, class constants, style helpers, or design-system primitives.

Extraction is a promotion decision. A repeated utility string is not by itself promotion evidence; a repeated *product-neutral* visual contract with a second consumer is. See [Ownership And Dependencies](./ownership-and-dependencies.md).
