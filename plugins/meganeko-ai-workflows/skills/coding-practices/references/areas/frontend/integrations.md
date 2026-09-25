# Frontend Integrations

Use when designing a client, port, or adapter for an external system. This file owns the integration's shape and its operational responsibilities. The data conversion it performs belongs to [Boundaries](./boundaries.md).

## Integration Shape

- Keep protocol, SDK, persistence, browser API, worker, and remote-service details in the smallest adapter that can observe them.
- One feature may own a boundary client directly when it is the only production consumer and there is one runtime and implementation.
- Introduce a port only when substitution, runtime composition, external-dependency isolation, or multiple implementations makes the seam concrete. Predicted reuse is not enough.
- Keep feature-specific clients with the feature unless a separate integration owner has a declared matching contract.
- Keep server-only and secret-bearing implementations out of browser-reachable imports, including indirect barrel and alias paths.

## External Operation Ownership

The split that causes the most bugs:

```text
Adapter owns          transport config, protocol retries, timeouts,
                      cancellation mechanism, SDK behavior,
                      normalizing dependency failures

State owner owns      whether the product retries, polls, refreshes,
                      caches, shows stale data, exposes progress
                      or cancellation to the user
```

- A transport cache may stay inside an adapter only when it does not decide product freshness or workflow behavior. Product-visible freshness and invalidation belong to the state owner.
- Do not let both an adapter and a state owner independently retry, cache, or schedule the same operation.

Double-retry is the classic symptom: the adapter retries three times, the state owner retries three times, and one user action becomes nine requests with a timeout that no longer means anything.

**Also load:** [State Ownership](./state-ownership.md) when loading, retry, polling, refresh, cancellation, cache visibility, or other user-visible lifecycle state changes.
