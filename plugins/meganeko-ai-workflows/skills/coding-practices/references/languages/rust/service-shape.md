# Rust Service Shape

Use when choosing between generic and dynamic dispatch for a service, or placing trait seams.

## Traits As Seams

- Use traits for real external or test seams: repositories, outbound clients, object storage, authorization clients, license verifiers, clocks, and deterministic ID generators.
- Do not create a trait for every struct. Prefer concrete types for deterministic internal logic.
- Treat dependency injection as a testing and replacement tool, not a universal rule.

## Dispatch Choice

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

Use dynamic dispatch when runtime composition, heterogeneous implementations, or simpler type signatures justify it:

```rust
pub struct NodeService {
    repository: Arc<dyn NodeRepository + Send + Sync>,
    authorization: Arc<dyn AuthorizationClient + Send + Sync>,
}
```

Generic parameters propagate into every caller's type signature. When that propagation starts appearing in unrelated modules, the readability argument has flipped and `Arc<dyn _>` is the better trade.

## Module Shape

- `lib.rs` exposes intentional APIs, so new implementation does not go there. An existing large `lib.rs` is left alone until the user asks for a split.
- Separate type-heavy modules from behavior when a domain grows. Common names are `model`, `command`, `service`, `ports`, `adapters`, `error`, and `constants`, but follow local project style.
- Keep domain modules free of web framework types, RPC framework types, database clients, vendor SDK clients, and tracing setup.
- An inline `#[cfg(test)] mod tests` is fine while the module covers one behavior area and stays under 1000 lines. Either a second behavior area or passing 1000 lines moves the tests to sibling files or `tests/`, split by behavior under test with positive and negative cases apart.
- Moving a function to its owner along with its tests also means updating the `mod` list and any visibility the move requires. A re-export shim left behind in the old file is not acceptable: it is a barrel that hides ownership, which [TypeScript Module Shape](../typescript/module-shape.md) forbids for the same reason.
