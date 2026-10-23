# API Story: Create DMR Registration

## Story Type: API | Feature: 619397 | Effort: 8 hours

---

## Description

### Story Card
**As a** Clinical System  
**I want** to register a patient for the DMR (Discharge Medicines Review) service with a nominated pharmacy  
**So that** the patient's medication supply can be managed consistently across the national system

### Background
Authorised external NHS Wales systems need to create new DMR registrations for patients, specifying their nominated pharmacy. The API must validate pharmacy status and prevent duplicate registrations, ensuring data consistency.

**DMR:** Discharge Medicines Review - a service providing pharmaceutical assessment and optimisation of medicines post-discharge.

### Objective
Deliver a secure POST endpoint that creates a new DMR registration with comprehensive validation, audit logging, and error handling.

---

## Endpoint Details

**HTTP Method:** POST  
**Endpoint:** `/api/v1/patients/{nhsNumber}/dmr-registration`  
**Full URL:** `https://api.choicepharmacy.nhs.wales/api/v1/patients/{nhsNumber}/dmr-registration`

### Path Parameters
- `nhsNumber`: [string, required] - Patient NHS Number (10 digits)

### Request Headers
```
Authorization: Bearer [access_token]
Content-Type: application/json
Accept: application/json
```

### Request Example
```
POST /api/v1/patients/1234567890/dmr-registration
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "nominatedPharmacyOdsCode": "W12345"
}
```

---

## Request Body

### Schema
```json
{
  "nominatedPharmacyOdsCode": {
    "type": "string",
    "required": true,
    "pattern": "^[A-Z][A-Z0-9]{4}$",
    "example": "W12345",
    "description": "ODS code of the pharmacy to nominate (must be active and DMR-accredited)"
  }
}
```

---

## Response

### Success Response (201 Created)
```json
{
  "nhsNumber": "1234567890",
  "registered": true,
  "registrationStatus": "Active",
  "registrationDate": "2026-10-23T15:37:24Z",
  "nominatedPharmacy": {
    "odsCode": "W12345",
    "name": "Example Pharmacy"
  },
  "lastUpdated": "2026-10-23T15:37:24Z"
}
```

### Validation Failure Response (400)
```json
{
  "type": "ValidationException",
  "title": "ValidationException",
  "status": 400,
  "detail": "The nominated pharmacy ODS code is invalid",
  "errors": {
    "nominatedPharmacyOdsCode": ["The nominated pharmacy ODS code is invalid"]
  }
}
```

### Conflict Response (409)
```json
{
  "type": "EntityAlreadyExistsException",
  "title": "EntityAlreadyExistsException",
  "status": 409,
  "detail": "Patient 1234567890 already has an active DMR registration"
}
```

### Pharmacy Not Found Response (404)
```json
{
  "type": "EntityNotFoundException",
  "title": "EntityNotFoundException",
  "status": 404,
  "detail": "Pharmacy with ODS code W12345 not found"
}
```

---

## Error Handling

| Status Code | Exception Type | Detail Message |
|---|---|---|
| 400 | ValidationException | NHS Number must be exactly 10 digits |
| 400 | ValidationException | The nominated pharmacy ODS code is invalid |
| 401 | AuthenticationException | Invalid or expired authorization token |
| 403 | UnauthorizedAccessException | User does not have permission to create DMR registrations |
| 404 | EntityNotFoundException | Patient with NHS Number {nhsNumber} not found |
| 404 | EntityNotFoundException | Pharmacy with ODS code {odsCode} not found |
| 409 | EntityAlreadyExistsException | Patient {nhsNumber} already has an active DMR registration |
| 500 | Internal Server Error | An unexpected error occurred |

---

## Business Rules

1. **No Duplicate Registrations:** Patient must not have an active DMR registration already
2. **Pharmacy Validation:** Nominated pharmacy must:
   - Exist in national pharmacy reference data
   - Be active (status = "Active")
   - Be accredited to provide DMR service
3. **Registration Date:** Registration date shall be set to current UTC timestamp
4. **Audit Logging:** All attempts (success and failure) logged with caller, NHS Number, pharmacy ODS code, timestamp

---

## Acceptance Criteria

**Total Scenarios: 7**

### Scenario 1: Successfully register patient for DMR
```gherkin
Given a patient with NHS Number that exists in the system
And the patient does not have an active DMR registration
And a valid nominated pharmacy ODS code is provided
And the pharmacy is active and accredited for DMR
When POST /api/v1/patients/{nhsNumber}/dmr-registration is called with nominatedPharmacyOdsCode
Then the API returns status 201 Created
And the response includes: nhsNumber, registered: true, registrationStatus: "Active"
And the response includes: registrationDate (current UTC timestamp), nominatedPharmacy object
And the nominatedPharmacy includes: odsCode, name
And the DMR registration is persisted in the database
And audit log records: timestamp, calling application, NHS Number, pharmacy ODS code, action "Created"
```

### Scenario 2: Cannot register if patient already has active registration
```gherkin
Given a patient with an active DMR registration
And a request is made to register the same patient again
When POST /api/v1/patients/{nhsNumber}/dmr-registration is called
Then the API returns status 409 Conflict
And the response type is "EntityAlreadyExistsException"
And the detail message is "Patient {nhsNumber} already has an active DMR registration"
And no data is persisted
```

### Scenario 3: Cannot register with non-existent pharmacy
```gherkin
Given a pharmacy ODS code that does not exist in reference data
When POST /api/v1/patients/{nhsNumber}/dmr-registration is called with invalid ODS code
Then the API returns status 404 Not Found
And the response type is "EntityNotFoundException"
And the detail message is "Pharmacy with ODS code {odsCode} not found"
And no data is persisted
```

### Scenario 4: Cannot register with inactive pharmacy
```gherkin
Given a pharmacy ODS code that exists but is marked as inactive
When POST /api/v1/patients/{nhsNumber}/dmr-registration is called with inactive pharmacy ODS code
Then the API returns status 400 Bad Request
And the response type is "ValidationException"
And the detail message is "The nominated pharmacy ODS code is invalid"
And the errors object contains: { "nominatedPharmacyOdsCode": ["The nominated pharmacy ODS code is invalid"] }
And no data is persisted
```

### Scenario 5: Cannot register with non-DMR-accredited pharmacy
```gherkin
Given a pharmacy ODS code that exists and is active but is not accredited for DMR
When POST /api/v1/patients/{nhsNumber}/dmr-registration is called
Then the API returns status 400 Bad Request
And the response type is "ValidationException"
And the detail message is "The nominated pharmacy ODS code is invalid"
And the errors object contains: { "nominatedPharmacyOdsCode": ["The nominated pharmacy ODS code is invalid"] }
And no data is persisted
```

### Scenario 6: Invalid NHS Number format
```gherkin
Given an invalid NHS Number is provided (not 10 digits)
When POST /api/v1/patients/invalid-nhs-number/dmr-registration is called
Then the API returns status 400 Bad Request
And the response type is "ValidationException"
And the detail message is "NHS Number must be exactly 10 digits"
And the errors object contains: { "nhsNumber": ["NHS Number must be exactly 10 digits"] }
And no data is persisted
```

### Scenario 7: Patient not found
```gherkin
Given a request for a patient NHS Number that does not exist in the system
When POST /api/v1/patients/9999999999/dmr-registration is called
Then the API returns status 404 Not Found
And the response type is "EntityNotFoundException"
And the detail message is "Patient with NHS Number 9999999999 not found"
And no data is persisted
```

---

## Security Requirements

**Authentication:** OAuth 2.0 / Azure Entra ID  
**Authorisation:** Caller must have `DMR Registration Create` scope  
**Data Protection:** HTTPS only, encrypted at rest and in transit

---

## Non-Functional Requirements

| Requirement | Target |
|---|---|
| Availability | 99.9% |
| Response Time | < 2 seconds (95th percentile) |
| Interface Standard | REST API |
| Data Format | JSON |
| Audit Retention | 8 years minimum |
| Audit Logging | All requests logged with patient identifier, timestamp, calling application, action |

---

## Definition of Done

- [ ] Endpoint implemented per specification
- [ ] NHS Number validation (10 digits)
- [ ] Pharmacy ODS code validation format
- [ ] Pharmacy reference data lookup implemented
- [ ] Pharmacy active status validation
- [ ] Pharmacy DMR accreditation validation
- [ ] Duplicate registration check implemented
- [ ] All acceptance criteria met
- [ ] Authentication/authorization verified
- [ ] Audit logging implemented (caller, NHS Number, ODS code, timestamp, action)
- [ ] Transactional integrity verified
- [ ] Unit tests passing (80%+ coverage)
- [ ] Integration tests passing
- [ ] Performance benchmarks met (< 2s response time)
- [ ] API documentation generated (OpenAPI 3.0)
- [ ] Ready for integration testing

---

## Notes
- ODS code format: First character = letter, followed by 4 alphanumeric characters (e.g., W12345)
- Registration date set automatically to current UTC timestamp
- Pharmacy validation must call national pharmacy reference data service
- All timestamps in ISO 8601 format with UTC timezone (Z suffix)
- Idempotent retry: If exact same request retried within 60 seconds, return 201 with same registration (no double-create)
