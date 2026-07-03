# Python

Python can follow the TypeScript class-inheritance model closely.

## Base Error

```python
from dataclasses import dataclass, field


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
            "errors": [error.__dict__ for error in self.errors],
        }
```

## Concrete Errors

```python
class RequestInvalidError(BaseError):
    code = "request_invalid"
    title = "Request invalid"
    status = 400
    type = "urn:example:problem:request-invalid"

    def __init__(self, detail: str = "request is invalid", errors=None):
        super().__init__(detail, errors)


class InternalError(BaseError):
    code = "internal_error"
    title = "Internal error"
    status = 500
    type = "urn:example:problem:internal-error"

    def __init__(self, detail: str = "internal server error"):
        super().__init__(detail)
```

At the HTTP boundary, catch `BaseError`, return `error.format(request_id)` with `application/problem+json`, and convert unknown exceptions to `InternalError` after logging internally.
