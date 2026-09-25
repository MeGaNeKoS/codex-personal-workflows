# TypeScript/JavaScript Language

Use this branch for TypeScript/JavaScript code in frontend or backend contexts, API clients, validation, state modules, component props, event contracts, generated client usage, type safety, build configuration, and dependency choices.

## Routing

| Changed concern | Load |
| --- | --- |
| External runtime data, schemas/parsers, canonicalization, domain-safe values, brands, or trusted construction | [Runtime And Domain Values](./runtime-and-domain-values.md) |
| Finite internal variants, discriminated unions, or exhaustive behavior | [Closed States](./closed-states.md) |
| `any`, assertions, direct casts, double casts, or external/framework typing gaps | [Casts And Escape Hatches](./casts-and-escape-hatches.md) |
| Generic API relationships, constraints, or inference | [Generics](./generics.md) |
| Static non-assignability, inference, schema-output, or exhaustiveness guarantees | [Type Contract Tests](./type-contract-tests.md) |
| File layout, `type` versus `interface`, barrels, strictness settings, or client responsibilities | [Module Shape](./module-shape.md) |

## Processing Order

Apply in this order; a later step never substitutes for an earlier one.

1. Treat data without a runtime guarantee as `unknown` and establish its contract through Runtime And Domain Values.
2. Model finite internal behavior through Closed States, only after boundary validation established the variant.
3. Use Casts And Escape Hatches only for a contained external typing limitation.
4. Use Generics only when callers depend on a real relationship.
5. Add Type Contract Tests for static guarantees runtime tests cannot prove.

## Gates

- Untrusted input is `unknown` until a runtime parser establishes its contract.
- No `any`, direct assertion, or double cast hides a boundary, product-model, or generic-relationship problem.
- A declaration, generated interface, assertion, or `enum` does not validate runtime data.
- When the correct contract requires a product or API decision, ask the user instead of encoding an assumption.
