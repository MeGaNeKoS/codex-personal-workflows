# Frontend Boundaries And Integrations

Use this reference when data crosses a trust, representation, process, or runtime boundary, or when a change designs a client, port, or adapter. Boundary conversion and integration design remain together because the adapter is where the external representation is observable.

## Boundary Pipeline

For each crossing:

1. name the boundary owner and external representation;
2. decode untrusted input and normalize representation-specific failures;
3. canonicalize values such as units, timestamps, URLs, and identifiers;
4. invoke validation or construction owned by the inward-facing contract; and
5. return the established feature or domain value without weakening it in later layers.

Apply this pipeline independently at every real boundary. "Decode once" means once per boundary crossing, not once for an entire application. A nested payload or later persistence/worker crossing may require another owned conversion.

- Treat remote responses, URL values, public runtime configuration, form/action payloads, persisted data, browser storage, worker messages, and cross-window messages as boundaries whenever trust or representation changes.
- Keep wire DTOs, generated protocol types, SDK types, and framework payloads inside the boundary owner.
- Preserve domain-safe identifiers, units, canonical values, timestamps, and closed states through state, props, commands, callbacks, events, ports, and adapters.
- Keep immediate client interaction checks, authoritative API or server validation, and transport or operation failures distinct. Return explicit outcomes that the state or workflow owner can handle without parsing adapter-specific exceptions.

## Integration Shape

- Keep protocol, SDK, persistence, browser API, worker, and remote-service details in the smallest adapter that can observe them.
- One feature may own a boundary client directly when it is the only production consumer and there is one runtime and implementation.
- Introduce a port only when substitution, runtime composition, external-dependency isolation, or multiple implementations makes the seam concrete. Predicted reuse is not enough.
- Keep feature-specific clients with the feature unless a separate integration owner has a declared matching contract.
- Keep server-only and secret-bearing implementations out of browser-reachable imports, including indirect barrel and alias paths.

## External Operation Ownership

- The adapter owns transport configuration, protocol retries, timeouts, cancellation mechanisms, SDK behavior, and normalization of dependency failures.
- The feature workflow or state owner owns whether the product retries, polls, refreshes, caches, shows stale data, or exposes progress and cancellation to the user.
- A transport cache may stay inside an adapter only when it does not decide product freshness or workflow behavior. Product-visible freshness and invalidation belong to the state owner.
- Do not let both an adapter and a state owner independently retry, cache, or schedule the same operation.

**Also load:** [State Ownership](./state-ownership.md) when loading, retry, polling, refresh, cancellation, cache visibility, or other user-visible lifecycle state changes.

**Also load for TypeScript:** [Runtime And Domain Values](../../languages/typescript/runtime-and-domain-values.md) when the boundary validates external data or constructs domain-safe values.
