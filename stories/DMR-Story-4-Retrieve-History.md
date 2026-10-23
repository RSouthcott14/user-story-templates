# API Story: Retrieve DMR Registration History

## Story Type: API | Feature: 619397 | Effort: 8 hours

---

## Description

### Story Card
**As a** Authorised System or Auditor  
**I want** to retrieve the complete change history for a patient's DMR registration  
**So that** I can track registration changes for audit, compliance, and operational investigation purposes

### Background
DMR registration changes must be traceable for audit and compliance purposes. The API must provide a complete history of all registration events (create, update, deregister, reactivate) with full context about what changed, when, who made the change, and which calling system initiated it.

### Objective
Deliver a secure GET endpoint that returns paginated DMR registration history with detailed audit information, supporting flexible filtering and sorting.

---

## Endpoint Details

**HTTP Method:** GET  
**Endpoint:** `/api/v1/patients/{nhsNumber}/dmr-registration/history`  
**Full URL:** `https://api.choicepharmacy.nhs.wales/api/v1/patients/{nhsNumber}/dmr-registration/history`

### Path Parameters
- `nhsNumber`: [string, required] - Patient NHS Number (10 digits)

### Query Parameters
- `limit`: [integer, optional, default: 10, max: 100] - Maximum number of history records to return
- `skip`: [integer, optional, default: 0] - Number of records to skip (for pagination)
- `sortBy`: [string, optional, default: "timestamp", enum: "timestamp"] - Sort field
- `from`: [date-time, optional] - Filter: history events from this date (ISO 8601)
- `to`: [date-time, optional] - Filter: history events up to this date (ISO 8601)

### Request Headers
```
Authorization: Bearer [access_token]
Content-Type: application/json
Accept: application/json
```

### Request Example
```
GET /api/v1/patients/1234567890/dmr-registration/history?limit=20&skip=0&from=2026-01-01&to=2026-12-31
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

---

## Response

### Success Response (200 OK)
```json
{
  "nhsNumber": "1234567890",
  "items": [
    {
      "id": "HIST-001",
      "timestamp": "2026-10-23T15:37:24Z",
      "action": "Created",
      "sourceSystem": "NHS Wales Clinical System X",
      "userId": "user123",
      "previousValues": null,
      "newValues": {
        "registrationStatus": "Active",
        "nominatedPharmacyOdsCode": "W12345",
        "nominatedPharmacyName": "Example Pharmacy"
      },
      "correlationId": "corr-12345678",
      "changeReason": "Initial registration"
    },
    {
      "id": "HIST-002",
      "timestamp": "2026-10-24T09:15:00Z",
      "action": "Updated",
      "sourceSystem": "NHS Wales Clinical System X",
      "userId": "user456",
      "previousValues": {
        "nominatedPharmacyOdsCode": "W12345",
        "nominatedPharmacyName": "Example Pharmacy"
      },
      "newValues": {
        "nominatedPharmacyOdsCode": "W67890",
        "nominatedPharmacyName": "New Example Pharmacy"
      },
      "correlationId": "corr-87654321",
      "changeReason": "Patient requested pharmacy change"
    },
    {
      "id": "HIST-003",
      "timestamp": "2026-10-25T14:20:30Z",
      "action": "Deregistered",
      "sourceSystem": "NHS Wales Clinical System X",
      "userId": "user789",
      "previousValues": {
        "registrationStatus": "Active",
        "nominatedPharmacyOdsCode": "W67890"
      },
      "newValues": {
        "registrationStatus": "Inactive"
      },
      "correlationId": "corr-11223344",
      "changeReason": "Patient no longer requires DMR service"
    }
  ],
  "total": 3,
  "skip": 0,
  "limit": 20
}
```

### Patient Not Found Response (404)
```json
{
  "type": "EntityNotFoundException",
  "title": "EntityNotFoundException",
  "status": 404,
  "detail": "Patient with NHS Number 1234567890 not found"
}
```

### Empty History Response (200 OK)
When patient exists but has no registration history:
```json
{
  "nhsNumber": "1234567890",
  "items": [],
  "total": 0,
  "skip": 0,
  "limit": 20
}
```

---

## Response Fields

Each history entry includes:

| Field | Type | Description |
|---|---|---|
| `id` | string | Unique identifier for this history record |
| `timestamp` | date-time | ISO 8601 timestamp of the change (UTC) |
| `action` | enum | Type of action: `Created`, `Updated`, `Deregistered`, `Reactivated` |
| `sourceSystem` | string | Name of the calling application that initiated the change |
| `userId` | string | User identifier (if available); null if system-initiated |
| `previousValues` | object | Previous field values (null for Create action) |
| `newValues` | object | New field values (null for Delete action) |
| `correlationId` | string | Correlation ID for tracing across systems |
| `changeReason` | string | Human-readable reason for the change (optional) |

---

## Error Handling

| Status Code | Exception Type | Detail Message |
|---|---|---|
| 400 | ValidationException | NHS Number must be exactly 10 digits |
| 400 | ValidationException | limit must be between 1 and 100 |
| 400 | ValidationException | skip must be non-negative |
| 400 | ValidationException | Invalid date format. Use ISO 8601 (YYYY-MM-DD) |
| 401 | AuthenticationException | Invalid or expired authorization token |
| 403 | UnauthorizedAccessException | User does not have permission to access patient history |
| 404 | EntityNotFoundException | Patient with NHS Number {nhsNumber} not found |
| 500 | Internal Server Error | An unexpected error occurred |

---

## Acceptance Criteria

**Total Scenarios: 8**

### Scenario 1: Retrieve full history for patient with multiple changes
```gherkin
Given a patient with multiple DMR registration history entries
When GET /api/v1/patients/{nhsNumber}/dmr-registration/history is called
Then the API returns status 200 OK
And the response includes: nhsNumber, items array, total count, skip, limit
And the items array includes all history entries sorted by timestamp descending
And each entry includes: id, timestamp, action, sourceSystem, userId, previousValues, newValues, correlationId
And the total count reflects the actual number of history records
```

### Scenario 2: Retrieve history with pagination
```gherkin
Given a patient with 50 DMR registration history entries
When GET /api/v1/patients/{nhsNumber}/dmr-registration/history?limit=10&skip=20 is called
Then the API returns status 200 OK
And the response includes: exactly 10 items
And the skip value is 20
And the limit value is 10
And the total count is 50
And the returned items are from positions 21-30 (skip offset)
And subsequent requests with skip=30 return the next 10 items
```

### Scenario 3: Retrieve history with date range filter
```gherkin
Given a patient with DMR history entries spanning multiple months
When GET /api/v1/patients/{nhsNumber}/dmr-registration/history?from=2026-10-01&to=2026-10-31 is called
Then the API returns status 200 OK
And all returned history entries have timestamps between 2026-10-01T00:00:00Z and 2026-10-31T23:59:59Z
And entries outside this range are not returned
```

### Scenario 4: Retrieve history for patient with no registration changes
```gherkin
Given a patient with no DMR registration history
When GET /api/v1/patients/{nhsNumber}/dmr-registration/history is called
Then the API returns status 200 OK
And the items array is empty
And the total count is 0
```

### Scenario 5: History shows all action types correctly
```gherkin
Given a patient with various registration actions (Create, Update, Deregister, Reactivate)
When GET /api/v1/patients/{nhsNumber}/dmr-registration/history is called
Then the API returns status 200 OK
And each action is correctly labeled: "Created" for initial registration, "Updated" for changes, "Deregistered" for removal, "Reactivated" for re-enable
And previousValues is null for "Created" actions
And newValues reflects the action type (all fields for Create, changed fields for Update, status only for Deregister)
```

### Scenario 6: Invalid NHS Number format
```gherkin
Given an invalid NHS Number is provided (not 10 digits)
When GET /api/v1/patients/invalid-nhs-number/dmr-registration/history is called
Then the API returns status 400 Bad Request
And the response type is "ValidationException"
And the detail message is "NHS Number must be exactly 10 digits"
And the errors object contains: { "nhsNumber": ["NHS Number must be exactly 10 digits"] }
And no data is persisted
```

### Scenario 7: Invalid pagination parameters
```gherkin
Given invalid limit or skip query parameters
When GET /api/v1/patients/{nhsNumber}/dmr-registration/history?limit=0&skip=-1 is called
Then the API returns status 400 Bad Request
And the response type is "ValidationException"
And the errors object contains field validation messages for limit and skip
And no data is persisted
```

### Scenario 8: Patient not found
```gherkin
Given a request for a patient NHS Number that does not exist in the system
When GET /api/v1/patients/9999999999/dmr-registration/history is called
Then the API returns status 404 Not Found
And the response type is "EntityNotFoundException"
And the detail message is "Patient with NHS Number 9999999999 not found"
And no data is persisted
```

---

## Security Requirements

**Authentication:** OAuth 2.0 / Azure Entra ID  
**Authorisation:** Caller must have `DMR Registration Read` scope (or higher)  
**Data Protection:** HTTPS only, encrypted at rest and in transit  
**Audit Trail:** Access to history is logged (who accessed, when, correlation ID)

---

## Non-Functional Requirements

| Requirement | Target |
|---|---|
| Availability | 99.9% |
| Response Time | < 2 seconds for 1000 records (95th percentile) |
| Interface Standard | REST API |
| Data Format | JSON |
| Pagination Support | Required (limit 1-100, default 10) |
| Sorting Support | By timestamp (ascending/descending) |
| Audit Retention | 8 years minimum |
| History Retention | Permanent (never deleted) |

---

## Definition of Done

- [ ] Endpoint implemented per specification
- [ ] NHS Number validation (10 digits)
- [ ] Pagination implemented (limit, skip)
- [ ] Date range filtering implemented (from, to)
- [ ] Sorting by timestamp implemented
- [ ] Query parameter validation (limit 1-100, skip >= 0, dates ISO 8601)
- [ ] All acceptance criteria met
- [ ] Authentication/authorization verified
- [ ] Audit logging implemented (access audit trail with correlation ID)
- [ ] History queries optimized with database indexing
- [ ] Unit tests passing (80%+ coverage)
- [ ] Integration tests passing (including large result sets)
- [ ] Performance benchmarks met (< 2s for 1000 records)
- [ ] API documentation generated (OpenAPI 3.0)
- [ ] Ready for integration testing

---

## Notes
- History records are immutable; once created, they cannot be modified or deleted
- Timestamps in ISO 8601 format with UTC timezone (Z suffix)
- Default limit is 10 records; maximum limit is 100 to prevent excessive data retrieval
- Date range filter uses inclusive boundaries (from 00:00:00 to 23:59:59 of specified date)
- Source system name comes from calling application's OAuth2 client name or provided header
- Correlation IDs enable tracing of a single request across systems
- History entries are paginated to handle large datasets efficiently
- Change reason is optional but recommended for audit context
