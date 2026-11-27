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
- `status`: HTTP status code. It equals the status line of the response.
- `detail`: instance-specific human-readable message.
- `instance`: URI identifying this specific error occurrence.
- `code`: stable machine-readable code, usually snake_case.
- `request_id`: request/error correlation ID.
- `errors`: field-level validation errors. Always an array: `[]` when there are none, never `null` and never absent.

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

All of this is HTTP boundary code, because a category carries an HTTP status:

- The boundary raises an application error itself for what it detects: a body that fails validation, missing or invalid credentials.
- A failure from inside a service arrives as a product error that knows nothing about HTTP. The boundary holds one mapping from each product error to a category.
- One formatter then produces the RFC 9457 response.

A service never constructs one of these types, and the category table sits with the HTTP adapter, not in a domain package.

## Common Error Categories

Define stable error categories for common API failures:

```text
request_invalid         -> 400
invalid_credentials     -> 401
authorization_required  -> 401
authorization_invalid   -> 401
permission_required     -> 403
not_found               -> 404
method_not_allowed      -> 405
conflict                -> 409
protected_resource      -> 409
rate_limited            -> 429
storage_unavailable     -> 503
internal_error          -> 500
```

Which status a failure gets is decided by the HTTP branch of `coding-practices`; this table records the result for the common cases. A status the table does not cover gets a category of its own; never answer it with a category that carries a different status.

Add a product-specific code when a client has to tell apart two failures that share a status, for example:

```text
invalid_audience
protected_system_resource
license_limit_exceeded
```

## The Boundary Formatter

The formatter is the only code that writes an error response. For every error it sets three things together:

1. the response status line, from the category's status;
2. the header `Content-Type: application/problem+json`;
3. the JSON body above.

On a `401` it also sets a `WWW-Authenticate` header carrying the challenge for the scheme in use, such as `Bearer` for bearer tokens. HTTP requires that header on every `401` (RFC 9110, section 15.5.2).

A framework raises HTTP errors of its own, for an unknown route, a method that is not allowed, or a body it refuses. Those leave as problem responses too, each mapped to the category with the same status.

Any other error that is not a typed application error is logged with its cause and answered as `internal_error`, with none of the cause in the body.

## Rules

- Keep `type` and `code` stable.
- Do not expose raw database errors, stack traces, tokens, secrets, or infrastructure details in `detail`.
- Log internal causes server-side.
- Generate or propagate a request ID for every problem response.
- Use `errors` for validation details.
- Keep one formatter per HTTP boundary.
- Do not manually assemble ad hoc JSON error bodies in individual handlers.
