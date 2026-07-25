# Backend Errors And Responses

Use this reference when defining product errors or mapping service outcomes at a protocol boundary.

## Product Errors

- Keep product errors stable and centralized.
- Give errors a stable code, useful detail or message, and transport-independent category where the product contract requires them.
- Let services and use cases map dependency failures into product errors rather than leaking adapter-specific failures inward.

## Boundary Mapping

- Centralize response and error formatting at the boundary that owns the protocol.
- Map product errors into the protocol's standard representation, such as HTTP Problem Details, gRPC status, or broker dead-letter metadata.
- Do not hand-build unrelated error envelopes in individual handlers.
- Use standardized response envelopes when the product requires them.
- Keep wire-specific statuses, envelopes, headers, and error details exclusively in the applicable protocol reference and boundary adapter.
