# Backend Testing

Use this reference to select backend test seams by production responsibility and boundary risk.

## Unit Seams

- Use unit tests for pure domain invariants and deterministic validation.
- Test service and use-case orchestration with small fakes at repository, outbound, clock, identifier, authorization, and other real dependency seams.
- Choose seams based on language constraints, risk, and clarity.

## Integration Seams

- Test transport, middleware, service, and composition wiring at an integration seam.
- Test repository SQL behavior and persistence mapping with repository integration tests.
- Test outbound request construction, dependency-failure normalization, retry behavior, and client configuration at the outbound boundary.
- Test product-error mapping and protocol error-envelope behavior at the transport boundary.
- Test migration compatibility when schemas, persisted representations, or startup migrations change.

## Test Shape

- Prefer small hand-written fakes when they clearly express the contract.
- Avoid mock-heavy tests that only prove call order, private helper use, or other implementation details.
- Control clocks, schedulers, network completions, and identifiers at their owning seams.
- Test public behavior and observable outcomes rather than private structure.
