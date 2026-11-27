# Backend Architecture And Dependencies

Use this reference when assigning backend responsibilities, choosing repository or outbound ownership, or introducing a port or adapter.

- Apply modular backend design by responsibility; adapt the pattern to the current project and language instead of copying a folder structure blindly.
- Prefer the option that improves boundary clarity, testability, and replacement cost without adding ceremony the project does not need.

## Layer Responsibilities

- Keep transport, service/use-case, domain, repository, outbound, and infrastructure concerns separate.
- Keep handlers and controllers thin: decode protocol input, call one owning service or use case, map successful results, and map product errors into protocol errors.
- Keep business rules, database calls, external client calls, deep dependency construction, and inconsistent response or error formatting out of transport code.
- Let service and use-case code own orchestration: call the compute function that checks the use-case rule, coordinate domain objects, call repositories for owned storage, call outbound clients for external systems, and pass their product errors on. The rule itself, a limit, an eligibility check, or a calculation, is a function over values in domain code, per [Function Shape](../../function-shape.md).
- Keep services free of HTTP, gRPC, Kafka, framework, route, header, response-envelope, and protocol-specific status types.
- Let domain code own product concepts and invariants. Keep it deterministic, framework-free, storage-free, and responsible for valid state.
- Let infrastructure and adapters own concrete technology, including SQL clients, HTTP or gRPC clients, SDKs, retry policy, serialization, client-specific errors, and connection management.

## Repository Versus Outbound

- Treat repositories as owned storage: service-owned relational tables, product-owned object-storage buckets, internal durable queues, and owned analytical or event tables.
- Treat external systems as outbound clients even when they return data: third-party APIs, other services, SaaS or vendor systems, identity providers, remote license services, notification providers, and authorization services.
- Keep repository and outbound ports owned near the service or domain side; keep concrete adapters in infrastructure.

The test is **ownership of the data, not whether it returns rows**:

```text
Repository (we own the storage)         Outbound (someone else owns the system)
----------------------------------      --------------------------------------
orders table in our Postgres            Stripe charges API
our S3 bucket of invoice PDFs           Auth0 user profile lookup
our internal durable job queue          another team's service, even internal
our analytics event table               SendGrid delivery status

Both return data. Only the left column is a repository.
```

Why it matters: an outbound client can fail, rate-limit, change contract, or go down independently. Its adapter normalizes those failures into product errors, and the calling service decides what the product does about each. A repository failure is usually infrastructure failure of something you control.

```text
Wrong: UserRepository.findById() that calls the Auth0 API.
       Named "repository", so retries and outages are never designed for.

Right: AuthorizationClient.fetchProfile() behind an outbound port.
       Its adapter maps the timeout into a product error the service handles.
```

## Thin Transport, Applied

```ts
// Wrong: handler owns business rules, storage, and error shape.
app.post('/orders', async (req, res) => {
  const items = req.body.items
  if (items.length > 50) return res.status(400).json({ e: 'too many' })
  const total = items.reduce((s, i) => s + i.price * i.qty, 0)
  if (total > 100_000) return res.status(400).json({ e: 'limit' })
  await db.query('INSERT INTO orders ...')
  res.json({ ok: true })
})

// Right: decode, call one owner, map result and error.
app.post('/orders', async (req, res) => {
  const command = PlaceOrderRequest.parse(req.body)   // decode at boundary
  const result = await orderService.place(command)    // one owning service
  if (!result.ok) return problem(res, result.error)   // one error formatter
  res.status(201).json(toOrderResponse(result.value))
})
```

The order limits are product rules, so they belong in domain code that the service calls, where they are testable without HTTP.

## Real-Boundary Abstraction

- Abstract real boundaries, not every helper.
- Use interfaces, traits, protocols, or ports for databases, external service clients, message brokers, object storage, clocks, deterministic ID generation, authorization clients, license verification, and runtime or process notifications.
- Do not abstract pure functions, simple deterministic calculations, local formatting helpers, one-off internal code with no replacement need, or data structures used by one module only.
- Treat dependency injection as a testing and replacement tool, not a universal rule.

## Dependency Direction

- Point dependencies inward: transport depends on service or feature entry points; services depend on domain contracts and inward-facing ports; adapters implement those ports; infrastructure supplies concrete technology.
- Keep runtime-neutral domain code dependent only on runtime-neutral concerns.
- Keep feature or service internals private and public exports narrow.
- A forbidden edge stays forbidden however it is reached. The gate is in [Core Practices](../../core.md).
- Keep production code independent of test code.
- Stop when a requested edge has no declared owner or violates the dependency graph, and ask, as [Core Practices](../../core.md) describes.

## Failure Modes

- Before implementing anything that calls an outside system (a database, a queue, another service), find the stated behavior for each failure mode: timeout, retry, partial write, crash midway, and outside system down. Take it from the task, the change map, or the existing code. Where none is stated, ask; do not choose the product's behavior yourself. When you are the one writing the brief or the design, state it there.
- This is a design question, not a constraint on the order code gets typed in.

## Idempotent Writes

- Anything that writes (a handler, a queue consumer, a retried outbound call) has a stated way to detect a repeat: an idempotency key, an upsert, or a state check, or a stated reason the write cannot repeat. Use the method the task or the existing code gives. A new key column or stored record is a decision: ask before adding it.
- Reads are exempt.
