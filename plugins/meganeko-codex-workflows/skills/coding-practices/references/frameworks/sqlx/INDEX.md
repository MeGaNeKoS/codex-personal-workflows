# SQLx Library

Use this branch for Rust database adapters implemented with SQLx.

- Keep SQLx types and row mapping inside infrastructure/repository adapters.
- Keep repository interfaces in the owning domain or service boundary.
- Validate and convert database rows into domain-safe types before returning from repositories.
- Use transactions explicitly where multiple writes must commit atomically.
- Keep query constants or query files discoverable near the repository that owns them.
- Do not leak connection pools into domain logic.
