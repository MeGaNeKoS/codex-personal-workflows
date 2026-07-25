# Rust

Rust has no class inheritance. Translate the TypeScript pattern into:

```text
ErrorCode constant or enum-like value
AppError instance
ProblemDetails formatter
IntoResponse / HTTP adapter
```

Each new error category should define one `ErrorCode` constant. Each runtime
error occurrence should instantiate `AppError` with an existing `ErrorCode`.
Do not create a new code value for every occurrence.

Preferred shape:

```rust
#[derive(Clone, Copy, Debug, Eq, Hash, PartialEq, Serialize)]
pub struct ErrorCode {
    pub name: &'static str,
    pub title: &'static str,
    pub status: u16,
    pub problem_type: &'static str,
}

#[derive(Clone, Debug, Error)]
#[error("{message}")]
pub struct AppError {
    code: ErrorCode,
    message: String,
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
        }
    }

    pub fn bad_request(message: impl Into<String>) -> Self {
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
}
```

Usage:

```rust
return Err(AppError::bad_request("username must not be empty"));
```

Summary:

```text
new error category   -> define one new ErrorCode constant
new error occurrence -> instantiate AppError with an existing ErrorCode
ergonomics           -> add constructors such as AppError::bad_request(...)
```

Problem formatter:

```rust
pub struct ProblemDetails {
    #[serde(rename = "type")]
    pub problem_type: String,
    pub title: String,
    pub status: u16,
    pub detail: String,
    pub instance: String,
    pub code: String,
    pub request_id: String,
    pub errors: Vec<ProblemFieldError>,
}
```

At the HTTP boundary, implement the framework response adapter once. For Axum, implement `IntoResponse` for `AppError`.

Do not model this as one enum variant per error if the goal is to follow the base-error pattern. Use stable code metadata plus runtime error instances.
