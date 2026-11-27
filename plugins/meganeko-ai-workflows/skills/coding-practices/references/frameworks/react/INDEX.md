# React Framework

Use this branch for React components, hooks, effects, context, and data fetching, in a React application or a framework built on it such as Next.js.

## Architecture Routing

Apply policy in this order:

1. the base coding-practices gates;
2. the [frontend area index](../../areas/frontend/INDEX.md) and every focused frontend reference selected by the changed concerns;
3. the [TypeScript index](../../languages/typescript/INDEX.md) and selected TypeScript references when TypeScript is active; and
4. the construct mapping below, for which owner's policy applies to each React construct.

This index is a router. Frontend references own architecture and behavior policy, TypeScript references own type-system mechanics, and the mapping below places those decisions onto React constructs without redefining them.

## Construct Mapping

For each changed construct, load every policy owner linked by the matching row.

| React construct | Framework responsibility | Policy owner |
| --- | --- | --- |
| Function component, props, children, callback props | View mechanism inside an existing owner | Frontend [Component Ownership](../../areas/frontend/component-ownership.md), [Promotion](../../areas/frontend/promotion.md), and [Naming And File Structure](../../areas/frontend/naming-and-file-structure.md) |
| `useState`, `useReducer`, derived values | Local state and its transitions | Frontend [State Ownership](../../areas/frontend/state-ownership.md) |
| Custom hook | A named unit of state or integration access inside an owner | Frontend [State Ownership](../../areas/frontend/state-ownership.md) and [Ownership And Dependencies](../../areas/frontend/ownership-and-dependencies.md) |
| `useEffect`, subscriptions, timers | Synchronization with something outside React | Frontend [Integrations](../../areas/frontend/integrations.md) and [State Ownership](../../areas/frontend/state-ownership.md) |
| Data fetching: a query library hook, a route loader, a server action | Boundary to a remote system | Frontend [Integrations](../../areas/frontend/integrations.md) and [Boundaries](../../areas/frontend/boundaries.md) |
| Context, providers, external stores | Dependency or state access across a subtree | Frontend [State Ownership](../../areas/frontend/state-ownership.md) and [Ownership And Dependencies](../../areas/frontend/ownership-and-dependencies.md) |
| Route files, layouts, server components | Route handoff and composition | Frontend [Ownership And Dependencies](../../areas/frontend/ownership-and-dependencies.md) and [Boundaries](../../areas/frontend/boundaries.md); add the [Backend Area](../../areas/backend/INDEX.md) when server product behavior changes |
| Forms, mutations, optimistic updates | Interaction lifecycle | Frontend [UI Patterns](../../areas/frontend/ui-patterns.md) and [State Ownership](../../areas/frontend/state-ownership.md) |
| Error boundaries, suspense fallbacks | Failure and loading representation | Frontend [State Ownership](../../areas/frontend/state-ownership.md) and [Testing](../../areas/frontend/testing.md) |

## Framework Semantics

- A component renders from props and state. A fetch, a subscription, or a product rule does not sit inline in its body: the integration owner holds the fetch, a hook or a prop delivers the result, and a product rule is a plain function the component calls.
- A custom hook is named `use` plus what it provides (`useInvoiceFilters`), holds one concern, and returns values and callbacks, never markup. It implements state or integration access. It does not choose which owner holds the state.
- An effect synchronizes with something outside React: a subscription, a timer, the DOM, a connection. A value that can be computed from props or state during render is computed there, not stored through an effect.
- `useState`, `useReducer`, context, and external stores implement state. Which owner holds it is decided by State Ownership. Context does not make a forbidden dependency valid.
- A callback prop asks the state's owner for a change (`onRemove`). A child does not receive the setter of state it does not own.
- Route files, layouts, and server components adapt framework inputs and compose owners. A filename does not authorize product logic in the route file.
- External data is decoded at the boundary the frontend Boundaries reference names, and components receive typed, validated values.
- Follow the repository's convention for component files. Where it has none, Naming And File Structure decides.

## Framework Dependency Mapping

A query, state, form, table, chart, or styling library is a new library under [Core Practices](../../core.md): report what it is for and what it would replace, and do not add it unasked. Use what the repository already has first.

## Verification Mapping

Run the repository-configured typecheck, lint, test, and build checks. Use frontend [Testing](../../areas/frontend/testing.md) for test seams and the active frontend-delivery workflow for browser evidence. This reference does not invent repository command names.
