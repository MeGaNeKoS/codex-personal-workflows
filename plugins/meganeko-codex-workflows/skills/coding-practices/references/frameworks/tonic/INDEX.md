# Tonic Framework

Use this branch for Rust gRPC services or clients implemented with Tonic.

- Keep generated protobuf types at the transport boundary.
- Convert transport messages into validated domain/request types before business logic.
- Map domain errors to gRPC status codes in one boundary layer.
- Preserve request IDs, auth context, tenant context, and trace context where the project supports them.
- Use streaming deliberately; document flow control, message size, cancellation, and retry behavior.
- Keep interceptors/middleware focused on cross-cutting concerns such as auth, tracing, deadlines, and rate limits.
