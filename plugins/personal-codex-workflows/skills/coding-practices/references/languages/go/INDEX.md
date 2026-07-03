# Go Language

Use this branch for Go backend services, handlers, repositories, outbound clients, error types, test seams, package layout, and domain-sensitive values.

- Keep handlers thin and push orchestration into services/use cases.
- Use handlers as controllers: read dependencies from composition wiring or request context, call services, and return typed outputs/errors.
- Define interfaces at the consuming side when they represent real seams.
- Use interfaces for repositories and outbound clients when tests or replacement require it. Go cannot monkey-patch ordinary function calls cleanly, so explicit interfaces and constructor injection are practical test seams.
- Use constructor injection for services.
- Return typed/domain errors where callers need to distinguish behavior.
- Keep database and external client types out of domain logic.
- Keep repositories for owned data stores only. Put third-party APIs and other services under outbound clients.
- Use repository aggregators for owned storage dependencies and outbound aggregators for external services when grouping is useful.
- Use centralized code/error packages with mappings for HTTP, gRPC, and broker semantics.
- Avoid services importing transport packages. Avoid repositories importing handlers.
- Avoid global mutable dependencies except deliberate process-level infrastructure.
- Use named types for domain-sensitive primitives when they prevent mistakes, for example different ID types, duration units, token values, or secret values.
- Add constructors when validation is needed.
- Keep DTO/model definitions separate from service behavior when a package grows. Do not split tiny packages just to mirror a template.
- Use constants for domain rules, protocol values, retry limits, timeout values, and validation bounds. Keep constants near the package that owns the rule.
- Use table tests for behavior with clear input/output cases.
