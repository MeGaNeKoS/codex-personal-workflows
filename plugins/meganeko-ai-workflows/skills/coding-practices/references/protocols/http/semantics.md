# HTTP Semantics

Use when choosing a method, status code, or HTTP version behavior.

## Methods

Use RFC 9110 semantics.

| Method | Meaning | Safe | Idempotent |
| --- | --- | --- | --- |
| `GET` | retrieve resource | yes | yes |
| `POST` | create or trigger non-idempotent processing | no | no |
| `PUT` | replace entire resource | no | yes |
| `PATCH` | partial update | no | no |
| `DELETE` | remove resource | no | yes |

Use RFC 7396 JSON Merge Patch only when the API chooses merge patch semantics.

## Status Codes

Use these consistently: `200`, `201`, `204`, `400`, `401`, `403`, `404`, `409`, `429`, `500`, `503`.

The distinction that is most often wrong:

```text
401 Unauthorized  -> credentials missing, malformed, or invalid
                     "we do not know who you are"
403 Forbidden     -> authenticated successfully, not allowed to do this
                     "we know who you are, and no"
```

## Versions

- HTTP/2 is RFC 9113. HTTP/3 is RFC 9114. HTTP/1.1 is RFC 9112; do not call HTTP/1.1 globally deprecated.
- A project may require HTTP/2+ for performance, multiplexing, or operational simplicity, but frame that as project policy.
- For public APIs, use a reverse proxy or gateway to negotiate HTTP/2 and HTTP/3 when appropriate.
- For internal service-to-service APIs, prefer the project protocol decision, such as gRPC over HTTP/2.
