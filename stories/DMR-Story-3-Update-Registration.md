# API Story: Update DMR Registration

## Story Type: API | Feature: 619397 | Effort: 8 hours

---

## Description

### Story Card
**As a** Clinical System  
**I want** to update a patient's DMR (Discharge Medicines Review) registration (nominated pharmacy and/or status)  
**So that** patients can change their nominated pharmacy or update their registration status without requiring manual intervention in Choose Pharmacy

### Background
DMR registrations must be updatable when patients change their nominated pharmacy or when their registration status needs to be modified. The API must validate all changes against pharmacy reference data and maintain audit trails for compliance.

**DMR:** Discharge Medicines Review - a service providing pharmaceutical assessment and optimisation of medicines post-discharge.

### Objective
Deliver a secure PUT endpoint that updates existing DMR registration details with optimistic concurrency control, comprehensive validation, and full audit logging.

---

## Endpoint Details

**HTTP Method:** PUT  
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
PUT /api/v1/patients/1234567890/dmr-registration
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "nominatedPharmacyOdsCode": "W67890"
}
```

---

## Request Body

### Schema
```json
{
  "nominatedPharmacyOdsCode": {
    "type": "string",
    "required": false,
    "pattern": "^[A-Z][A-Z0-9]{4}$",
    "example": "W67890",
    "description": "ODS code of the new pharmacy to nominate (must be active and DMR-accredited). Omit to leave unchanged."
  }
}
```

### Notes on Request Body
- At least one updatable field must be provided
- Fields not included in the request are left unchanged
- `nominatedPharmacyOdsCode` is the only currently updatable field per FR3

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
    "odsCode": "W67890",
    "name": "New Example Pharmacy"
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

### Conflict Response (409) - Resource Modified
```json
{
  "type": "EntityModifiedException",
  "title": "EntityModifiedException",
  "status": 409,
  "detail": "DMR registration has been modified since it was last read. Please retrieve the current registration and retry."
}
```

### Not Found Response (404)
```json
{
  "type": "EntityNotFoundException",
  "title": "EntityNotFoundException",
  "status": 404,
  "detail": "Patient with NHS Number 1234567890 does not have an active DMR registration"
}
```

---

## Error Handling

| Status Code | Exception Type | Detail Message |
|---|---|---|
| 400 | ValidationException | NHS Number must be exactly 10 digits |
| 400 | ValidationException | The nominated pharmacy ODS code is invalid |
| 400 | ValidationException | At least one field must be provided for update |
| 401 | AuthenticationException | Invalid or expired authorization token |
| 403 | UnauthorizedAccessException | User does not have permission to update DMR registrations |
| 404 | EntityNotFoundException | Patient with NHS Number {nhsNumber} does not have an active DMR registration |
| 404 | EntityNotFoundException | Pharmacy with ODS code {odsCode} not found |
| 409 | EntityModifiedException | DMR registration has been modified since it was last read |
| 500 | Internal Server Error | An unexpected error occurred |

---

## Business Rules

1. **Active Registration Required:** Only active registrations can be updated; inactive registrations cannot be modified
2. **Pharmacy Validation:** If nominated pharmacy is provided:
   - Pharmacy must exist in national pharmacy reference data
   - Pharmacy must be active
   - Pharmacy must be accredited for DMR
3. **Concurrency Control:** Use optimistic locking (version/LastUpdated) to prevent lost updates
4. **Audit Logging:** All updates logged with caller, NHS Number, previous values, new values, timestamp
5. **Timestamp Update:** `lastUpdated` field is automatically set to current UTC timestamp

---

## Acceptance Criteria

**Total Scenarios: 8**

### Scenario 1: Successfully update nominated pharmacy
```gherkin
Given a patient with an active DMR registration
And a new valid pharmacy ODS code is provided
And the new pharmacy is active and accredited for DMR
When PUT /api/v1/patients/{nhsNumber}/dmr-registration is called
Then the API returns status 200 OK
And the response includes: nhsNumber, registered: true, registrationStatus: "Active"
And the response includes: nominatedPharmacy with new ODS code and name
And the lastUpdated timestamp reflects the current update time
And the registrationDate remains unchanged
And the DMR registration is persisted with the new pharmacy ODS code
And audit log records: timestamp, calling application, NHS Number, previous pharmacy, new pharmacy, action "Updated"
```

### Scenario 2: Cannot update with non-existent pharmacy
```gherkin
Given a patient with an active DMR registration
And a pharmacy ODS code that does not exist in reference data
When PUT /api/v1/patients/{nhsNumber}/dmr-registration is called with invalid ODS code
Then the API returns status 404 Not Found
And the response type is "EntityNotFoundException"
And the detail message is "Pharmacy with ODS code {odsCode} not found"
And no data is persisted
```

### Scenario 3: Cannot update with inactive pharmacy
```gherkin
Given a patient with an active DMR registration
And a pharmacy ODS code that exists but is marked as inactive
When PUT /api/v1/patients/{nhsNumber}/dmr-registration is called
Then the API returns status 400 Bad Request
And the response type is "ValidationException"
And the detail message is "The nominated pharmacy ODS code is invalid"
And the errors object contains: { "nominatedPharmacyOdsCode": ["The nominated pharmacy ODS code is invalid"] }
And no data is persisted
```

### Scenario 4: Cannot update with non-DMR-accredited pharmacy
```gherkin
Given a patient with an active DMR registration
And a pharmacy ODS code that is active but not accredited for DMR
When PUT /api/v1/patients/{nhsNumber}/dmr-registration is called
Then the API returns status 400 Bad Request
And the response type is "ValidationException"
And the detail message is "The nominated pharmacy ODS code is invalid"
And the errors object contains: { "nominatedPharmacyOdsCode": ["The nominated pharmacy ODS code is invalid"] }
And no data is persisted
```

### Scenario 5: Cannot update inactive registration
```gherkin
Given a patient with an inactive (deregistered) DMR registration
And a valid new pharmacy ODS code is provided
When PUT /api/v1/patients/{nhsNumber}/dmr-registration is called
Then the API returns status 404 Not Found
And the response type is "EntityNotFoundException"
And the detail message is "Patient with NHS Number {nhsNumber} does not have an active DMR registration"
And no data is persisted
```

### Scenario 6: Cannot update if registration modified concurrently
```gherkin
Given a patient with an active DMR registration that was last updated at timestamp T1
And a concurrent update has occurred, changing the registration at timestamp T2 (after T1)
When PUT /api/v1/patients/{nhsNumber}/dmr-registration is called with outdated context
Then the API returns status 409 Conflict
And the response type is "EntityModifiedException"
And the detail message is "DMR registration has been modified since it was last read. Please retrieve the current registration and retry."
And no data is persisted
```

### Scenario 7: Invalid NHS Number format
```gherkin
Given an invalid NHS Number is provided (not 10 digits)
When PUT /api/v1/patients/invalid-nhs-number/dmr-registration is called
Then the API returns status 400 Bad Request
And the response type is "ValidationException"
And the detail message is "NHS Number must be exactly 10 digits"
And the errors object contains: { "nhsNumber": ["NHS Number must be exactly 10 digits"] }
And no data is persisted
```

### Scenario 8: Patient not found
```gherkin
Given a request for a patient NHS Number that does not exist in the system
When PUT /api/v1/patients/9999999999/dmr-registration is called
Then the API returns status 404 Not Found
And the response type is "EntityNotFoundException"
And the detail message is "Patient with NHS Number 9999999999 does not have an active DMR registration"
And no data is persisted
```

---

## Security Requirements

**Authentication:** OAuth 2.0 / Azure Entra ID  
**Authorisation:** Caller must have `DMR Registration Update` scope  
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
| Audit Logging | All requests logged with patient identifier, timestamp, calling application, action, old/new values |

---

## Notes
- ODS code format: First character = letter, followed by 4 alphanumeric characters (e.g., W67890)
- lastUpdated timestamp set automatically to current UTC timestamp on successful update
- registrationDate (original date patient was registered) never changes on update
- Optimistic locking: Compare current lastUpdated with timestamp from last read; fail if mismatch
- All timestamps in ISO 8601 format with UTC timezone (Z suffix)
- Pharmacy validation must call national pharmacy reference data service
