# TypeScript

Use class inheritance. The concrete error classes define their own code metadata.

## Base Error

```ts
export type ProblemFieldError = {
  field: string
  code: string
  detail: string
}

export type ProblemDetails = {
  type: string
  title: string
  status: number
  detail: string
  instance: string
  code: string
  request_id: string
  errors: ProblemFieldError[]
}

export abstract class BaseError extends Error {
  abstract readonly code: string
  abstract readonly title: string
  abstract readonly status: number
  abstract readonly type: string

  readonly errors: ProblemFieldError[]

  constructor(detail: string, errors: ProblemFieldError[] = []) {
    super(detail)
    this.name = new.target.name
    this.errors = errors
  }

  format(requestId: string): ProblemDetails {
    return {
      type: this.type,
      title: this.title,
      status: this.status,
      detail: this.message,
      instance: `/problems/${requestId}`,
      code: this.code,
      request_id: requestId,
      errors: this.errors,
    }
  }
}
```

## Concrete Errors

```ts
export class RequestInvalidError extends BaseError {
  readonly code = "request_invalid"
  readonly title = "Request invalid"
  readonly status = 400
  readonly type = "urn:example:problem:request-invalid"

  constructor(detail = "request is invalid", errors: ProblemFieldError[] = []) {
    super(detail, errors)
  }
}

export class AuthorizationRequiredError extends BaseError {
  readonly code = "authorization_required"
  readonly title = "Authorization required"
  readonly status = 401
  readonly type = "urn:example:problem:authorization-required"

  constructor(detail = "missing Authorization header") {
    super(detail)
  }
}

export class InternalError extends BaseError {
  readonly code = "internal_error"
  readonly title = "Internal error"
  readonly status = 500
  readonly type = "urn:example:problem:internal-error"

  constructor(detail = "internal server error") {
    super(detail)
  }
}
```

## HTTP Boundary

At the HTTP boundary, catch `BaseError`, call `.format(requestId)`, and return `application/problem+json`.

Unknown errors should be logged internally and returned as `InternalError` without leaking sensitive details.

```ts
try {
  return await handler(request)
} catch (error) {
  if (error instanceof BaseError) {
    return json(error.format(requestId), {
      status: error.status,
      headers: { "Content-Type": "application/problem+json" },
    })
  }

  logger.error(error)
  const internal = new InternalError()
  return json(internal.format(requestId), {
    status: internal.status,
    headers: { "Content-Type": "application/problem+json" },
  })
}
```
