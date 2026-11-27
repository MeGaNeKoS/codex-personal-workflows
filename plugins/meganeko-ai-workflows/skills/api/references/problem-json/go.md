# Go

Go has no inheritance. Translate the [TypeScript pattern](./typescript.md) into composition:

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
    Code   string
    Title  string
    Status int
    Type   string
}

type FieldError struct {
    Field  string `json:"field"`
    Code   string `json:"code"`
    Detail string `json:"detail"`
}

type AppError struct {
    ProblemCode ProblemCode
    Detail      string
    FieldErrors []FieldError
}

func (e *AppError) Error() string {
    return e.Detail
}
```

## Error Categories

Define one package-level value per stable error category:

```go
var RequestInvalid = ProblemCode{
    Code:   "request_invalid",
    Title:  "Request invalid",
    Status: http.StatusBadRequest,
    Type:   "urn:example:problem:request-invalid",
}

var AuthorizationRequired = ProblemCode{
    Code:   "authorization_required",
    Title:  "Authorization required",
    Status: http.StatusUnauthorized,
    Type:   "urn:example:problem:authorization-required",
}

var InternalError = ProblemCode{
    Code:   "internal_error",
    Title:  "Internal error",
    Status: http.StatusInternalServerError,
    Type:   "urn:example:problem:internal-error",
}
```

Use constructors for runtime error instances:

```go
func NewAppError(code ProblemCode, detail string, fieldErrors ...FieldError) *AppError {
    return &AppError{
        ProblemCode: code,
        Detail:      detail,
        FieldErrors: fieldErrors,
    }
}

func NewRequestInvalid(detail string, fieldErrors ...FieldError) *AppError {
    return NewAppError(RequestInvalid, detail, fieldErrors...)
}

func NewAuthorizationRequired() *AppError {
    return NewAppError(AuthorizationRequired, "missing Authorization header")
}

func NewInternalError() *AppError {
    return NewAppError(InternalError, "internal server error")
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

## Formatter

A nil slice encodes as JSON `null`, and the contract says `errors` is always an array, so the formatter replaces a nil slice with an empty one.

```go
type ProblemDetails struct {
    Type      string       `json:"type"`
    Title     string       `json:"title"`
    Status    int          `json:"status"`
    Detail    string       `json:"detail"`
    Instance  string       `json:"instance"`
    Code      string       `json:"code"`
    RequestID string       `json:"request_id"`
    Errors    []FieldError `json:"errors"`
}

func (e *AppError) Format(requestID string) ProblemDetails {
    return ProblemDetails{
        Type:      e.ProblemCode.Type,
        Title:     e.ProblemCode.Title,
        Status:    e.ProblemCode.Status,
        Detail:    e.Detail,
        Instance:  "/problems/" + requestID,
        Code:      e.ProblemCode.Code,
        RequestID: requestID,
        Errors:    fieldErrorsOrEmpty(e.FieldErrors),
    }
}

func fieldErrorsOrEmpty(fieldErrors []FieldError) []FieldError {
    if fieldErrors == nil {
        return []FieldError{}
    }
    return fieldErrors
}
```

## HTTP Boundary

One function writes every error response. It sets the status line, the content type, and the body together, and adds the challenge header on a 401.

```go
const problemContentType = "application/problem+json"

func WriteProblem(w http.ResponseWriter, requestID string, err error) {
    appError := asAppError(err, requestID)
    if appError.ProblemCode.Status == http.StatusUnauthorized {
        w.Header().Set("WWW-Authenticate", "Bearer")
    }
    w.Header().Set("Content-Type", problemContentType)
    w.WriteHeader(appError.ProblemCode.Status)
    if encodeErr := json.NewEncoder(w).Encode(appError.Format(requestID)); encodeErr != nil {
        slog.Error("problem response not written", "request_id", requestID, "error", encodeErr)
    }
}

func asAppError(err error, requestID string) *AppError {
    var appError *AppError
    if errors.As(err, &appError) {
        return appError
    }
    slog.Error("unhandled error", "request_id", requestID, "error", err)
    return NewInternalError()
}
```

`WriteProblem` treats any error that is not an `*AppError` as internal. A product error from a service is therefore mapped before the call, by one function beside `WriteProblem` that holds the whole mapping: `WriteProblem(w, requestID, toAppError(err))`. `toAppError` uses `errors.As` to turn each product error into `NewAppError` with its category, and returns every other error unchanged. Without that step a product error such as "order not found" is answered as a 500.

The blocks on this page need the imports `encoding/json`, `errors`, `log/slog`, and `net/http`.
