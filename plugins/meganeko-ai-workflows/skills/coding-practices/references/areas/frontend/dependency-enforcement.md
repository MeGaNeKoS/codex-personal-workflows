# Frontend Dependency Enforcement

Use when proving the dependency graph, configuring import boundaries, or handling a forbidden edge. The graph itself is declared in [Ownership And Dependencies](./ownership-and-dependencies.md); this file owns how it is enforced.

The declared graph is the canonical policy. Enforcement proves that policy; it never defines a second one.

## Proving The Graph

- Use the repository's existing import-boundary, module-graph, lint, compiler, or build checks when available. Update the configuration owner when the intended graph changes.
- When no dedicated boundary tool exists, verify affected imports with the narrowest reproducible repository command and a targeted import search. Record any edge that remains manually verified.
- Report the exact command or inspection used. Do not claim dependency enforcement without executable or reviewable evidence.

## Exceptions

- Keep exceptions narrow, named, owned, and time-bounded when the repository supports expiry metadata.
- Never use wildcard ignores or a barrel to conceal an exception.
- A baseline records known violations only when the repository already uses that migration mechanism. New violations fail the change.

```text
Wrong   eslintrc: "import/no-restricted-paths": "off"
        one line disables the graph everywhere, forever, with no owner

Right   allow: features/billing -> features/invoices/contracts
        owner: billing team
        reason: shared invoice contract pending extraction to domain
        expires: 2026-Q1
```

An exception without an owner and a reason is indistinguishable from a bug.

## Forbidden Edges

When a change requires an edge outside the declared graph, stop. Resolve the ownership decision explicitly.

A barrel, alias, callback, context, store, cast, or service locator does not make a forbidden edge valid. It only makes it invisible to enforcement, which is worse than the original violation.
