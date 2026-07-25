# Svelte Project Structure And Lifecycle Mapping

Use this reference for Svelte/SvelteKit file placement, runtime selection, and lifecycle mechanisms. It maps framework constructs to their canonical policy owners and defines no universal folder tree, component hierarchy, size threshold, type policy, or test strategy.

For each changed construct, load every policy owner linked by the matching row.

## Construct Mapping

| Svelte/SvelteKit construct | Framework responsibility | Policy owner |
| --- | --- | --- |
| `+page.svelte`, `+layout.svelte` | Page or route-tree view handoff and composition | Frontend [Ownership And Dependencies](../../areas/frontend/ownership-and-dependencies.md), [Components And Promotion](../../areas/frontend/components-and-promotion.md), and [State Ownership](../../areas/frontend/state-ownership.md) |
| `+page.ts`, `+layout.ts` | Universal load boundary that may run during server rendering and in the browser | Frontend [Boundaries And Integrations](../../areas/frontend/boundaries-and-integrations.md) and [State Ownership](../../areas/frontend/state-ownership.md) |
| `+page.server.ts`, `+layout.server.ts` | Server-only load/action boundary for cookies, secrets, credentials, or trusted runtime dependencies | Frontend [Boundaries And Integrations](../../areas/frontend/boundaries-and-integrations.md); add the [Backend Area](../../areas/backend/INDEX.md) when backend product behavior changes |
| `+server.ts` | HTTP request/response adapter | Frontend [Boundaries And Integrations](../../areas/frontend/boundaries-and-integrations.md), [Backend Area](../../areas/backend/INDEX.md), and [HTTP/API](../../protocols/http/INDEX.md) |
| `+error.svelte`, `handleError`, `error`, `fail`, redirects | Framework failure representation and error-boundary handoff | Frontend [Boundaries And Integrations](../../areas/frontend/boundaries-and-integrations.md), [State Ownership](../../areas/frontend/state-ownership.md), and [Testing](../../areas/frontend/testing.md); TypeScript [Closed States](../../languages/typescript/closed-states.md) when handling is exhaustive |
| `hooks.server.ts`, `hooks.client.ts`, runtime initialization | Runtime composition or request lifecycle entry | Frontend [Ownership And Dependencies](../../areas/frontend/ownership-and-dependencies.md) and [Boundaries And Integrations](../../areas/frontend/boundaries-and-integrations.md) |
| `.svelte` components, props, callbacks, events, snippets | Framework view mechanisms inside an existing owner | Frontend [Components And Promotion](../../areas/frontend/components-and-promotion.md), [State Ownership](../../areas/frontend/state-ownership.md), and [File Organization](../../areas/frontend/file-organization.md) |
| Runes, stores, context | Framework state/dependency mechanisms inside an existing owner | Frontend [State Ownership](../../areas/frontend/state-ownership.md) and [Ownership And Dependencies](../../areas/frontend/ownership-and-dependencies.md) |
| Form actions, `use:enhance`, action results | Progressive form lifecycle spanning browser and server boundaries | Frontend [Boundaries And Integrations](../../areas/frontend/boundaries-and-integrations.md), [State Ownership](../../areas/frontend/state-ownership.md), and [Testing](../../areas/frontend/testing.md) |
| `depends`, `invalidate`, `invalidateAll`, navigation refresh | SvelteKit data dependency and refresh mechanism | Frontend [State Ownership](../../areas/frontend/state-ownership.md), [Boundaries And Integrations](../../areas/frontend/boundaries-and-integrations.md), and [Testing](../../areas/frontend/testing.md) |
| SSR, CSR, prerendering, browser/server guards | Runtime and representation selection | Frontend [Ownership And Dependencies](../../areas/frontend/ownership-and-dependencies.md) and [Boundaries And Integrations](../../areas/frontend/boundaries-and-integrations.md) |
| SvelteKit adapters, platform bindings, deployment configuration | Deployment/runtime integration owned by repository composition | Frontend [Ownership And Dependencies](../../areas/frontend/ownership-and-dependencies.md) and [Boundaries And Integrations](../../areas/frontend/boundaries-and-integrations.md) |
| Scoped styles, global style entry points, accessibility compiler diagnostics | Svelte styling scope and compiler evidence | [Frontend Area](../../areas/frontend/INDEX.md) and the active frontend-design workflow |

## Framework Semantics

- Use `+layout.svelte` for route-tree composition and persistent route-shell UI, and `+page.svelte` for the page-level view handoff. These filenames do not authorize product logic in the route owner.
- Universal `+page.ts` and `+layout.ts` modules must remain safe for every runtime in which SvelteKit executes them. Use `.server.ts` only when the code requires the trusted server runtime.
- Treat `+server.ts` as an HTTP boundary. Server placement does not make product policy framework-owned.
- Keep server-only modules and dependencies outside browser-reachable imports. Validate this through the frontend dependency graph, including aliases and barrels.
- Treat `$lib` as an import alias, not an owner. Apply frontend ownership and file-organization policy beneath it.
- Runes, stores, and context implement state or dependency access; they do not choose state scope or make a forbidden dependency valid.
- Treat props and route data as owner-provided inputs. Callbacks and events request changes from the transition owner; snippets express view composition rather than hiding product behavior behind configuration.
- Keep component props, callbacks, events, and snippets typed through their owner contracts. Use SvelteKit's generated types for route data and action results when TypeScript is active.
- Form actions, `use:enhance`, and invalidation implement lifecycle handoffs; they do not change which boundary, state owner, or product workflow owns the behavior.
- Start a form action with a native `POST` form path that works without client-side JavaScript. Add `use:enhance` only for progressive behavior and preserve the same server action and product outcomes.
- Return non-sensitive submitted values and field failures through the action result when correction is possible. Keep immediate interaction checks, authoritative validation, and dependency failures distinct through that lifecycle.
- Use named `depends` keys and targeted `invalidate` when the changed workflow owns a specific data dependency. An indiscriminate refresh requires an explicit workflow reason.
- Keep component-scoped styles with the component mechanism. Route resets, tokens, application-shell primitives, and typography through the repository's declared global style owner.
- Resolve Svelte accessibility compiler diagnostics. Suppress one only when the accessibility contract is intentionally satisfied another way and the local rationale plus verification are recorded.

## Framework Dependency Mapping

- Apply the base dependency gate to browser, Svelte, and SvelteKit primitives before adding a framework-adjacent library.
- Add a query or state library only when the selected state and integration contracts require behavior that load functions, invalidation, runes, stores, and explicit adapters do not provide.
- Add visual framework, chart, table, or form dependencies only when established product or design-system owners cannot meet the required behavior.
- Use the repository-approved CodeMirror integration for a full code-editing surface. A native text control remains appropriate when code-editing behavior is not required.

## Verification Mapping

Run the repository-configured Svelte typecheck and build checks. Use frontend [Testing](../../areas/frontend/testing.md) for test seams and the active frontend-design workflow for affected-route, browser-console, responsive, keyboard, focus, and accessibility evidence. This reference does not invent repository command names or duplicate those procedures.
