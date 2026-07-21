# TypeScript Generics

Use this reference when designing or changing a generic API.

- Introduce a type parameter only when callers rely on a relationship it preserves, such as input to output, correlated fields and callbacks, schema to parsed output, a port to its implementation, or a reused data structure or algorithm.
- A parameter used only in one input position usually adds no caller-visible relationship. Make the API concrete unless the parameter preserves a documented constraint that cannot be expressed more directly.
- Carry the parameter through every public member participating in the relationship. Do not erase it at a callback, collection member, event, or result boundary.
- Prefer call-site inference and the narrowest useful constraint. Use a descriptive parameter name when a single letter hides the relationship.
- Use a concrete feature wrapper when a neutral generic primitive gains product permissions, statuses, commands, copy, or styling policy.
- Do not create generic service, repository, component, hook, store, or command frameworks for one implementation or anticipated reuse.
- Do not repair a broken relationship with `any`, a double cast, or a caller-selected result assertion. Fix the relationship or make the API concrete.

**Also load:** [Type Contract Tests](./type-contract-tests.md) when inference or correlation is part of the public contract.
