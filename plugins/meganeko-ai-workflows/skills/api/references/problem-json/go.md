# Go

Go has no inheritance. Translate the TypeScript pattern into composition:

```text
ProblemCode struct
AppError struct
ProblemDetails formatter
HTTP middleware/adapter
```

Each new error category should define one `ProblemCode` value. Each runtime
error occurrence should instantiate `AppError` with an existing `ProblemCode`.
Do not create a new code value for every occurrence.

Starter shape:

```go
type ProblemCode struct {
    Code string
    Title string
    Status int
    Type string
}

type FieldError struct {
    Field string `json:"field"`
    Code string `json:"code"`
    Detail string `json:"detail"`
}

type AppError struct {
    ProblemCode ProblemCode
    Detail string
    Errors []FieldError
}

func (e *AppError) Error() string {
    return e.Detail
}
```

## Error Categories

Define one package-level value per stable error category:

```go
var RequestInvalid = ProblemCode{
    Code: "request_invalid",
    Title: "Request invalid",
    Status: http.StatusBadRequest,
    Type: "urn:example:problem:request-invalid",
}

var AuthorizationRequired = ProblemCode{
    Code: "authorization_required",
    Title: "Authorization required",
    Status: http.StatusUnauthorized,
    Type: "urn:example:problem:authorization-required",
}

var InternalErrorCode = ProblemCode{
    Code: "internal_error",
    Title: "Internal error",
    Status: http.StatusInternalServerError,
    Type: "urn:example:problem:internal-error",
}
```

Use constructors for runtime error instances:

```go
func NewAppError(code ProblemCode, detail string, errors ...FieldError) *AppError {
    return &AppError{
        ProblemCode: code,
        Detail: detail,
        Errors: errors,
    }
}

func NewRequestInvalid(detail string, errors ...FieldError) *AppError {
    return NewAppError(RequestInvalid, detail, errors...)
}

func NewAuthorizationRequired() *AppError {
    return NewAppError(AuthorizationRequired, "missing Authorization header")
}

func NewInternalError() *AppError {
    return NewAppError(InternalErrorCode, "internal server error")
}
```

Usage:

```go
return NewRequestInvalid("username must not be empty")
```

Summary:

```text
new error category   -> define one new ProblemCode value
new error occurrence -> instantiate AppError with an existing ProblemCode
ergonomics           -> add constructor helpers such as NewRequestInvalid(...)
```

Formatter:

```go
type ProblemDetails struct {
    Type string `json:"type"`
    Title string `json:"title"`
    Status int `json:"status"`
    Detail string `json:"detail"`
    Instance string `json:"instance"`
    Code string `json:"code"`
    RequestID string `json:"request_id"`
    Errors []FieldError `json:"errors"`
}

func (e *AppError) Format(requestID string) ProblemDetails {
    return ProblemDetails{
        Type: e.ProblemCode.Type,
        Title: e.ProblemCode.Title,
        Status: e.ProblemCode.Status,
        Detail: e.Detail,
        Instance: "/problems/" + requestID,
        Code: e.ProblemCode.Code,
        RequestID: requestID,
        Errors: e.Errors,
    }
}
```

At the HTTP boundary, use `errors.As` to detect `*AppError`; otherwise log the cause and return an internal error.
