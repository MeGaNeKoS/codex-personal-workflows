# TypeScript Casts And Escape Hatches

Use this reference when code proposes `any`, an assertion, a direct cast, a double cast, or a workaround for an external/framework typing limitation.

Prefer narrowing, runtime parsing, `satisfies`, type guards, overloads, corrected generic relationships, or a typed adapter. `as const` used to preserve literal inference is not an escape hatch.

A cast is permitted only when all of these are true:

1. the mismatch comes from an identified external library, framework, DOM, generated declaration, or compiler limitation;
2. the cast is inside the smallest adapter where that limitation is directly observable;
3. every runtime assumption observable at that boundary is checked or guaranteed by a cited external contract;
4. the asserted type does not escape as a broader unchecked representation; and
5. a focused test or reproducible typecheck covers the adapter contract.

- Document the specific limitation and invariant, not merely that the cast is "safe" or "unavoidable".
- Avoid `any`; when an external signature forces it, convert to `unknown` immediately and narrow before use.
- Do not use `as unknown as Target` or `as any as Target` to cross unrelated types.
- Do not use casts to create domain brands, validate runtime data, force a closed variant, repair a generic relationship, or make fixtures convenient.
- Test code has no broader cast exception. It may use an escape hatch only for the same directly observable external typing limitation that would justify a production adapter.
- Domain brand construction is authorized only by Runtime And Domain Values. This file owns external typing escape hatches and does not authorize trusted construction.

If the five conditions cannot be demonstrated, fix the declaration, adapter, runtime contract, or model instead of casting.
