# API User Story: GET /api/v1/users/{userId} - Retrieve User Profile Information

## Story Type: API | Feature: TBD | Effort: 13 hours

---

## Description

### Story Card
**As a** Frontend Application  
**I want** to retrieve user profile information from EntraID  
**So that** I can populate the user profile page with accurate identity and role attributes

### Background
The system needs to retrieve user profile data (given name, surname, job title, email) from EntraID to display in the user profile interface. This endpoint acts as a wrapper around the Microsoft Graph API, providing a standardized, API-versioned interface for internal services.

### Objective
Deliver a secure, performant GET endpoint that retrieves user profile information from EntraID with proper authentication, authorization, validation, and error handling.

---

## Endpoint Details

**HTTP Method:** GET  
**Endpoint:** `/api/v1/users/{userId}`  
**Full URL:** `https://api.example.com/api/v1/users/{userId}`

### Path Parameters
- `{userId}`: [string, UUID, required] - User ID in UUID format

### Query Parameters
- `includeGphc`: [boolean, optional, default: true] - Include GPHC registration number in response

---

## Request

### Request Headers
```
Authorization: Bearer {accessToken}
Content-Type: application/json
Accept: application/json
```

### Request Example
```
GET /api/v1/users/3fa85f64-5717-4562-b3fc-2c963f66afa6?includeGphc=true
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

---

## Response

### Success Response (200 OK)
```json
{
  "userId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "givenName": "John",
  "surname": "Smith",
  "jobTitle": "Clinical Pharmacist",
  "mail": "john.smith@pharmacy.wales.nhs.uk",
  "gphcNumber": "2156789",
  "retrievedDateTime": "2026-09-29T12:15:00Z"
}
```

**Response Fields:**
- `userId`: [string] — User ID (UUID format)
- `givenName`: [string] — User's given name from EntraID
- `surname`: [string] — User's surname from EntraID
- `jobTitle`: [string] — User's job title from EntraID
- `mail`: [string] — User's email address from EntraID
- `gphcNumber`: [string] — User's GPHC (General Pharmaceutical Council) registration number from EntraID extension
- `retrievedDateTime`: [ISO 8601 datetime] — Timestamp when data was retrieved

---

## Error Responses

### Invalid User ID Format (400 Bad Request)
```json
{
  "type": "ValidationException",
  "title": "ValidationException",
  "status": 400,
  "detail": "User ID must be a valid UUID format",
  "errors": {
    "userId": ["User ID must be a valid UUID format"]
  }
}
```

### Unauthorized (401 Unauthorized)
```json
{
  "type": "AuthenticationException",
  "title": "AuthenticationException",
  "status": 401,
  "detail": "Invalid or expired authorization token"
}
```

### Forbidden (403 Forbidden)
```json
{
  "type": "UnauthorizedAccessException",
  "title": "UnauthorizedAccessException",
  "status": 403,
  "detail": "User does not have permission to access profile information for this user ID"
}
```

### User Not Found (404 Not Found)
```json
{
  "type": "EntityNotFoundException",
  "title": "EntityNotFoundException",
  "status": 404,
  "detail": "User with ID 3fa85f64-5717-4562-b3fc-2c963f66afa6 not found in EntraID"
}
```

### EntraID Service Unavailable (502 Bad Gateway)
```json
{
  "type": "ServiceUnavailableException",
  "title": "ServiceUnavailableException",
  "status": 502,
  "detail": "Microsoft Graph API is temporarily unavailable. Please try again later."
}
```

### Server Error (500 Internal Server Error)
```json
{
  "type": "Exception",
  "title": "Exception",
  "status": 500,
  "detail": "An unexpected error occurred while retrieving user profile information"
}
```

---

## Error Handling Reference

All errors follow ASP.NET Core `ProblemDetails` format as per [standards/API-ERROR-HANDLING.md](../standards/API-ERROR-HANDLING.md).

| Status Code | Error Type | Scenario |
|---|---|---|
| 400 | ValidationException | Invalid UUID format |
| 401 | AuthenticationException | Missing/expired token |
| 403 | UnauthorizedAccessException | Insufficient permissions |
| 404 | EntityNotFoundException | User not found in EntraID |
| 502 | ServiceUnavailableException | Microsoft Graph API unavailable |
| 500 | Exception | Unexpected server error |

---

## Authentication & Authorization

### Authentication
- **Method:** OAuth 2.0 Bearer Token
- **Header:** `Authorization: Bearer {accessToken}`
- **Token Source:** Azure Entra ID
- **Required:** Yes — all requests must include a valid access token

### Authorization
- **Required Roles:** Any authenticated user can retrieve their own profile
- **Cross-User Access:** Users can only retrieve their own profile data (userId must match authenticated user)
- **Admin Exception:** System administrators may retrieve other users' profiles with appropriate role
- **Claim Validation:** `oid` claim in token must match requested `userId` or user must have admin role

---

## Performance Requirements

| Metric | Target |
|---|---|
| Response Time (p95) | < 200ms |
| Response Time (p99) | < 500ms |
| Rate Limit | 1,000 requests/min per user |
| Cache Duration | 5 minutes (300 seconds) for same user |

### Caching Strategy
- Responses for the same user are cached for 5 minutes
- `Cache-Control: private, max-age=300`
- Cache is invalidated when user profile is updated in EntraID

---

## Acceptance Criteria

**Total Scenarios: 7**

### Scenario 1: Retrieve User Profile Successfully
```gherkin
Given a valid user ID is provided in UUID format
And the user is authenticated with a valid token
And the user has permission to access this profile
When GET /api/v1/users/{userId}?includeGphc=true is called
Then the API returns status 200
And the response includes: userId, givenName, surname, jobTitle, mail, gphcNumber, retrievedDateTime
And response time is < 200ms (p95)
```

### Scenario 2: Cache Hit
```gherkin
Given a user's profile was retrieved less than 5 minutes ago
And the request is for the same userId
When GET /api/v1/users/{userId} is called with cached=true
Then the API returns status 200
And the cached result is returned
And response time is < 10ms
And Cache-Control header shows: max-age=300
```

### Scenario 3: Invalid User ID Format
```gherkin
Given an invalid user ID (not UUID format)
When GET /api/v1/users/invalid-user-id is called
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "User ID must be a valid UUID format"
And the errors object contains: { "userId": ["User ID must be a valid UUID format"] }
And no data is persisted
```

### Scenario 4: Missing or Expired Token
```gherkin
Given the Authorization header is missing or contains an expired token
When GET /api/v1/users/{userId} is called
Then the API returns status 401
And the response type is "AuthenticationException"
And the detail message is "Invalid or expired authorization token"
And no data is persisted
```

### Scenario 5: User Lacks Permission
```gherkin
Given an authenticated user requests another user's profile
And the authenticated user does not have admin role
When GET /api/v1/users/{otherUserId} is called
Then the API returns status 403
And the response type is "UnauthorizedAccessException"
And the detail message is "User does not have permission to access profile information for this user ID"
And no data is persisted
```

### Scenario 6: User Not Found in EntraID
```gherkin
Given a valid userId that does not exist in EntraID
When GET /api/v1/users/{userId} is called
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message contains "User with ID {userId} not found in EntraID"
And no data is persisted
```

### Scenario 7: Microsoft Graph API Unavailable
```gherkin
Given the Microsoft Graph API is temporarily unavailable
When GET /api/v1/users/{userId} is called
Then the API returns status 502
And the response type is "ServiceUnavailableException"
And the detail message is "Microsoft Graph API is temporarily unavailable. Please try again later."
And the request can be safely retried after waiting 60 seconds
And no data is persisted
```

---

## Definition of Done

- [ ] Endpoint implemented per specification
- [ ] Authentication (Bearer token) enforced
- [ ] Authorization checks implemented (user can only access own profile unless admin)
- [ ] Request validation implemented (UUID format check)
- [ ] All acceptance criteria met
- [ ] Error handling implemented per [API-ERROR-HANDLING.md](../standards/API-ERROR-HANDLING.md)
- [ ] Response format matches [API-RESPONSE-PATTERNS.md](../standards/API-RESPONSE-PATTERNS.md)
- [ ] Response headers include `Content-Type: application/json` and `Cache-Control: private, max-age=300`
- [ ] Caching implemented (5-minute cache for same user)
- [ ] Microsoft Graph API integration tested
- [ ] Unit tests passing (80%+ code coverage)
- [ ] Integration tests passing
- [ ] Performance benchmarks met (< 200ms p95)
- [ ] Rate limiting configured (1000 req/min)
- [ ] API documentation (Swagger/OpenAPI) generated
- [ ] Ready for integration testing with frontend

---

## Implementation Notes

### EntraID Integration
- Use Microsoft Graph API endpoint: `GET https://graph.microsoft.com/v1.0/me`
- Request directory extension for GPHC number: `extension_<appId>_gphcNumber`
- Map EntraID attributes:
  - `givenName` → `givenName`
  - `surname` → `surname`
  - `jobTitle` → `jobTitle`
  - `mail` → `mail`
  - `extension_<appId>_gphcNumber` → `gphcNumber` (custom directory extension)

### GPHC Number (Professional Registration)
- GPHC number is stored as a custom extension attribute in EntraID
- Format: 7-digit numeric string (e.g., "2156789")
- Only included if `includeGphc=true` query parameter is set
- If GPHC number is not available or user is not a pharmacist, omit the field or return `null`
- This attribute requires appropriate permissions to read from Microsoft Graph

### Data Mapping
All responses must normalize EntraID data:
- Ensure `mail` is lowercase
- Handle `null` values gracefully (return empty string or omit field per standards)
- Include timestamp of retrieval for audit purposes

### Security Considerations
- Never log or expose access tokens in any output
- Validate token claims (`oid`) match requested userId
- Implement rate limiting to prevent brute-force attacks
- Cache responses to reduce Microsoft Graph API calls

### Error Recovery
- Implement retry logic with exponential backoff for Microsoft Graph timeouts
- Set timeout threshold to 10 seconds for Microsoft Graph calls
- Log all failed Microsoft Graph calls for monitoring

---

## Related Standards

- [API-PATH-CONVENTIONS.md](../standards/API-PATH-CONVENTIONS.md) — URL structure and naming
- [API-RESPONSE-PATTERNS.md](../standards/API-RESPONSE-PATTERNS.md) — Response format
- [API-ERROR-HANDLING.md](../standards/API-ERROR-HANDLING.md) — Error response structure
