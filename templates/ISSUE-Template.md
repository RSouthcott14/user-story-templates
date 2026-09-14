# API User Story Template

## Story Type: API

---

## Description

### Story Card
**As a** [consumer/system]  
**I want** [API capability]  
**So that** [business value/integration]

### Background
[Why is this API needed? What does it enable? Integration context?]

### Objective
[Clear description of what the API endpoint provides]

---

## Endpoint Details

**HTTP Method:** [GET / POST / PUT / PATCH / DELETE]  
**Endpoint:** `[/api/resource]`  
**Full URL:** `[https://api.example.com/api/resource]`

### Path Parameters
- `{id}`: [Description] - [Type: string/integer]

### Query Parameters
- `filter`: [Description] - Optional
- `limit`: [Description] - Optional

---

## Request

### Request Headers
```
Authorization: Bearer {token}
Content-Type: application/json
Accept: application/json
```

### Request Body (Example)
```json
{
  "field1": "value1",
  "field2": 123
}
```

---

## Response

### Success Response (200 OK)
```json
{
  "id": "123456",
  "status": "success",
  "data": {
    "field1": "value1"
  }
}
```

### Error Response (400 Bad Request)
```json
{
  "status": "error",
  "code": "INVALID_REQUEST",
  "message": "Missing required field: field1"
}
```

---

## Error Handling

| Status Code | Error | Description |
|-------------|-------|-------------|
| 400 | INVALID_REQUEST | Missing/invalid parameters |
| 401 | UNAUTHORIZED | Invalid credentials |
| 403 | FORBIDDEN | No permission |
| 404 | NOT_FOUND | Resource doesn't exist |
| 500 | SERVER_ERROR | Server-side error |

---

## Authentication & Authorization

**Authentication Method:** [Bearer Token / API Key / OAuth 2.0]

**Required Roles:**
- [Role 1]: [What can they do?]
- [Role 2]: [What can they do?]

---

## Acceptance Criteria

### Scenario 1: Success Path
```gherkin
Given the client has a valid token
When a valid request is sent to [endpoint]
Then the API returns status 200
And the response matches the schema
```

### Scenario 2: Validation
```gherkin
Given a request with missing required field
When the request is sent
Then the API returns status 400
And appropriate error message is returned
```

### Scenario 3: Authentication
```gherkin
Given the client has no valid token
When a request is sent
Then the API returns status 401
```

### Scenario 4: Authorization
```gherkin
Given the client has no permission
When a request is sent
Then the API returns status 403
```

### Scenario 5: Data Integrity
```gherkin
Given a valid request
When the API creates/updates a resource
Then the data is persisted correctly
And subsequent GET request returns the same data
```

### Scenario 6: Performance
```gherkin
Given a valid request
When the API processes the request
Then response time is < 200ms (95th percentile)
```

---

## Dependencies

**External Services:**
- [Service name]: [Purpose]

**Database:**
- [Tables/Collections]: [Which data is read/written]

---

## Rate Limiting
- **Limit:** 1000 requests per minute
- **Exceeded Response:** Status 429

---

## Definition of Done

- [ ] Endpoint implemented per specification
- [ ] All acceptance criteria met
- [ ] Request/response validation working
- [ ] Error handling implemented
- [ ] Authentication/authorization verified
- [ ] Unit tests passing (80%+ coverage)
- [ ] Integration tests passing
- [ ] API documentation generated
- [ ] Performance benchmarks met
- [ ] Ready for integration testing

---

## Notes
[Additional technical details or considerations]
