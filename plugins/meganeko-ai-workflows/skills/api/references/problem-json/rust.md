# Rust

Rust has no class inheritance. Translate the [TypeScript pattern](./typescript.md) into:

```text
ErrorCode constant
AppError instance
ProblemDetails formatter
HTTP response adapter
```

Each new error category should define one `ErrorCode` constant. Each runtime
error occurrence should instantiate `AppError` with an existing `ErrorCode`.
Do not create a new code value for every occurrence.

The blocks on this page use the `serde` crate with its `derive` feature and the `thiserror` crate.

Preferred shape:

```rust
use serde::Serialize;
use thiserror::Error;

#[derive(Clone, Copy, Debug, Eq, Hash, PartialEq)]
pub struct ErrorCode {
    pub name: &'static str,
    pub title: &'static str,
    pub status: u16,
    pub problem_type: &'static str,
}

#[derive(Clone, Debug, Serialize)]
pub struct ProblemFieldError {
    pub field: String,
    pub code: String,
    pub detail: String,
}

#[derive(Clone, Debug, Error)]
#[error("{message}")]
pub struct AppError {
    code: ErrorCode,
    message: String,
    field_errors: Vec<ProblemFieldError>,
}
```

## Error Categories

Define one constant per stable error category:

```rust
pub mod error_codes {
    use super::ErrorCode;

    pub const REQUEST_INVALID: ErrorCode = ErrorCode {
        name: "request_invalid",
        title: "Request invalid",
        status: 400,
        problem_type: "urn:example:problem:request-invalid",
    };

    pub const AUTHORIZATION_REQUIRED: ErrorCode = ErrorCode {
        name: "authorization_required",
        title: "Authorization required",
        status: 401,
        problem_type: "urn:example:problem:authorization-required",
    };

    pub const INTERNAL_ERROR: ErrorCode = ErrorCode {
        name: "internal_error",
        title: "Internal error",
        status: 500,
        problem_type: "urn:example:problem:internal-error",
    };
}
```

Use constructors for runtime error instances:

```rust
impl AppError {
    pub fn new(code: ErrorCode, message: impl Into<String>) -> Self {
        Self {
            code,
            message: message.into(),
            field_errors: Vec::new(),
        }
    }

    pub fn with_field_errors(mut self, field_errors: Vec<ProblemFieldError>) -> Self {
        self.field_errors = field_errors;
        self
    }

    pub fn request_invalid(message: impl Into<String>) -> Self {
        Self::new(error_codes::REQUEST_INVALID, message)
    }

    pub fn authorization_required() -> Self {
        Self::new(
            error_codes::AUTHORIZATION_REQUIRED,
            "missing Authorization header",
        )
    }

    pub fn internal_error() -> Self {
        Self::new(error_codes::INTERNAL_ERROR, "internal server error")
    }

    pub fn status(&self) -> u16 {
        self.code.status
    }
}
```

Usage:

```rust
return Err(AppError::request_invalid("username must not be empty"));
```

Summary:

```text
new error category   -> define one new ErrorCode constant
new error occurrence -> instantiate AppError with an existing ErrorCode
ergonomics           -> add constructors such as AppError::request_invalid(...)
```

## Formatter

```rust
#[derive(Debug, Serialize)]
pub struct ProblemDetails {
    #[serde(rename = "type")]
    pub problem_type: &'static str,
    pub title: &'static str,
    pub status: u16,
    pub detail: String,
    pub instance: String,
    pub code: &'static str,
    pub request_id: String,
    pub errors: Vec<ProblemFieldError>,
}

impl AppError {
    pub fn to_problem_details(&self, request_id: &str) -> ProblemDetails {
        ProblemDetails {
            problem_type: self.code.problem_type,
            title: self.code.title,
            status: self.code.status,
            detail: self.message.clone(),
            instance: format!("/problems/{request_id}"),
            code: self.code.name,
            request_id: request_id.to_owned(),
            errors: self.field_errors.clone(),
        }
    }
}
```

An empty `Vec` serializes as `[]`, so `errors` is always an array.

## HTTP Boundary

Implement the framework's response adapter once. It sets the status line from `AppError::status`, the header `Content-Type: application/problem+json`, a `WWW-Authenticate` header when the status is 401, and the body from `to_problem_details`.

A product error from a service becomes an `AppError` in one place at the boundary: a `From` implementation or a mapping function that names the category for each product error. A product error that reaches the adapter unmapped has no category, and answering it as `internal_error` hides a 404 behind a 500.

The adapter needs the request ID. In Axum, `IntoResponse` does not receive the request, so the ID has to reach the adapter another way. Use the way the project already carries request-scoped values, and ask when it has none.

Do not model this as one enum variant per error. A variant per error puts the category's metadata in `match` arms spread over the code, and every new category then edits each of them. One constant per category keeps code, title, status, and type in one place.
