# HTTP Contracts

Use when defining or changing a public request/response shape, timestamp format, pagination, or API documentation.

## Stability

- Keep API contracts stable, explicit, and versioned when external clients depend on them.
- Keep transport DTOs separate from core domain objects when the transport shape is not the domain shape.
- Use consistent timestamp, pagination, filtering, sorting, and ID formats.
- Make deprecation behavior explicit before changing or removing public fields.
- Document exact emitted headers, auth requirements, error shapes, request bodies, response bodies, and status codes in OpenAPI or the project contract format.

## Timestamps

- Prefer UTC internally.
- For API strings, use RFC 3339-compatible timestamps by default because client support is universal.
- Use RFC 9557 IXDTF only when the API needs additional timestamp information such as timezone identifiers or extension tags, and verify client/library support.
- Accept RFC 3339 at boundaries unless the product explicitly requires IXDTF.

## Pagination And Links

- Use RFC 8288 Web Linking for `Link` headers. RFC 8288 obsoletes RFC 5988.
- Use link relations for pagination where it helps clients.
- Keep pagination rules explicit: cursor/page shape, sort stability, max page size, and filters.

## Payload Formats

- Use RFC 8259 JSON. Responses should be UTF-8 JSON with valid escaping and no comments.
- Use RFC 7578 `multipart/form-data` for file uploads. Validate content type, magic bytes where possible, size limits, storage destination, and malware scanning requirements.
- Use RFC 6570 URI template syntax in documentation and link descriptions when templates are needed.

## Discovery

- Use RFC 9727 `/.well-known/api-catalog` when API discovery is useful for clients, gateways, or tooling.
- Keep `.well-known` endpoints stable and documented.

## RFC Keywords

Use BCP 14 keywords from RFC 2119 and RFC 8174 only when intended as normative requirements: `MUST`, `MUST NOT`, `SHOULD`, `SHOULD NOT`, `MAY`, `REQUIRED`, `OPTIONAL`.
