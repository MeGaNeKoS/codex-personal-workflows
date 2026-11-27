# Go Language

Use this branch for Go code: error types, test seams, package layout, and domain-sensitive values. The lines about handlers, services, repositories, and outbound clients apply to backend services; a command line tool or a library skips them.

- Keep handlers thin and push orchestration into services/use cases.
- Use handlers as controllers: read dependencies from the handler's constructor fields, call services, and return typed outputs and errors. Request context carries request-scoped values (deadline, trace, authenticated caller), never services.
- Define interfaces at the consuming side when they represent real seams.
- Use interfaces for repositories and outbound clients when tests or replacement require it. Go cannot monkey-patch ordinary function calls cleanly, so explicit interfaces and constructor injection are practical test seams.
- Use constructor injection for services.
- A context has one package in each layer, and they share a name. Import each under an alias that names the layer, such as `billingservice` and `billingrepo`.
- Return typed/domain errors where callers need to distinguish behavior.
- Keep database and external client types out of domain logic.
- Keep repositories for owned data stores only. Put third-party APIs and other services under outbound clients.
- Use repository aggregators for owned storage dependencies and outbound aggregators for external services when grouping is useful.
- Keep each domain's errors in that domain's package, and one mapping per protocol (HTTP, gRPC, broker) at that protocol's boundary.
- Avoid services importing transport packages. Avoid repositories importing handlers.
- Avoid global mutable dependencies except deliberate process-level infrastructure.
- Use named types for domain-sensitive primitives when they prevent mistakes, for example different ID types, duration units, token values, or secret values.
- Add constructors when validation is needed.
- Keep DTO and model definitions in their own file once code outside the package uses them. Do not split tiny packages just to mirror a template.
- Use constants for domain rules, protocol values, retry limits, timeout values, and validation bounds. Keep constants near the package that owns the rule.
- Use table tests for behavior with clear input/output cases.

```go
// Wrong: both are strings, so a customer ID passes as an order ID.
func findOrder(orderID string) (Order, error)

// Right: passing a CustomerID where an OrderID is expected does not compile.
type OrderID string
type CustomerID string

func findOrder(orderID OrderID) (Order, error)
```
