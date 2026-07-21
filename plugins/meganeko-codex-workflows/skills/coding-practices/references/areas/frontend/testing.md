# Frontend Testing

Use this reference to select verification seams for changed frontend behavior. Test placement follows repository convention; test responsibility follows the production owner.

| Changed responsibility | Primary evidence |
| --- | --- |
| Pure domain rule or derivation | Deterministic unit test at the domain/feature owner |
| State transition or workflow | State-owner test covering commands and observable transitions |
| Boundary parser or adapter | Contract/integration test with representative valid, invalid, and dependency-failure inputs |
| Route or framework handoff | Framework integration test for data, action, error, and navigation behavior |
| Component interaction | User-observable interaction test through semantics, keyboard, focus, and accessible names |
| Cross-owner workflow | Browser or end-to-end test at the public workflow seam |
| Dependency direction | Repository import-boundary, lint, compiler, build, or targeted import evidence |

- Control clocks, schedulers, network completions, and identifiers at their owning seam. Cover stale-result, cancellation, retry, and synchronization behavior when present.
- Verify loading, empty, partial, stale, error, permission, disabled, and validation states that the changed workflow can reach.
- Test public behavior rather than component internals, store implementation details, or private helper call order.
- Use the active frontend-design workflow for responsive, visual, keyboard, focus, announcement, and browser-console evidence. This reference owns test seams, not visual standards.
- Run the narrowest relevant tests during iteration, then the repository's required frontend typecheck, test, build, and browser verification gates before completion.

**Also load for TypeScript:** [Type Contract Tests](../../languages/typescript/type-contract-tests.md) when correctness depends on non-assignability, inference, schema-output relationships, or exhaustiveness.
