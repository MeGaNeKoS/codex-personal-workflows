# HTTP/API Protocol

Use this branch for REST/HTTP API design, response shapes, authentication headers, timestamps, pagination, caching, rate limits, API discovery, versioning, or deprecation behavior.

Do not force REST for internal service-to-service traffic just because this reference exists. Follow the project protocol decision.

## Contracts

- Keep API contracts stable, explicit, and versioned when external clients depend on them.
- Keep transport DTOs separate from core domain objects when the transport shape is not the domain shape.
- Use consistent timestamp, pagination, filtering, sorting, and ID formats.
- Make deprecation behavior explicit before changing or removing public fields.
- Document exact emitted headers, auth requirements, error shapes, request bodies, response bodies, and status codes in OpenAPI or the project contract format.

## Problem Details

- Use RFC 9457 Problem Details for HTTP API errors. RFC 9457 obsoletes RFC 7807.
- Use `application/problem+json` for JSON problem responses.
- Normalize validation, authentication, permission, conflict, timeout, unavailable, and internal errors at the API boundary.
- Keep product-specific machine-readable `code` fields stable as extension members.
- Do not hand-build unrelated error envelopes in individual handlers.
- For deeper API error contract and RFC 9457 implementation patterns, load the `api` skill and its `references/problem-json/*` files.

## Timestamps

- Prefer UTC internally.
- For API strings, use RFC 3339-compatible timestamps by default because client support is universal.
- Use RFC 9557 IXDTF only when the API needs additional timestamp information such as timezone identifiers or extension tags, and verify client/library support.
- Accept RFC 3339 at boundaries unless the product explicitly requires IXDTF.

## Authentication

- Use RFC 6750 Bearer token syntax: `Authorization: Bearer <token>`.
- For JWT payloads, follow RFC 7519 claims. `exp`, `iat`, and similar NumericDate claims are seconds since epoch, not milliseconds.
- For OAuth 2.0 flows, use RFC 6749 architecture and current OAuth security best practice.
- Public OAuth clients should use PKCE and strict redirect URI validation.

## HTTP Semantics

- Use RFC 9110 semantics for methods and status codes.
- `GET`: retrieve resource, safe, idempotent.
- `POST`: create or trigger non-idempotent processing.
- `PUT`: replace entire resource, idempotent.
- `PATCH`: partial update. Use RFC 7396 JSON Merge Patch only when the API chooses merge patch semantics.
- `DELETE`: remove resource, idempotent from client semantics.
- Use status codes consistently: `200`, `201`, `204`, `400`, `401`, `403`, `404`, `409`, `429`, `500`, and `503`.
- Use `401 Unauthorized` for missing/invalid credentials and `403 Forbidden` for authenticated but not allowed.

## HTTP Versions

- HTTP/2 is RFC 9113. HTTP/3 is RFC 9114. HTTP/1.1 is RFC 9112; do not call HTTP/1.1 globally deprecated.
- A project may require HTTP/2+ for performance, multiplexing, or operational simplicity, but frame that as project policy.
- For public APIs, use a reverse proxy or gateway to negotiate HTTP/2 and HTTP/3 when appropriate.
- For internal service-to-service APIs, prefer the project protocol decision, such as gRPC over HTTP/2.

## Caching

- Use RFC 9111 for HTTP caching. RFC 9111 obsoletes RFC 7234.
- Use `Cache-Control`, `ETag`, `Last-Modified`, and conditional requests where useful.
- Avoid caching private data in shared caches.
- Common examples: `Cache-Control: public, max-age=3600`, `Cache-Control: private, max-age=300`, `Cache-Control: no-store`.

## Pagination And Links

- Use RFC 8288 Web Linking for `Link` headers. RFC 8288 obsoletes RFC 5988.
- Use link relations for pagination where it helps clients.
- Keep pagination rules explicit: cursor/page shape, sort stability, max page size, and filters.

## Discovery

- Use RFC 9727 `/.well-known/api-catalog` when API discovery is useful for clients, gateways, or tooling.
- Keep `.well-known` endpoints stable and documented.

## Multipart And JSON

- Use RFC 7578 `multipart/form-data` for file uploads.
- Validate content type, magic bytes where possible, size limits, storage destination, and malware scanning requirements.
- Use RFC 8259 JSON. Responses should be UTF-8 JSON with valid escaping and no comments.
- Use RFC 6570 URI template syntax in documentation and link descriptions when templates are needed.

## Browser Security

- Never use `Access-Control-Allow-Origin: *` with credentials.
- Prefer explicit CORS origins and expose only required methods and headers.
- Use RFC 6797 Strict Transport Security in production HTTPS deployments where appropriate.
- Only use HSTS `preload` when the domain owner has accepted the operational commitment.

## Rate Limits

- For exceeded limits, return `429 Too Many Requests` and `Retry-After` where useful.
- The `RateLimit-*` header family has changed across drafts and deployments. Verify the current project/API gateway standard before treating a specific syntax as mandatory.
- Prefer documenting the exact emitted rate-limit headers in the API contract.

## Versioning And Deprecation

- API versioning has no single RFC. Prefer an explicit project policy.
- Path versioning such as `/api/v1/users` is simple and operationally clear.
- For deprecation, use `Deprecation` header RFC 9745, `Sunset` header RFC 8594, and `Link` header relations RFC 8288 when useful.
- Use deprecation and sunset headers with migration links when removing or replacing APIs.

## RFC Keywords

- Use BCP 14 keywords from RFC 2119 and RFC 8174 only when intended as normative requirements: `MUST`, `MUST NOT`, `SHOULD`, `SHOULD NOT`, `MAY`, `REQUIRED`, `OPTIONAL`.
