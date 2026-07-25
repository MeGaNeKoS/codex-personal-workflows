# Frontend State Ownership

Use this reference when a change adds mutable state, a transition, synchronization, or another source of truth.

- Put mutable state in the narrowest owner spanning every legitimate reader and writer: component-local, then feature workflow, then application state. Do not begin with a global store.
- Give every transition one owner. Other owners issue commands or consume snapshots and events; they do not mutate the same state independently.
- Keep cross-feature coordination in application composition unless a deliberate public feature contract establishes another allowed edge.
- Derive values that can be computed from authoritative state. Maintain a copy only for a named boundary, offline, synchronization, draft-editing, or measured performance reason.
- Distinguish server state, remote cache, persisted state, URL state, workflow state, and ephemeral UI state when their source of truth or lifecycle differs.

## Asynchronous State

- The state owner defines loading, success, empty, stale, partial, cancelled, and failure transitions that affect product behavior.
- Define how newer requests supersede older work, how cancellation is observed, and whether late results may commit. Do not rely on timing to prevent stale writes.
- Keep polling, refresh, product retry, synchronization, and asynchronous-job lifecycle in a named state or workflow owner. Presentational components render state and issue commands; they do not own timers or competing transitions.
- Keep URL and persisted-state synchronization one-directional at each step, with an explicit conflict rule when both sources may change.
- Preserve domain-safe values and validated closed states inside state contracts.

The boundary adapter owns transport and cancellation mechanisms; this reference owns the product-visible lifecycle and transition policy. Do not implement retries, schedules, or caches independently in both places.

**Also load:** [Boundaries And Integrations](./boundaries-and-integrations.md) when state coordinates an external operation.

Test transitions at the state owner, including stale-result, cancellation, retry, and synchronization cases. Test cross-owner workflows at the integration or browser seam.
