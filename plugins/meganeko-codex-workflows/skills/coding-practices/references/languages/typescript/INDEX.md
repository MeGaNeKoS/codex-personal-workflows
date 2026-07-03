# TypeScript/JavaScript Language

Use this branch for TypeScript/JavaScript code in frontend or backend contexts, API clients, validation, state modules, component props, event contracts, generated client usage, type safety, build configuration, and dependency choices.

## Rules

- Keep TypeScript at maximum practical strictness.
- Do not weaken compiler options to bypass real typing issues.
- Avoid `any`; use `unknown` at untrusted boundaries and validate before use.
- Avoid double casts such as `as unknown as Target` and `as any as Target`.
- Prefer discriminated unions for known state variants and result shapes.
- Use branded types for distinct IDs, timestamps, durations, and domain primitives when confusion is likely.
- Validate external data at API, storage, generated client, or browser boundary modules.
- Keep generated types near generated clients and convert to domain/frontend types when the generated shape is awkward for UI logic.
- Keep API clients responsible for transport details, decoding, and error normalization.
- Keep components focused on rendering and interaction, not API protocol details.
- Prefer platform APIs and framework primitives before adding state, form, table, or validation libraries.
- Use `type` for payloads, DTOs, domain values, branded primitives, unions, and inferred schema output.
- Use `interface` for service, adapter, or class/object implementation contracts.

## Backend Module Shape

For non-trivial backend domains, keep type declarations and behavior separate:

```text
<domain>/
  <domain>.types.ts
  <domain>.service.ts
  <domain>.errors.ts
  <domain>.constants.ts
  index.ts
```

Use this as a pattern, not a mandatory tree. Small domains can stay smaller.

- Put public payload types, domain data shapes, branded primitive types, and service/adapter interfaces in type files.
- Keep implementation files focused on behavior.
- Use `index.ts` barrel exports at domain boundaries when they make imports clearer.
- Avoid barrels that hide ownership or create circular dependencies.
- Keep web framework types out of service/domain modules unless the framework is intentionally the application boundary.

## Type Boundaries

- Use `type` for data structures, payloads, API responses, DTOs, domain values, branded primitives, unions, and inferred schema output.
- Use `interface` only for class contracts, service contracts, adapter contracts, or implementation surfaces intended to be implemented by classes/objects.
- Do not use `I*` prefixes if the project style avoids them. If a project already uses `IService`/`IRepository`, follow local consistency.
- Prefer schema validators for API and external input.
- Use direct branded casts only at trusted construction points. Do not scatter brand casts through business logic.

## Constants And Dependencies

- Extract domain constants with names that explain the rule: validation bounds, token expiry, password rules, rate limits, pagination limits, content types, header names, retry limits, and timeouts.
- Keep constants near their domain or protocol boundary.
- Inject DB clients, HTTP clients, queues, object storage, clocks, authorization clients, and license verifiers where tests or replacement require it.
- Do not inject pure deterministic helpers just for style.
- Avoid service locator containers unless the application is large enough to justify them.
