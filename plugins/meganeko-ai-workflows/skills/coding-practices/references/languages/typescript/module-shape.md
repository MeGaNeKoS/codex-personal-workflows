# TypeScript Module Shape

Use when organizing a TypeScript domain into files, choosing `type` versus `interface`, or deciding barrel exports.

## Declarations

- Use `type` for payloads, DTOs, domain values, branded primitives, unions, and inferred schema output.
- Use `interface` for service, adapter, or class/object implementation contracts.
- Follow the repository's established interface-prefix convention. Do not introduce or remove `I*` prefixes piecemeal.
- Keep TypeScript at maximum practical strictness. Do not weaken compiler options to bypass a real model or boundary problem.
- Keep generated types near generated clients and convert them when their representation or guarantees differ from the inward-facing contract.

## Domain Layout

For non-trivial domains, keep type declarations and behavior separate:

```text
<domain>/
  <domain>.types.ts
  <domain>.service.ts
  <domain>.errors.ts
  <domain>.constants.ts
  index.ts
```

Use this as a pattern, not a mandatory tree. Small domains stay smaller.

- Put public payload types, domain data shapes, branded primitive types, and service/adapter interfaces in type files.
- Keep implementation files focused on behavior.
- Keep web framework types out of service/domain modules unless the framework is intentionally the application boundary.

## Barrels

- Use `index.ts` barrel exports at domain boundaries when they make imports clearer.
- Avoid barrels that hide ownership or create circular dependencies.

A barrel is legitimate when it narrows a domain's public surface. It is illegitimate when it exists so an outside module can reach past the domain's contract.

## Clients

Keep API clients responsible for transport mechanics, boundary decoding, and dependency-error normalization. Keep components focused on rendering and interaction.
