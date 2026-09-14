# API Example: GET /api/ipstatus - Retrieve Independent Prescriber Status

## Story Type: API | Feature: 606759 | Effort: 8 hours

---

## Description

### Story Card
**As a** Clinical System  
**I want** to retrieve Independent Prescriber (IP) status for a user  
**So that** I can determine their access level and available clinical tools

### Background
The system needs to query the IP status database to determine if a user is registered as an Independent Prescriber. This controls which conditions and tools are available to them in the Clinical Conditions Management (CCM) journey.

### Objective
Deliver a simple, fast GET endpoint that returns IP status with optional caching.

---

## Endpoint Details

**HTTP Method:** GET  
**Endpoint:** `/api/ipstatus`  
**Full URL:** `https://api.example.com/api/ipstatus`

### Query Parameters
- `userId`: [string, required] - User ID in UUID format
- `cached`: [boolean, optional, default: true] - Use cached result or force fresh lookup

---

## Request

### Request Headers
```
Authorization: Bearer {token}
Content-Type: application/json
Accept: application/json
```

### Request Example
```
GET /api/ipstatus?userId=3fa85f64-5717-4562-b3fc-2c963f66afa6&cached=true
Authorization: Bearer eyJhbGciOiJSUzI1NiIs...
```

---

## Response

### Success Response (200 OK)
```json
{
  "userId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "isIndependentPrescriber": true,
  "status": "Active",
  "registrationDate": "2023-06-15",
  "expiryDate": "2026-06-15",
  "scope": ["Drug Allergy", "Adverse Reactions", "Conditions Management"],
  "message": "User is an Independent Prescriber"
}
```

### Not Found Response (404)
```json
{
  "status": "error",
  "code": "USER_NOT_FOUND",
  "message": "User with ID 3fa85f64-5717-4562-b3fc-2c963f66afa6 not found"
}
```

---

## Error Handling

| Status Code | Error | Description |
|-------------|-------|-------------|
| 400 | INVALID_USER_ID | User ID not in valid UUID format |
| 401 | UNAUTHORIZED | Invalid/expired token |
| 403 | FORBIDDEN | User lacks permission to query IP status |
| 404 | USER_NOT_FOUND | User record not found |
| 500 | SERVER_ERROR | Database error |

---

## Acceptance Criteria

### Scenario 1: Return IP Status
```gherkin
Given a valid userId is provided
When GET /api/ipstatus?userId=[uuid] is called
Then the API returns status 200
And the response includes: isIndependentPrescriber, status, registrationDate, expiryDate, scope
And the response matches the schema
```

### Scenario 2: Cache Hit
```gherkin
Given a user's IP status was cached 30 seconds ago
When GET /api/ipstatus?userId=[uuid]&cached=true is called
Then the API returns the cached result
And response time is < 10ms
And cache header shows: Cache-Control: max-age=300
```

### Scenario 3: Validation
```gherkin
Given an invalid user ID (not UUID format)
When GET /api/ipstatus?userId=invalid-id is called
Then the API returns status 400
And the error is: "Invalid user ID format. Expected UUID."
```

### Scenario 4: Performance
```gherkin
Given a valid request
When the API processes the request
Then response time is < 200ms (95th percentile)
And the API handles 1000 requests/min
```

---

## Definition of Done

- [ ] Endpoint implemented per specification
- [ ] All acceptance criteria met
- [ ] Request/response validation working
- [ ] Error handling implemented
- [ ] Authentication/authorization verified
- [ ] Unit tests passing (80%+ coverage)
- [ ] Integration tests passing
- [ ] Performance benchmarks met (< 200ms)
- [ ] Rate limiting configured (1000 req/min)
- [ ] Caching working correctly
- [ ] API documentation generated
- [ ] Ready for integration testing

---

## Notes
- Cache duration: 5 minutes (300 seconds)
- Rate limit: 1000 requests per minute per user
