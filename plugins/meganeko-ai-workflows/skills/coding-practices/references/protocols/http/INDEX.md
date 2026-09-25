# HTTP/API Protocol

Use this branch for REST/HTTP API design, response shapes, authentication headers, timestamps, pagination, caching, rate limits, API discovery, versioning, or deprecation behavior.

Do not force REST for internal service-to-service traffic just because this reference exists. Follow the project protocol decision.

## Routing

| Changed concern | Load |
| --- | --- |
| Request/response shape, DTO separation, timestamps, pagination, payload format, discovery, or API docs | [Contracts](./contracts.md) |
| Method choice, status code choice, or HTTP version behavior | [Semantics](./semantics.md) |
| Auth headers, bearer tokens, JWT claims, OAuth, CORS, or HSTS | [Auth And Security](./auth-and-security.md) |
| Cache headers, conditional requests, or rate limiting | [Caching And Limits](./caching-and-limits.md) |
| New API version, public field change, or endpoint removal | [Versioning](./versioning.md) |

## Gates

- Error responses use RFC 9457 Problem Details with `application/problem+json`. RFC 9457 obsoletes RFC 7807.
- Validation, authentication, permission, conflict, timeout, unavailable, and internal errors are normalized at the API boundary, not per handler.
- Product-specific machine-readable `code` fields stay stable as extension members.

For the problem body shape and per-language implementation, use the `api` skill. This branch owns HTTP mechanics; `api` owns the error contract.
