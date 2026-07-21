# TypeScript/JavaScript Language

Use this branch for TypeScript/JavaScript code in frontend or backend contexts, API clients, validation, state modules, component props, event contracts, generated client usage, type safety, build configuration, and dependency choices.

## Language-Wide Rules

- Keep TypeScript at maximum practical strictness. Do not weaken compiler options to bypass a real model or boundary problem.
- Keep generated types near generated clients and convert them when their representation or guarantees differ from the inward-facing contract.
- Keep API clients responsible for transport mechanics, boundary decoding, and dependency-error normalization. Keep components focused on rendering and interaction.
- Use `type` for payloads, DTOs, domain values, branded primitives, unions, and inferred schema output. Use `interface` for service, adapter, or class/object implementation contracts.
- Follow the repository's established interface-prefix convention. Do not introduce or remove `I*` prefixes piecemeal.

## Type-System Routing

Select every row matched by the change. An explicit **Also load** instruction in a focused reference adds another reference; ordinary links do not.

| Changed concern | Load |
| --- | --- |
| External runtime data, schemas/parsers, canonicalization, domain-safe values, brands, or trusted construction | [Runtime And Domain Values](./runtime-and-domain-values.md) |
| Finite internal variants, discriminated unions, or exhaustive behavior | [Closed States](./closed-states.md) |
| `any`, assertions, direct casts, double casts, or external/framework typing gaps | [Casts And Escape Hatches](./casts-and-escape-hatches.md) |
| Generic API relationships, constraints, or inference | [Generics](./generics.md) |
| Static non-assignability, inference, schema-output, or exhaustiveness guarantees | [Type Contract Tests](./type-contract-tests.md) |

## Type Processing Order

1. Treat data without a runtime guarantee as `unknown` and establish its runtime contract through Runtime And Domain Values.
2. Model finite internal behavior through Closed States only after boundary validation has established the variant.
3. Use Casts And Escape Hatches only for a contained external typing limitation; a cast never substitutes for steps 1 or 2.
4. Use Generics only when callers depend on a real relationship.
5. Add Type Contract Tests for static guarantees that runtime tests cannot prove.

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

## TypeScript Gates

- Use `unknown` for untrusted input until a runtime parser establishes its contract.
- Do not use `any`, a direct assertion, or a double cast to hide a boundary, product-model, or generic-relationship problem.
- A TypeScript declaration, generated interface, assertion, or `enum` does not validate runtime data.
- When a typing problem is complex, identify the actual data owner and boundary before changing types. If the correct contract requires a product or API decision, ask the user instead of encoding an assumption.
