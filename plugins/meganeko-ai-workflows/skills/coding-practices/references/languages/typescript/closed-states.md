# TypeScript Closed States

Use this reference when internal behavior depends on a finite set of variants or every variant must be handled.

- Prefer a discriminated union when variants carry different data or behavior. Use a literal union or repository convention for simple finite values.
- Keep the discriminant and its payload in one declaration so impossible combinations are unrepresentable.
- Handle behavior-driving unions exhaustively with a `never` assertion or the repository's equivalent compile-time check.
- Keep one canonical variant declaration. Derive schemas, constants, and display mappings from it when practical instead of synchronizing duplicate lists manually.

Static closure is not runtime validation. A TypeScript union or `enum` cannot prove that external data contains a known variant.

- Parse external variants through the runtime boundary before treating them as closed.
- Reject unknown variants unless the product contract deliberately defines an explicit `unknown` or forward-compatible variant. Do not cast an unknown string into the union.
- If an explicit unknown variant exists, define its allowed payload and behavior as part of the union rather than using a permissive fallback object.

**Also load:** [Runtime And Domain Values](./runtime-and-domain-values.md) when variants enter from API, storage, URL, form, worker, configuration, or other runtime data.

**Also load:** [Type Contract Tests](./type-contract-tests.md) when exhaustive handling is an important static guarantee.
