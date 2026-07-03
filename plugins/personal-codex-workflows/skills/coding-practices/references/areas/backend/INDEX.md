# Backend Area

Use this branch for backend architecture, services, transport handlers, use-case layers, domain modules, repositories, outbound clients, API boundaries, error models, dependency boundaries, and test seams.

## Intent

Apply modular backend design by responsibility. Do not copy a folder structure blindly. Translate the pattern into the current project and language.

Prefer the option that improves boundary clarity, testability, and long-term replacement cost without adding ceremony that the current code does not need.

## Layers

- Keep transport, service/use-case, domain, repository, outbound, and infrastructure concerns separate.
- Keep handlers/controllers thin.
- Keep transport code focused on protocol input/output: parse requests, call services/use cases, map successful results, and map product errors into protocol errors.
- Do not put business rules, database calls, external client calls, deep dependency construction, or inconsistent response/error formatting in transport code.
- Let service/use-case code own orchestration: validate use-case rules, coordinate domain objects, call repositories for owned storage, call outbound clients for external systems, and map dependency failures into product errors.
- Keep services free of HTTP/gRPC/Kafka framework types, route paths, headers, response envelopes, and protocol-specific status formatting.
- Let domain code own product concepts and invariants. Keep it deterministic, framework-free, storage-free, and responsible for keeping state valid.
- Let infrastructure/adapters own concrete technology such as SQL clients, HTTP/gRPC clients, SDKs, retry policy, serialization details, client-specific errors, and connection management.

## Repository vs Outbound

- Treat repositories as owned storage only: service-owned relational tables, product-owned object storage buckets, internal durable queues, and owned analytical/event tables.
- Treat external systems as outbound clients even when they return data: third-party APIs, other services, SaaS/vendor systems, identity providers, remote license services, notification providers, or authorization services.
- Keep repository and outbound traits/interfaces owned near the service/domain side, with concrete adapters in infrastructure.

## Abstraction

- Abstract real boundaries, not every helper.
- Use interfaces, traits, protocols, or ports for databases, external service clients, message brokers, object storage, clocks/time, deterministic ID generation, authorization clients, license verification, and runtime/process notifications.
- Do not abstract pure functions, simple deterministic calculations, local formatting helpers, one-off internal code with no replacement need, or data structures only used inside one module.
- Treat dependency injection as a testing and replacement tool, not a universal rule.
- Prefer small hand-written fakes over heavy mocking frameworks when the fake is clearer.

## Types And Modules

- Separate data shape definitions from behavior when a domain becomes non-trivial.
- Use model/type files for payloads, domain values, contracts, and DTOs.
- Use service/use-case files for orchestration and behavior.
- Use error files for domain-specific errors and boundary error mapping.
- Use constants files for named domain rules, protocol values, limits, defaults, and validation rules.
- Keep module roots/package roots/barrels/`lib.rs` files narrow; they should expose intentional public APIs, not every internal helper.
- Do not split tiny modules just to satisfy a template. Split when it improves discoverability, ownership, or reviewability.
- Prefer code that makes responsibility obvious over code that merely has many files.
- Split by responsibility before a file becomes the default place for unrelated behavior.
- Avoid vague root-level buckets such as `common`, `utils`, `helpers`, or `misc` unless the content is genuinely cross-cutting and has a clear contract.

## Domain-Safe Values

- Avoid primitive obsession for values that represent different concepts but share the same runtime type.
- Use the language idiom to make sensitive values distinct: Rust newtypes, TypeScript branded types or schema-inferred values, Go named types plus constructors, Python value objects/dataclasses/validation models/NewType.
- Examples include user ID vs organization ID, URL vs path, seconds vs milliseconds, plaintext secret vs hashed secret, access token vs refresh token.
- Construct domain-safe values at boundaries after validation. Do not scatter unchecked casts or conversions through business logic.

## Constants And Rules

- Avoid magic numbers, magic strings, and unexplained variables in behavior.
- Extract values into named constants when the value is a business rule, protocol rule, retry limit, timeout, size limit, pagination limit, validation rule, or security setting.
- Put constants in discoverable modules when they belong to a module, domain, adapter, or protocol boundary.
- Keep constants close to the domain that owns them. Export only constants that form part of another module's contract.
- Do not create a global constants dumping ground.

## Boundary Validation

- Treat HTTP/gRPC/Kafka payloads, database rows, object storage metadata, environment/config values, and third-party API responses as untrusted.
- Validate or decode at boundaries into typed/domain-safe structures.
- Keep casts or unchecked conversions near validation and explain why they are safe when unavoidable.

## Construction

- Use a composition root for config loading, telemetry/logging, client construction, repository/outbound aggregation, service construction, transport/router/server construction, lifecycle, and shutdown wiring.
- Do not create long-lived concrete clients deep inside handlers or services.
- Make mandatory dependencies explicit in constructors. Invalid construction should fail early or be impossible.
- Use repository/outbound aggregators when a family of dependencies should be grouped, cached, lazily constructed, or passed through request/runtime context.
- Do not let an aggregator become an untyped service locator.

## Errors And Responses

- Keep error and response formatting centralized at the boundary.
- Use centralized product error codes. Errors should carry a stable code, detail/message, category/status mapping, and transport mapping where needed.
- Transport layers convert product errors into protocol formats such as HTTP Problem Details, gRPC status, or broker/dead-letter metadata.
- Do not hand-build unrelated error envelopes in individual handlers.
- Use standardized response envelopes when the product requires them.

## Testing

- Choose test seams based on language constraints, risk, and clarity.
- Use unit tests for pure domain invariants, deterministic validation, and service orchestration with small fakes.
- Use integration tests for transport/middleware/service wiring, repository SQL behavior, outbound request construction, error envelope behavior, and migration compatibility.
- Avoid mock-heavy tests that only prove implementation details.

## Naming

- Name by responsibility and domain: `NodeService`, `NodeRepository`, `PostgresNodeRepository`, `OpenFgaAuthorizationClient`, `LicenseVerifier`, `AuditSink`.
- Avoid vague names such as `Manager`, `Helper`, `Utils`, `Common`, or `Processor` without domain context.

Combine this branch with the relevant language, framework/library, and protocol branches.
