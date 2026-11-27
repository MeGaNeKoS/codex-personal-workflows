# Python

Python can follow the [TypeScript class-inheritance model](./typescript.md) closely.

## Base Error

```python
from dataclasses import asdict, dataclass


@dataclass(frozen=True)
class ProblemFieldError:
    field: str
    code: str
    detail: str


class BaseError(Exception):
    code: str
    title: str
    status: int
    type: str

    def __init__(self, detail: str, errors: list[ProblemFieldError] | None = None):
        super().__init__(detail)
        self.detail = detail
        self.errors = errors or []

    def format(self, request_id: str) -> dict:
        return {
            "type": self.type,
            "title": self.title,
            "status": self.status,
            "detail": self.detail,
            "instance": f"/problems/{request_id}",
            "code": self.code,
            "request_id": request_id,
            "errors": [asdict(error) for error in self.errors],
        }
```

## Concrete Errors

```python
class RequestInvalidError(BaseError):
    code = "request_invalid"
    title = "Request invalid"
    status = 400
    type = "urn:example:problem:request-invalid"

    def __init__(self, detail: str = "request is invalid", errors: list[ProblemFieldError] | None = None):
        super().__init__(detail, errors)


class AuthorizationRequiredError(BaseError):
    code = "authorization_required"
    title = "Authorization required"
    status = 401
    type = "urn:example:problem:authorization-required"

    def __init__(self, detail: str = "missing Authorization header"):
        super().__init__(detail)


class InternalError(BaseError):
    code = "internal_error"
    title = "Internal error"
    status = 500
    type = "urn:example:problem:internal-error"

    def __init__(self, detail: str = "internal server error"):
        super().__init__(detail)
```

## HTTP Boundary

One function decides the status line, the headers, and the body of every error response. The framework's exception handler calls it and hands the three values to its own response type.

```python
import logging

PROBLEM_CONTENT_TYPE = "application/problem+json"
UNAUTHORIZED_STATUS = 401

logger = logging.getLogger(__name__)


def problem_response(error: Exception, request_id: str) -> tuple[int, dict[str, str], dict]:
    problem = as_base_error(error, request_id)
    headers = {"Content-Type": PROBLEM_CONTENT_TYPE}
    if problem.status == UNAUTHORIZED_STATUS:
        headers["WWW-Authenticate"] = "Bearer"
    return problem.status, headers, problem.format(request_id)


def as_base_error(error: Exception, request_id: str) -> BaseError:
    if isinstance(error, BaseError):
        return error
    logger.error("unhandled error, request_id=%s", request_id, exc_info=error)
    return InternalError()
```

Register `problem_response` as the framework's handler for `BaseError` and for exceptions nothing else handles, so no route returns an error body of its own.

`as_base_error` treats anything that is not a `BaseError` as internal, so two kinds of error are converted before they reach it, by one function beside it that holds the whole mapping:

- A product error raised by a service becomes the `BaseError` subclass for its category.
- An HTTP error the framework raises itself, for an unknown route or a method that is not allowed, becomes the `BaseError` subclass with the same status. A handler registered for every exception receives those too.

Without that step an "order not found" from a service, or a missing route, is answered as a 500.
