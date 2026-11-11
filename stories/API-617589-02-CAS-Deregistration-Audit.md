# API Story: GET /api/v1/cas/housekeeping/deregistrations - Retrieve CAS De-Registration Audit Logs

## Story Type: API | Feature: 617589 | Effort: 8 hours

---

## Description

### Story Card
**As a** Clinical Administrator or Compliance Officer  
**I want** to retrieve and report on CAS de-registrations performed by the housekeeping process  
**So that** I can audit the automatic de-registration actions, verify compliance with policy, and produce governance reports

### Background
The automatic CAS de-registration housekeeping feature maintains a complete audit trail of all de-registered patients. This story delivers an endpoint to query that audit trail, supporting compliance, governance, and operational verification.

### Objective
Deliver a query endpoint that retrieves CAS de-registration records with support for date range filtering, pagination, and sorting — enabling administrators to audit, verify, and report on automated de-registration actions.

---

## Endpoint Details

**HTTP Method:** GET  
**Endpoint:** `/api/v1/cas/housekeeping/deregistrations`  
**Full URL:** `https://api.example.com/api/v1/cas/housekeeping/deregistrations`

### Authorization
This endpoint is accessible to authenticated users with audit/read permissions. No elevated role required.

- **Required Permission:** `CASAuditRead` or equivalent read-only permission
- **Authentication:** Bearer Token (JWT)

---

## Request

### Request Headers
```
Authorization: Bearer {token}
Content-Type: application/json
Accept: application/json
```

### Query Parameters

| Parameter | Type | Required | Default | Validation |
|-----------|------|----------|---------|-----------|
| `from` | ISO 8601 Date | No | None | Must be ≤ `to` if both provided |
| `to` | ISO 8601 Date | No | Today | Must be ≥ `from` if both provided |
| `q` | string | No | None | Free-text search (patient ID, NHS number, etc.) |
| `limit` | integer | No | 50 | Between 1 and 500 |
| `skip` | integer | No | 0 | Must be ≥ 0 |
| `sortBy` | string | No | `deregistrationDate` | Enum: `deregistrationDate`, `patientId`, `nhs-number` |

### Request Example
```
GET /api/v1/cas/housekeeping/deregistrations?from=2026-09-01&to=2026-11-02&limit=50&skip=0&sortBy=deregistrationDate
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json
```

---

## Response

### Success Response (200 OK)
```json
{
  "items": [
    {
      "deregistrationId": "550e8400-e29b-41d4-a716-446655440001",
      "patientId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
      "nhsNumber": "9000000009",
      "deregistrationDate": "2026-11-02T15:54:56Z",
      "reason": "No CAS consultation within 12 months",
      "inactivityDays": 365,
      "lastConsultationDate": "2025-10-30T14:20:00Z",
      "processedByJobId": "550e8400-e29b-41d4-a716-446655440000",
      "recordType": "active",
      "auditedBy": "System"
    },
    {
      "deregistrationId": "550e8400-e29b-41d4-a716-446655440002",
      "patientId": "4fa85f64-5717-4562-b3fc-2c963f66afa7",
      "nhsNumber": "9000000010",
      "deregistrationDate": "2026-11-02T15:54:56Z",
      "reason": "No CAS consultation within 12 months",
      "inactivityDays": 365,
      "lastConsultationDate": "2025-10-15T09:30:00Z",
      "processedByJobId": "550e8400-e29b-41d4-a716-446655440000",
      "recordType": "orphaned",
      "auditedBy": "System"
    }
  ],
  "total": 87,
  "skip": 0,
  "limit": 50
}
```

**Response Fields:**

**Top-level:**
- `items`: [array] — Array of de-registration records
- `total`: [integer] — Total count of de-registrations matching the filter
- `skip`: [integer] — Offset of results
- `limit`: [integer] — Maximum results returned per request

**Each item:**
- `deregistrationId`: [UUID string] — Unique identifier for this de-registration record
- `patientId`: [UUID string] — Choose Pharmacy internal patient ID
- `nhsNumber`: [string] — Patient's NHS Number (if available)
- `deregistrationDate`: [ISO 8601 timestamp] — When the de-registration was recorded
- `reason`: [string] — Reason for de-registration (e.g., "No CAS consultation within 12 months")
- `inactivityDays`: [integer] — Inactivity threshold used (typically 365)
- `lastConsultationDate`: [ISO 8601 timestamp, nullable] — Date of patient's last CAS consultation (null if none found)
- `processedByJobId`: [UUID string] — ID of the housekeeping job that performed the de-registration
- `recordType`: [enum: "active", "orphaned"] — Whether this was an active registration or orphaned legacy record
- `auditedBy`: [string] — Always "System" for automated de-registrations

**HTTP Status:** `200 OK`

### Empty Results
```json
{
  "items": [],
  "total": 0,
  "skip": 0,
  "limit": 50
}
```

**HTTP Status:** `200 OK` (not 404)

---

## Error Handling

| Status Code | Error Type | Detail Message |
|---|---|---|
| 400 | `ValidationException` | `Date range is invalid: 'from' must be before or equal to 'to'` |
| 400 | `ValidationException` | `Limit must be between 1 and 500` |
| 400 | `ValidationException` | `Skip must be a non-negative integer` |
| 401 | `AuthenticationException` | `Invalid or expired authorization token` |
| 403 | `UnauthorizedAccessException` | `User does not have permission to read CAS audit logs` |
| 500 | `InternalServerError` | `An unexpected error occurred while retrieving de-registration records` |

---

## Acceptance Criteria

**Total Scenarios: 10**

### Scenario 1: Retrieve All De-Registrations (No Filter)
```gherkin
Given an authenticated user with CASAuditRead permission
And there are 87 CAS de-registrations in the system
When GET /api/v1/cas/housekeeping/deregistrations is called
Then the API returns status 200
And the response items array contains up to 50 de-registration records (default limit)
And the total count is 87
And skip is 0 and limit is 50
```

### Scenario 2: Filter by Date Range
```gherkin
Given an authenticated user with CASAuditRead permission
And there are 87 CAS de-registrations across various dates
When GET /api/v1/cas/housekeeping/deregistrations?from=2026-10-01&to=2026-11-02 is called
Then the API returns status 200
And only de-registrations within the date range (2026-10-01 to 2026-11-02) are returned
And the total count reflects only matching records
```

### Scenario 3: Pagination Support
```gherkin
Given an authenticated user with CASAuditRead permission
And there are 100 CAS de-registrations in the system
When GET /api/v1/cas/housekeeping/deregistrations?limit=25&skip=0 is called first
Then the API returns 25 records with total=100
When GET /api/v1/cas/housekeeping/deregistrations?limit=25&skip=25 is called next
Then the API returns the next 25 records (records 26-50)
When GET /api/v1/cas/housekeeping/deregistrations?limit=25&skip=50 is called
Then the API returns records 51-75
```

### Scenario 4: Free-Text Search
```gherkin
Given an authenticated user with CASAuditRead permission
And there are 87 de-registrations with various patient IDs and NHS numbers
When GET /api/v1/cas/housekeeping/deregistrations?q=9000000009 is called
Then the API returns de-registrations matching the search term
And the search term is checked against patient ID and NHS number fields
```

### Scenario 5: Sorting by De-Registration Date (Descending)
```gherkin
Given an authenticated user with CASAuditRead permission
And there are 87 de-registrations from different dates
When GET /api/v1/cas/housekeeping/deregistrations?sortBy=deregistrationDate is called
Then the API returns de-registrations sorted by deregistrationDate in descending order (most recent first)
```

### Scenario 6: Sorting by Patient ID
```gherkin
Given an authenticated user with CASAuditRead permission
And there are 87 de-registrations
When GET /api/v1/cas/housekeeping/deregistrations?sortBy=patientId is called
Then the API returns de-registrations sorted by patientId in ascending order
```

### Scenario 7: Distinguishing Active vs. Orphaned Records
```gherkin
Given an authenticated user with CASAuditRead permission
And there are de-registrations from both active and orphaned records
When GET /api/v1/cas/housekeeping/deregistrations is called
Then each item includes a recordType field: either "active" or "orphaned"
And active registrations show their last consultation date
And orphaned records show their legacy system last consultation date
```

### Scenario 8: Validation - Invalid Date Range
```gherkin
Given an authenticated user with CASAuditRead permission
When GET /api/v1/cas/housekeeping/deregistrations?from=2026-11-02&to=2026-10-01 is called
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Date range is invalid: 'from' must be before or equal to 'to'"
And the errors object contains: { "dateRange": ["Date range is invalid: 'from' must be before or equal to 'to'"] }
And no data is persisted
```

### Scenario 9: Validation - Invalid Pagination Limit
```gherkin
Given an authenticated user with CASAuditRead permission
When GET /api/v1/cas/housekeeping/deregistrations?limit=1000 is called
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Limit must be between 1 and 500"
And the errors object contains: { "limit": ["Limit must be between 1 and 500"] }
And no data is persisted
```

### Scenario 10: Missing Authentication
```gherkin
Given a request with no Authorization header
When GET /api/v1/cas/housekeeping/deregistrations is called
Then the API returns status 401
And the response type is "AuthenticationException"
And the detail message is "Invalid or expired authorization token"
And no data is persisted
```

---

## Definition of Done

- [ ] Endpoint implemented per specification
- [ ] Query parameter validation working (date range, pagination, sortBy)
- [ ] Authentication/authorization verified (CASAuditRead permission required)
- [ ] Date range filtering correctly applied (from/to inclusive)
- [ ] Free-text search working across patient ID and NHS number fields
- [ ] Pagination working correctly (skip/limit with total count)
- [ ] Sorting by deregistrationDate, patientId implemented
- [ ] Active and orphaned record types correctly distinguished
- [ ] Last consultation date correctly populated/null for orphaned records
- [ ] Response format matches specification (paginated with metadata)
- [ ] All error scenarios return appropriate HTTP status codes
- [ ] Empty result set returns 200 OK with empty items array
- [ ] Unit tests passing (80%+ coverage)
- [ ] Integration tests passing
- [ ] Performance testing: pagination works efficiently with 10,000+ records
- [ ] API documentation generated
- [ ] Ready for integration testing

---

## Related Stories

- **API Story 1:** POST /api/v1/cas/housekeeping/deregister — Trigger CAS de-registration housekeeping

---

## Notes

- The de-registration audit trail is critical for compliance and governance. All entries include system-generated timestamps and reason tracking.
- Orphaned records that are successfully de-registered and cleaned up from the legacy table still appear in this audit trail, marked as `recordType: "orphaned"`.
- The search functionality (`q` parameter) supports patient ID and NHS number lookups for quick audit retrieval.
- Default pagination (50 records) balances performance with usability; the 500 limit supports batch exports for reporting.
- Sorting is available but not required to be multi-field; single-field sorting is acceptable.
