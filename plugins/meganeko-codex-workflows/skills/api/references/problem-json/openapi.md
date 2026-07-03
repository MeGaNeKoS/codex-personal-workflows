# OpenAPI

Document one reusable `Problem` schema and reference it from error responses.

```yaml
Problem:
  type: object
  description: RFC 9457 problem details with stable product extensions.
  required: [type, title, status, detail, instance, code, request_id, errors]
  properties:
    type:
      type: string
      format: uri
      example: urn:example:problem:request-invalid
    title:
      type: string
      example: Request invalid
    status:
      type: integer
      format: int32
      minimum: 100
      maximum: 599
      example: 400
    detail:
      type: string
      example: username must not be empty
    instance:
      type: string
      example: /problems/74b445d2-2c8d-4f43-8a84-55ecdbfd7c94
    code:
      type: string
      description: Stable machine-readable problem code.
      example: request_invalid
    request_id:
      type: string
      example: 74b445d2-2c8d-4f43-8a84-55ecdbfd7c94
    errors:
      type: array
      items:
        type: object
        required: [field, code, detail]
        properties:
          field:
            type: string
          code:
            type: string
          detail:
            type: string
```

Use this schema for `400`, `401`, `403`, `404`, `409`, `429`, `500`, and other error responses.

Always document the response media type as:

```yaml
application/problem+json
```
