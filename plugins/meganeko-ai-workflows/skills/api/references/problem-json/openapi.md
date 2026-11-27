# OpenAPI

Document one reusable `Problem` schema under `components/schemas` and reference it from every error response.

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

Use this schema for `400`, `401`, `403`, `404`, `409`, `429`, `500`, and every other error response.

Every error response is documented under the media type `application/problem+json`, never `application/json`. A `401` also documents its `WWW-Authenticate` header:

```yaml
responses:
  "401":
    description: The request carries no valid credentials.
    headers:
      WWW-Authenticate:
        description: The challenge for the authentication scheme in use.
        schema:
          type: string
    content:
      application/problem+json:
        schema:
          $ref: "#/components/schemas/Problem"
  "404":
    description: The resource does not exist.
    content:
      application/problem+json:
        schema:
          $ref: "#/components/schemas/Problem"
```
