# API Error Handling

**API:** Choose Pharmacy NextGen
**Spec:** OAS 3.0 — `https://localhost:7074/swagger/v1/swagger.json`

**Implementation:** CommunityCommon.Presentation.AspNet `GlobalExceptionHandlerMiddleware`

---

## Overview

All errors are returned using ASP.NET Core's `ProblemDetails` standard format. The global exception middleware catches unhandled exceptions, maps them to appropriate HTTP status codes, and returns a structured error response.

---

## Error Response Structure

All error responses use ASP.NET Core's `ProblemDetails` format:

```json
{
  "type": "EntityNotFoundException",
  "title": "EntityNotFoundException",
  "status": 404,
  "detail": "Patient with ID 3fa85f64-5717-4562-b3fc-2c963f66afa6 not found"
}
```

**Fields:**
- `type`: [string] — The exception type name (e.g. `ValidationException`, `EntityNotFoundException`)
- `title`: [string] — Human-readable title (usually the exception type name)
- `status`: [integer] — HTTP status code
- `detail`: [string] — Detailed error message

**Validation errors use `ValidationProblemDetails`:**
```json
{
  "type": "ValidationException",
  "title": "ValidationException",
  "status": 400,
  "detail": "One or more validation errors occurred.",
  "errors": {
    "searchTerm": ["Search term must be at least 2 characters"],
    "limit": ["Limit must be between 1 and 100"]
  }
}
```

**Fields (ValidationProblemDetails):**
- `errors`: [object] — Dictionary of field name → array of error messages
- All standard ProblemDetails fields as above

---

## Standard HTTP Status Codes

### 400 Bad Request

Returned when:
- Request validation fails (FluentValidation exceptions)
- Query parameters are invalid or out of range
- Required fields are missing

**Exceptions that map to 400:**
- `ValidationException` (FluentValidation)
- `StringIdLengthException`
- `EntityHasChildrenEntitiesException`

**Example:**
```
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "type": "ValidationException",
  "title": "ValidationException",
  "status": 400,
  "detail": "Limit must be between 1 and 100",
  "errors": {
    "limit": ["Limit must be between 1 and 100"]
  }
}
```

### 401 Unauthorized

Returned when authentication is missing, invalid, or expired.

**Trigger:** Missing or invalid `Authorization` header

**Example:**
```
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "type": "AuthenticationException",
  "title": "AuthenticationException",
  "status": 401,
  "detail": "Invalid or expired authorization token"
}
```

### 403 Forbidden

Returned when the authenticated user lacks permission to access the resource.

**Exceptions that map to 403:**
- `UnauthorizedAccessException` (System exception)

**Example:**
```
HTTP/1.1 403 Forbidden
Content-Type: application/json

{
  "type": "UnauthorizedAccessException",
  "title": "UnauthorizedAccessException",
  "status": 403,
  "detail": "User does not have permission to access this patient's data"
}
```

### 404 Not Found

Returned when a requested resource does not exist.

**Exceptions that map to 404:**
- `EntityNotFoundException<TEntity>` (generic)

**Example:**
```
HTTP/1.1 404 Not Found
Content-Type: application/json

{
  "type": "EntityNotFoundException",
  "title": "EntityNotFoundException",
  "status": 404,
  "detail": "Patient with ID 3fa85f64-5717-4562-b3fc-2c963f66afa6 not found"
}
```

### 409 Conflict

Returned when the request conflicts with the current state of the resource.

**Exceptions that map to 409:**
- `EntityModifiedException<TEntity>` — Resource was modified since last read
- `EntityAlreadyExistsException<TEntity>` — Resource already exists

**Example:**
```
HTTP/1.1 409 Conflict
Content-Type: application/json

{
  "type": "EntityModifiedException",
  "title": "EntityModifiedException",
  "status": 409,
  "detail": "Consultation has been modified since it was last read"
}
```

### 500 Internal Server Error

Returned when an unexpected error occurs on the server that isn't mapped to a specific status code.

**Triggers:**
- Unhandled exceptions
- Database errors (not mapped to a domain exception)
- Unexpected null references or logic errors

**In development builds only**, the `detail` field includes the exception message. In production, details are not leaked.

**Example (Development):**
```
HTTP/1.1 500 Internal Server Error
Content-Type: application/json

{
  "type": "NullReferenceException",
  "title": "NullReferenceException",
  "status": 500,
  "detail": "Object reference not set to an instance of an object"
}
```

**Example (Production):**
```
HTTP/1.1 500 Internal Server Error
Content-Type: application/json

{
  "type": "NullReferenceException",
  "title": "NullReferenceException",
  "status": 500
}
```

### 502 Bad Gateway

Returned when an upstream service (e.g. DHCW FHIR Terminology Server, DHCW CDR) is unreachable or returns an error.

**Triggers:**
- DHCW Terminology Server timeout or error
- DHCW CDR is unavailable
- NHS e-referral system is unreachable

---

## Domain Exceptions

Your domain layer uses typed exceptions that map to HTTP status codes. When writing handlers or service logic, throw these exceptions:

| Exception | HTTP Status | Use When |
|-----------|---|---|
| `EntityNotFoundException<TEntity>` | 404 | Resource doesn't exist (e.g. patient not found) |
| `EntityAlreadyExistsException<TEntity>` | 409 | Resource already exists (e.g. duplicate record) |
| `EntityModifiedException<TEntity>` | 409 | Resource was changed since last read (concurrency) |
| `UnauthorizedAccessException` | 403 | User lacks permission for the operation |
| `ValidationException` (FluentValidation) | 400 | Request data fails validation |
| `StringIdLengthException` | 400 | String ID exceeds max length |
| `EntityHasChildrenEntitiesException<T,TChild>` | 400 | Cannot delete entity that has children |

**Example from a handler:**
```csharp
if (patient == null)
{
    throw new EntityNotFoundException<Patient>($"Patient {patientId} not found");
}
```

---

## Acceptance Criteria for Error Scenarios

Every story with validation or error scenarios MUST specify:

1. **Exception type** — What domain exception or validation rule triggers the error
2. **HTTP status code** — The exact status code (400, 401, 403, 404, 409, 500)
3. **Response format** — Must follow `ProblemDetails` structure
4. **Error detail message** — Exact message that appears in `detail` field
5. **No data persisted** — Always include `And no data is persisted` in failure scenarios

**Template for acceptance criteria:**

```gherkin
Scenario: Validation failure
Given [precondition]
When [action]
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Search term must be at least 2 characters"
And the errors object contains: { "searchTerm": ["Search term must be at least 2 characters"] }
And no data is persisted
```

```gherkin
Scenario: Resource not found
Given a request for a patient that doesn't exist
When GET /api/v1/Patient/{patientId} is called
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message contains the patient ID that was not found
And no data is persisted
```

---

## How Exception Mapping Works

The `GlobalExceptionHandlerMiddleware` in CommunityCommon:

1. Catches all unhandled exceptions in the request pipeline
2. Applies exception converters (transforms one exception type to another if needed)
3. Looks up the exception type in `IExceptionTypesMapper`
4. If a mapping exists, uses the custom response handler and status code
5. If no mapping exists, returns `500 Internal Server Error`
6. Returns the response as JSON with `Content-Type: application/json`

**The middleware is configured in `Program.cs`:**
```csharp
app.UseMiddleware<GlobalExceptionHandlerMiddleware>();
```

---

## Content Type

All error responses include:
```
Content-Type: application/json
```

---

## Related Standards

- [API-PATH-CONVENTIONS.md](API-PATH-CONVENTIONS.md) — URL structure and naming
- [API-RESPONSE-PATTERNS.md](API-RESPONSE-PATTERNS.md) — Success response format
- [ACCEPTANCE-CRITERIA.md](../guides/ACCEPTANCE-CRITERIA.md) — How to write acceptance criteria
- **CommunityCommon Repository:** Error handling implementation
  - `GlobalExceptionHandlerMiddleware` — Middleware that catches exceptions
  - `IExceptionTypesMapper` — Maps exceptions to status codes and responses
  - Domain exceptions — `EntityNotFoundException`, `UnauthorizedAccessException`, etc.


