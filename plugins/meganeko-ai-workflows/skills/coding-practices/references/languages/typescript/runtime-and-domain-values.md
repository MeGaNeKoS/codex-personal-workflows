# TypeScript Runtime And Domain Values

Use this reference when TypeScript receives external data, defines a runtime schema/parser, canonicalizes a representation, or constructs a domain-safe value.

This file owns the complete boundary type pipeline. Closed-state handling, general cast escape hatches, generic API design, and compile-time test mechanics have separate owners.

## Boundary Pipeline

1. Accept data without a runtime guarantee as `unknown`.
2. Parse and validate the external representation with the boundary's runtime schema or explicit validator.
3. Canonicalize representation details such as trimming, case, units, timestamps, URLs, and identifiers when the owning contract requires it.
4. Invoke the constructor owned by the domain value or invariant.
5. Return the exact parsed or constructed type and retain it through internal contracts.

- A runtime schema or parser is authoritative for the external shape it validates. Derive its TypeScript output when the library supports inference; do not maintain a separate handwritten assertion of the same shape.
- For a type-first external contract, provide one runtime validator and a compile-time conformance check between its output and the static contract.
- A generic schema helper must bind its result to the exact schema argument. Reject caller-selected APIs such as `parse<T>(schema, value): T` when `T` is not derived from `schema`.
- Validation proves runtime shape and constraints. Canonicalization decides representation. Domain construction establishes the named invariant. Keep those steps explicit even when one library call implements more than one step.
- Return failures through the repository's established result or exception contract. Do not convert validation failures into a partially typed value.
- Test representative invalid input, canonicalization, and the established output contract at the boundary owner.

## Domain-Safe Values

- Use a named or branded type when interchangeable primitives could violate an invariant, such as distinct identifiers, units, canonical URLs, timestamps, durations, tokens, or validated names.
- The module owning the invariant owns its brand symbol, ordinary parser/constructor, trusted constructor when justified, and conversion back to an external representation.
- Do not export a reusable brand-casting helper or scatter direct brand assertions through callers.
- Preserve domain-safe values through feature state, callbacks, events, services, ports, repositories, and adapters until a real boundary requires conversion.

## Trusted Construction

Ordinary external input always uses runtime parsing. A named trusted constructor is allowed only when all of these are true:

1. an upstream mechanism already enforces the same invariant;
2. that guarantee is concrete and reviewable, such as a domain-owned serialized round trip, a repository-controlled decoder plus matching database constraint, or a generated runtime decoder with an equivalent contract;
3. the caller cannot substitute arbitrary unvalidated input without crossing a visibly named API; and
4. tests cover the upstream guarantee and the trusted construction path.

A TypeScript type, generated interface without runtime decoding, database column type alone, comment, or prior cast is not evidence of trust.

Keep a trusted constructor narrow and domain-owned. Name the trust source in its documentation or API, contain any unavoidable brand assertion inside it, and do not re-export the unchecked representation. This is the only reference that authorizes a cast used solely to construct a domain-safe brand.

**Also load:** [Type Contract Tests](./type-contract-tests.md) when non-assignability or schema-output relationships are part of the contract.
