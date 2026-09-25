# Rust Language

Use this branch for Rust crates, async services, traits, ports/adapters, repositories, outbound clients, error types, newtypes, validation boundaries, tests, and backend code.

## Routing

| Changed concern | Load |
| --- | --- |
| Trait seams, generic versus dynamic dispatch, service construction, module or `lib.rs` shape | [Service Shape](./service-shape.md) |
| Newtypes, domain values, boundary parsing, validated constructors, error types, test fakes | [Values And Boundaries](./values-and-boundaries.md) |
| Async runtime, task structure, cancellation, or concurrency | [Async](./async.md) |

## Gates

- Traits exist for real external or test seams, not for every struct.
- Domain modules stay free of web framework, RPC framework, database client, vendor SDK, and tracing types.
- Untrusted input is validated at the boundary through `TryFrom`, `FromStr`, serde validation, or an explicit constructor.
- Unchecked conversion helpers never substitute for that validation.
