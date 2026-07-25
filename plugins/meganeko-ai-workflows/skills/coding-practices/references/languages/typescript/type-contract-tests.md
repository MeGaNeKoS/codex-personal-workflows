# TypeScript Type Contract Tests

Use this reference when correctness depends on static non-assignability, inference, schema-output relationships, or exhaustiveness.

Test the contract that runtime tests cannot prove:

- distinct domain-safe values are not mutually assignable and unchecked primitives cannot enter them;
- generic calls infer the intended result and correlated callbacks retain their parameter relationship;
- a schema helper returns the output of the exact schema passed to it; and
- behavior-driving closed states remain exhaustive.

## Executable Test Gate

1. Use the repository's existing type-test facility and required command when one exists.
2. Otherwise add the smallest dedicated compile-only fixture covered by a checked-in `tsconfig` with `noEmit: true`. Use the repository's filename convention, or a clear convention such as `*.type-test.ts`.
3. Use `@ts-expect-error` for intentional negative cases so the test fails when an invalid expression becomes valid. Pair negative cases with positive assignments or equality helpers proving the expected inferred type.
4. Add the compile-only command to the repository's normal test scripts or CI gate. A fixture that no standard command executes is not coverage.
5. Report the exact command and result. If repository constraints prevent an executable gate, report the static invariant as unverified.

Keep runtime tests for runtime validation and behavior. Type-contract tests supplement them; they do not replace them.
