# API Story: Retrieve DMR Registration

## Story Type: API | Feature: 619397 | Effort: 5 hours

---

## Description

### Story Card
**As a** Clinical System  
**I want** to retrieve a patient's current DMR (Discharge Medicines Review) registration status  
**So that** I can determine their nominated pharmacy and active DMR status for clinical decision-making

### Background
Authorised external NHS Wales systems need to query DMR registration status for patients to ensure consistent medication supply coordination across national services. This is the foundational read operation for the DMR Registration API.

**DMR:** Discharge Medicines Review - a service providing pharmaceutical assessment and optimisation of medicines post-discharge.

### Objective
Deliver a secure GET endpoint that returns the current DMR registration for a patient, including registration status, nominated pharmacy, and audit timestamps.

---

## Endpoint Details

**HTTP Method:** GET  
**Endpoint:** `/api/v1/patients/{nhsNumber}/dmr-registration`  
**Full URL:** `https://api.choicepharmacy.nhs.wales/api/v1/patients/{nhsNumber}/dmr-registration`

### Path Parameters
- `nhsNumber`: [string, required] - Patient NHS Number (10 digits)

### Query Parameters
None

### Request Headers
```
Authorization: Bearer [access_token]
Content-Type: application/json
Accept: application/json
```

### Request Example
```
GET /api/v1/patients/1234567890/dmr-registration
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

---

## Response

### Success Response (200 OK)
```json
{
  "nhsNumber": "1234567890",
  "registered": true,
  "registrationStatus": "Active",
  "registrationDate": "2026-10-01T10:00:00Z",
  "nominatedPharmacy": {
    "odsCode": "W12345",
    "name": "Example Pharmacy"
  },
  "lastUpdated": "2026-10-10T14:00:00Z"
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

### Not Registered Response (200 OK)
When a patient exists but has no active DMR registration:
```json
{
  "nhsNumber": "1234567890",
  "registered": false,
  "registrationStatus": null,
  "registrationDate": null,
  "nominatedPharmacy": null,
  "lastUpdated": null
}
```

---

## Error Handling

| Status Code | Exception Type | Detail Message |
|---|---|---|
| 400 | ValidationException | NHS Number must be exactly 10 digits |
| 401 | AuthenticationException | Invalid or expired authorization token |
| 403 | UnauthorizedAccessException | User does not have permission to access patient data |
| 404 | EntityNotFoundException | Patient with NHS Number {nhsNumber} not found |
| 500 | Internal Server Error | An unexpected error occurred |

---

## Acceptance Criteria

**Total Scenarios: 5**

### Scenario 1: Retrieve active DMR registration
```gherkin
Given a patient has an active DMR registration
When GET /api/v1/patients/{nhsNumber}/dmr-registration is called
Then the API returns status 200
And the response includes: nhsNumber, registered: true, registrationStatus: "Active"
And the response includes: registrationDate (ISO 8601 format), nominatedPharmacy object
And the nominatedPharmacy includes: odsCode, name
And the lastUpdated timestamp is present
```

### Scenario 2: Retrieve inactive DMR registration
```gherkin
Given a patient has an inactive DMR registration
When GET /api/v1/patients/{nhsNumber}/dmr-registration is called
Then the API returns status 200
And the response includes: nhsNumber, registered: false
And the response includes: registrationStatus: null, registrationDate: null, nominatedPharmacy: null
```

### Scenario 3: Patient with no registration record
```gherkin
Given a patient exists but has never been registered for DMR
When GET /api/v1/patients/{nhsNumber}/dmr-registration is called
Then the API returns status 200
And the response includes: nhsNumber, registered: false
And all registration fields are null
```

### Scenario 4: Invalid NHS Number format
```gherkin
Given an invalid NHS Number is provided (not 10 digits)
When GET /api/v1/patients/invalid-nhs-number/dmr-registration is called
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "NHS Number must be exactly 10 digits"
And the errors object contains: { "nhsNumber": ["NHS Number must be exactly 10 digits"] }
And no data is persisted
```

### Scenario 5: Patient not found
```gherkin
Given a request for a patient NHS Number that does not exist in the system
When GET /api/v1/patients/9999999999/dmr-registration is called
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message is "Patient with NHS Number 9999999999 not found"
And no data is persisted
```

---

## Security Requirements

**Authentication:** OAuth 2.0 / Azure Entra ID  
**Authorisation:** Caller must have `DMR Registration Read` scope  
**Data Protection:** HTTPS only, encrypted at rest and in transit

---

## Non-Functional Requirements

| Requirement | Target |
|---|---|
| Availability | 99.9% |
| Response Time | < 2 seconds (95th percentile) |
| Interface Standard | REST API |
| Data Format | JSON |
| Audit Logging | All requests logged with patient identifier, timestamp, calling application |

---

## Definition of Done

- [ ] Endpoint implemented per specification
- [ ] Request validation for NHS Number format
- [ ] All acceptance criteria met
- [ ] Authentication/authorization verified
- [ ] Audit logging implemented (caller, timestamp, NHS Number)
- [ ] Unit tests passing (80%+ coverage)
- [ ] Integration tests passing
- [ ] Performance benchmarks met (< 2s response time)
- [ ] API documentation generated (OpenAPI 3.0)
- [ ] Ready for integration testing

---

## Notes
- NHS Number validation: Must be exactly 10 numeric digits
- Response includes both active and inactive states; consumers should check `registered` field
- Timestamps in ISO 8601 format with UTC timezone (Z suffix)
