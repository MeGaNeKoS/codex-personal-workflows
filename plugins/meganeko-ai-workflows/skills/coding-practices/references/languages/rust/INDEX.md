# Rust Language

Use this branch for Rust crates, async services, traits, ports/adapters, repositories, outbound clients, error types, newtypes, validation boundaries, tests, and backend code.

## Rules

- Use traits for real external or test seams: repositories, outbound clients, object storage, authorization clients, license verifiers, clocks, and deterministic ID generators.
- Do not create a trait for every struct. Prefer concrete types for deterministic internal logic.
- Prefer generic services when static dispatch stays readable.
- Use `Arc<dyn Trait + Send + Sync>` when runtime composition, heterogeneous implementations, or simpler type signatures justify dynamic dispatch.
- Keep domain modules free of web framework types, RPC framework types, database clients, vendor SDK clients, and tracing setup.
- Use newtypes for IDs and domain-safe values.
- Prefer validated constructors for values created from untrusted input.
- Use the project error type for fallible behavior.
- Split large `lib.rs` files; `lib.rs` should expose intentional APIs, not contain all implementation.
- Prefer hand-written fakes in tests. Use mock crates sparingly when a fake would be noisy.
- Keep parsing and validation at boundaries with `TryFrom`, `FromStr`, serde validation, or explicit constructors.
- Separate type-heavy modules from behavior when a domain grows. Common Rust module names are `model`, `command`, `service`, `ports`, `adapters`, `error`, and `constants`, but follow the local project style.
- Avoid unchecked conversion helpers that hide validation.

## Service Shape

Prefer generic services when static dispatch stays readable:

```rust
pub struct NodeService<R, A> {
    repository: R,
    authorization: A,
}

impl<R, A> NodeService<R, A>
where
    R: NodeRepository,
    A: AuthorizationClient,
{
    pub async fn get_node(&self, request: GetNodeRequest) -> ProductResult<RegisteredNode> {
        // orchestration only
    }
}
```

Use dynamic dispatch when runtime composition or clearer type signatures justify it:

```rust
pub struct NodeService {
    repository: Arc<dyn NodeRepository + Send + Sync>,
    authorization: Arc<dyn AuthorizationClient + Send + Sync>,
}
```

For async runtime or task structure, read `async.md`.
