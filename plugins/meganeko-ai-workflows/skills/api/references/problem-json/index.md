# Problem JSON

Use RFC 9457 Problem Details for REST API errors.

Return all user-facing API errors as:

```http
Content-Type: application/problem+json
```

Use this baseline response shape:

```json
{
  "type": "urn:example:problem:request-invalid",
  "title": "Request invalid",
  "status": 400,
  "detail": "username must not be empty",
  "instance": "/problems/74b445d2-2c8d-4f43-8a84-55ecdbfd7c94",
  "code": "request_invalid",
  "request_id": "74b445d2-2c8d-4f43-8a84-55ecdbfd7c94",
  "errors": []
}
```

## Fields

- `type`: stable problem type URI, usually `urn:<product>:problem:<kebab-code>`.
- `title`: short human-readable title for the error category.
- `status`: HTTP status code.
- `detail`: instance-specific human-readable message.
- `instance`: URI identifying this specific error occurrence.
- `code`: stable machine-readable code, usually snake_case.
- `request_id`: request/error correlation ID.
- `errors`: field-level validation errors, empty array when not applicable.

## Model

Separate these concepts:

- Stable error category metadata.
- Runtime error instance/message.
- Optional field-level validation errors.
- HTTP boundary formatter.

Conceptually:

```text
Error category:
  code
  title
  HTTP status
  problem type

Base application error:
  category metadata
  runtime detail/message
  optional field errors

Problem details formatter:
  application error + request id -> RFC 9457 JSON
```

Do not let every handler manually construct arbitrary JSON. Handlers and services should raise or return typed application errors, then one HTTP boundary formatter should produce the RFC 9457 response.

## Common Error Categories

Define stable error categories for common API failures:

```text
request_invalid         -> 400
invalid_credentials     -> 401
authorization_required  -> 401
authorization_invalid   -> 401
permission_required     -> 403
not_found               -> 404
conflict                -> 409
protected_resource      -> 409
rate_limited            -> 429
storage_unavailable     -> 503
internal_error          -> 500
```

Prefer product-specific codes when they add useful meaning:

```text
invalid_audience
protected_system_resource
license_limit_exceeded
```

## Rules

- Keep `type` and `code` stable.
- Do not expose raw database errors, stack traces, tokens, secrets, or infrastructure details in `detail`.
- Log internal causes server-side.
- Generate or propagate a request ID for every problem response.
- Use `errors` for validation details.
- Keep one formatter per HTTP boundary.
- Do not manually assemble ad hoc JSON error bodies in individual handlers.
