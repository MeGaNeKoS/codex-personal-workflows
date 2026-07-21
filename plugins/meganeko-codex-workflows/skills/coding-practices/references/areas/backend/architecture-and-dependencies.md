# Backend Architecture And Dependencies

Use this reference when assigning backend responsibilities, choosing repository or outbound ownership, or introducing a port or adapter.

- Apply modular backend design by responsibility; adapt the pattern to the current project and language instead of copying a folder structure blindly.
- Prefer the option that improves boundary clarity, testability, and replacement cost without adding ceremony the project does not need.

## Layer Responsibilities

- Keep transport, service/use-case, domain, repository, outbound, and infrastructure concerns separate.
- Keep handlers and controllers thin: decode protocol input, call one owning service or use case, map successful results, and map product errors into protocol errors.
- Keep business rules, database calls, external client calls, deep dependency construction, and inconsistent response or error formatting out of transport code.
- Let service and use-case code own orchestration: validate use-case rules, coordinate domain objects, call repositories for owned storage, call outbound clients for external systems, and map dependency failures into product errors.
- Keep services free of HTTP, gRPC, Kafka, framework, route, header, response-envelope, and protocol-specific status types.
- Let domain code own product concepts and invariants. Keep it deterministic, framework-free, storage-free, and responsible for valid state.
- Let infrastructure and adapters own concrete technology, including SQL clients, HTTP or gRPC clients, SDKs, retry policy, serialization, client-specific errors, and connection management.

## Repository Versus Outbound

- Treat repositories as owned storage: service-owned relational tables, product-owned object-storage buckets, internal durable queues, and owned analytical or event tables.
- Treat external systems as outbound clients even when they return data: third-party APIs, other services, SaaS or vendor systems, identity providers, remote license services, notification providers, and authorization services.
- Keep repository and outbound ports owned near the service or domain side; keep concrete adapters in infrastructure.

## Real-Boundary Abstraction

- Abstract real boundaries, not every helper.
- Use interfaces, traits, protocols, or ports for databases, external service clients, message brokers, object storage, clocks, deterministic ID generation, authorization clients, license verification, and runtime or process notifications.
- Do not abstract pure functions, simple deterministic calculations, local formatting helpers, one-off internal code with no replacement need, or data structures used by one module only.
- Treat dependency injection as a testing and replacement tool, not a universal rule.

## Dependency Direction

- Point dependencies inward: transport depends on service or feature entry points; services depend on domain contracts and inward-facing ports; adapters implement those ports; infrastructure supplies concrete technology.
- Keep runtime-neutral domain code dependent only on runtime-neutral concerns.
- Keep feature or service internals private and public exports narrow.
- Do not use a barrel, alias, callback, cast, context, or service locator to conceal a forbidden dependency.
- Keep production code independent of test code.
- Stop when a requested edge has no declared owner or violates the dependency graph; resolve ownership explicitly.
