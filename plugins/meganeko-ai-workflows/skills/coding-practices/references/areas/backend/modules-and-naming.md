# Backend Modules And Naming

Use this reference when splitting backend files or naming models, services, errors, constants, and public module surfaces.

## Responsibility-Based Modules

- Separate data-shape definitions from behavior when a domain becomes non-trivial.
- Use model or type files for payloads, domain values, contracts, and DTOs.
- Use service or use-case files for orchestration and behavior.
- Use error files for domain-specific errors and boundary error mapping.
- Use constants files for named domain rules, protocol values, limits, defaults, and validation rules.
- Split by responsibility before a file becomes the default place for unrelated behavior.
- Do not split tiny modules only to satisfy a template. Split when it improves discoverability, ownership, or reviewability.
- Prefer obvious responsibility over a high file count.

## Public Surfaces

- Keep module roots, package roots, barrels, and `lib.rs` files narrow.
- Expose intentional public APIs, not every internal helper.
- Keep constants private unless they form part of another module's contract.
- Avoid vague buckets such as `common`, `utils`, `helpers`, or `misc` unless the content is genuinely cross-cutting and has a clear contract.

## Constants And Naming

- Name business rules, protocol rules, retry limits, timeouts, size and pagination limits, defaults, validation rules, and security settings instead of scattering magic numbers or strings.
- Keep constants close to the domain, adapter, or protocol boundary that owns the rule.
- Do not create a global constants dumping ground.
- Name types and modules by responsibility and domain, such as `NodeService`, `NodeRepository`, `PostgresNodeRepository`, `OpenFgaAuthorizationClient`, `LicenseVerifier`, and `AuditSink`.
- Avoid vague names such as `Manager`, `Helper`, `Utils`, `Common`, or `Processor` without domain context.

### Applied

A good name states the responsibility *and* the technology when the technology is the point:

```text
Vague                 Named by responsibility        What the name now tells you
------------------    ---------------------------    ---------------------------
DataManager           OrderRepository                owned storage for orders
ApiHelper             StripePaymentClient            outbound, and which vendor
Processor             InvoicePdfRenderer             what it produces
UserUtils             EmailAddress                   a domain value with rules
Service               LicenseVerifier                the single verb it owns
common/validation.ts  order/order.constants.ts       whose rules these are
```

Note the pattern in the port/adapter pair: the port names the responsibility, the adapter prefixes the technology.

```text
OrderRepository            <- port, owned near the domain
PostgresOrderRepository    <- adapter, lives in infrastructure
InMemoryOrderRepository    <- test fake, same contract
```

If you cannot name the adapter without the technology prefix, the port is probably leaking technology into its contract.

### Splitting And Placement, Applied

The file growth rule, its line threshold, and what it gates are [Core Practices](../../core.md); the cases below are examples, not a restatement.

```text
Do not split on line count:
  order.service.ts is 300 lines and cohesive  -> leave it

Do move a function you are rewriting to its owner, even if that owner
already exists:
  order.service.ts has calculateTax() being reworked end to end, and
  tax.service.ts already owns tax rules for the rest of the codebase
  -> move calculateTax(), with its tests, into tax.service.ts

Do create a new file when no owner fits:
  order.service.ts has calculateShipping() being reworked end to end, and
  no existing file owns shipping rules
  -> move calculateShipping(), with its tests, into shipping.calculator.ts

Do put new responsibility in the owner that fits, not the file already open:
  order.service.ts is asked to add PDF rendering and email sending
  -> OrderService keeps orchestration and does not grow to hold them
     InvoicePdfRenderer (neutral, no order rules) gets the new rendering
     OrderNotifier (outbound seam) gets the new notification
```
