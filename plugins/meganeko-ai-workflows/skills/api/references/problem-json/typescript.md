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

One function builds every error response. It sets the status line, the content type, and the body together, and adds the challenge header on a 401. `json` stands for the framework's JSON response helper.

```ts
const PROBLEM_CONTENT_TYPE = "application/problem+json"
const UNAUTHORIZED_STATUS = 401

function problemResponse(error: BaseError, requestId: string): Response {
  const headers: Record<string, string> = { "Content-Type": PROBLEM_CONTENT_TYPE }
  if (error.status === UNAUTHORIZED_STATUS) headers["WWW-Authenticate"] = "Bearer"
  return json(error.format(requestId), { status: error.status, headers })
}
```

The boundary catches everything. A `BaseError` is formatted as it is. Anything else is logged internally and answered as `InternalError`, so no sensitive detail leaks.

A product error thrown by a service is not a `BaseError`. One function beside this code holds the whole mapping from product error to `BaseError` subclass, and the catch block applies it before the `instanceof` test. Without that step a product error such as "order not found" is answered as a 500.

```ts
try {
  return await handler(request)
} catch (error) {
  if (error instanceof BaseError) return problemResponse(error, requestId)

  logger.error(error)
  return problemResponse(new InternalError(), requestId)
}
```
