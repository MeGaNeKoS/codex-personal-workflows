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

## Seam Selection, Applied

Pick the seam from what could actually break:

| Risk | Seam | Fakes |
| --- | --- | --- |
| Order total math is wrong | unit, domain | none |
| Service calls payment before saving | unit, service | fake repository + payment port |
| SQL returns wrong rows or mapping loses a field | integration, repository | real database |
| Retry fires twice on a 503 | integration, outbound | stubbed HTTP transport |
| 404 body is not problem+json | integration, transport | fake service raising the product error |
| Old rows fail to decode after a schema change | integration, migration | real database, seeded pre-change |

### Test Shape, Applied

```ts
// Wrong: asserts call order and private structure. Passes while the product breaks.
expect(repo.save).toHaveBeenCalledBefore(payments.charge)
expect(service._buildRow).toHaveBeenCalled()

// Right: asserts an observable outcome at the public seam.
const result = await service.place(command)
expect(result.ok).toBe(true)
expect(await orders.findById(result.value.id)).toMatchObject({ status: 'paid' })

// And the behavior that actually matters on failure:
payments.failNext(new PaymentRejected())
const failed = await service.place(command)
expect(failed.error).toBeInstanceOf(PaymentRejected)
expect(await orders.findById(command.id)).toBeUndefined()  // no partial write
```

The last assertion is the point: it tests the invariant (no orphaned order on payment failure), not the call sequence that currently implements it.
