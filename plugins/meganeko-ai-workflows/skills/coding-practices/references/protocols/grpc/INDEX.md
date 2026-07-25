# gRPC Protocol

Use this branch for gRPC contract, service, client, streaming, status-code, deadline, and compatibility decisions.

- Treat protobuf definitions as contracts with compatibility requirements.
- Keep field numbers stable and never reuse removed field numbers.
- Prefer explicit request and response messages over primitive-only RPC signatures.
- Define deadline, retry, idempotency, and streaming behavior deliberately.
- Map domain failures to stable gRPC status behavior at the transport boundary.
- Preserve metadata needed for auth, tenant, trace, and request correlation.
