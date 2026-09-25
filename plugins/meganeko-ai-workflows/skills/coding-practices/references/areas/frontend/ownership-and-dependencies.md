# Frontend Ownership And Dependencies

Use this reference when a change adds, moves, or connects frontend responsibilities. It owns both owner selection and allowed dependency direction because those decisions are incomplete in isolation. Other references own boundary mechanics, state, UI neutrality and promotion, physical file organization, and testing.

## Required Change Map

The change map is required before editing here. It is defined once, with a worked example, in [Core Practices](../../core.md).

Two frontend-specific additions:

- Use repository names and layout when their contracts are clear.
- A newly proposed owner or edge is an architecture decision, not an implementation detail. Resolve it before editing, using the owner table and dependency graph below.

## Production Owners

| Owner | Owns | Excludes |
| --- | --- | --- |
| Framework or route adapter | Framework inputs/outputs, route selection, metadata, and handoff | Reusable product workflow, remote-client detail, and domain policy |
| Application composition | Shell, navigation, session coordination, providers, runtime initialization, and deliberate cross-feature workflows | Feature internals and domain construction |
| Feature | Product workflow, product UI, state, commands, queries, copy, formatting policy, and feature-local contracts | Peer feature internals and neutral primitive ownership by convenience |
| Runtime-neutral domain | Invariants and pure behavior that genuinely cross owners or runtimes | UI framework, browser, server, transport, persistence, and design-system dependencies |
| Design system | Product-neutral primitives, tokens, accessibility behavior, and compositional UI contracts | Product permissions, workflow, statuses, copy, and remote data shapes |
| Integration adapter | Protocol, SDK, persistence, browser API, worker, or remote-service mechanisms | Product workflow, product retry policy, and domain rules |

Keep a contract feature-local until another owner or runtime actually consumes the same invariant. Do not create global `domain`, `shared`, `common`, `platform`, `components`, `services`, `utils`, or `types` buckets to postpone an ownership decision.

## Dependency Direction

Use the repository's declared graph when it is stricter. Otherwise use these defaults:

```text
framework/route adapter -> application or feature public entry point
application composition  -> feature public entry points + app services
feature UI/workflow/state -> feature contracts + runtime-neutral domain
                            + design-system primitives
feature workflow          -> feature-owned boundary client, when direct
integration adapter       -> inward-facing feature/domain port, when port-based
application composition   -> wires ports to adapters when runtime wiring exists
runtime-neutral domain    -> runtime-neutral dependencies only
design system             -> product-neutral dependencies only
tests                     -> the owner or public seam under test
```

- Keep feature internals private and exports narrow.
- Peer features do not import each other's internals. Application composition owns deliberate cross-feature coordination.
- Production code never depends on test code.
- A barrel, alias, callback, context, store, cast, or service locator does not make a forbidden edge valid.
- Stop when the requested edge is outside the declared graph. Resolve the owner or dependency decision explicitly.

## Enforcement

The declared graph above is the canonical policy.

**Also load:** [Dependency Enforcement](./dependency-enforcement.md) when configuring import checks, recording an exception, or resolving a forbidden edge.
