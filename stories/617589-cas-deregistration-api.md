# API: PUT /api/v1/patients/{patientId}/service-registrations/cas - Automatically De-Register Inactive CAS Registration

## Story Type: API | Feature: 617589 | Effort: TBD

---

## Description

### Story Card
**As a** Choose Pharmacy housekeeping process (scheduled daily)
**I want** to de-register a patient's CAS registration when they have not received a CAS consultation in the previous 12 months
**So that** CAS registrations remain accurate and compliant with service policy, and the registration history is complete for audit and governance

### Background
Patients are registered for the Common Ailments Service (CAS). Registrations that have not been used for 12 months should be removed automatically to reduce manual administrative effort.

This story applies to **CAS registrations only**. DMR registrations are out of scope and must not be changed.

Legacy registrations held in the orphaned service registration table must also be reviewed. Once a legacy record has been migrated to a de-registration record in the new application's registration table, the orphaned record is removed. The new application's registration table becomes the single source of truth for CAS registration status.

### Objective
Provide an endpoint that records the automatic de-registration of a CAS registration, with the de-registration date and reason, and writes a complete audit trail. The scheduled housekeeping process calls this endpoint for each eligible patient.

---

## Endpoint Details

**HTTP Method:** PUT
**Endpoint:** `/api/v1/patients/{patientId}/service-registrations/cas`
**Full URL:** `https://api.example.com/api/v1/patients/{patientId}/service-registrations/cas`

### URL Parameters
- `{patientId}`: [string, required, UUID format] - Choose Pharmacy patient record identifier

### Request Headers
```
Authorization: ******
Content-Type: application/json
Accept: application/json
```

---

## Request

### Request Body
```json
{
  "status": "deregistered",
  "reason": "No CAS consultation within 12 months",
  "deregisteredDateTime": "2026-11-02T06:00:00Z",
  "lastConsultationDate": "2025-09-30"
}
```

**Fields:**
- `status`: [string, required, enum: `deregistered`] - Target registration status
- `reason`: [string, required, max 200 characters] - Reason for de-registration. Default value: `No CAS consultation within 12 months`
- `deregisteredDateTime`: [date-time, required] - Date and time of the automatic de-registration
- `lastConsultationDate`: [date, optional] - Date of the patient's most recent CAS consultation, as determined by the housekeeping process

### Request Example
```
PUT /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations/cas
Authorization: ******
Content-Type: application/json
```

---

## Response

### Success Response (200 OK)
```json
{
  "patientId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "serviceType": "cas",
  "status": "deregistered",
  "deregisteredDateTime": "2026-11-02T06:00:00Z",
  "reason": "No CAS consultation within 12 months"
}
```

---

## Error Handling

Errors use the ProblemDetails format defined in [API-ERROR-HANDLING.md](../standards/API-ERROR-HANDLING.md).

| Status Code | Exception Type | Description |
|-------------|----------------|-------------|
| 400 | ValidationException | Request body fails validation |
| 401 | AuthenticationException | Missing or invalid authorization token |
| 403 | UnauthorizedAccessException | Caller lacks permission to change service registrations |
| 404 | EntityNotFoundException | Patient or CAS registration not found |
| 409 | EntityModifiedException | Registration was modified since it was last read |
| 500 | (unmapped) | Unexpected error or database failure |

---

## Acceptance Criteria

### Scenario 1: De-register CAS registration with no consultation in 12 months
```gherkin
Given a patient has an active CAS registration
And the patient has no CAS consultation in the previous 12 months
When PUT /api/v1/patients/{patientId}/service-registrations/cas is called with status "deregistered"
Then the API returns status 200
And the CAS registration status is "deregistered"
And the de-registration date and time is recorded against the patient's service registration
And the reason is recorded as "No CAS consultation within 12 months"
```

### Scenario 2: Do not de-register when a CAS consultation exists within 12 months
```gherkin
Given a patient has an active CAS registration
And the patient had a CAS consultation 6 months ago
When the housekeeping process evaluates the patient's CAS registration
Then the housekeeping process does not call the de-registration endpoint for the patient
And the CAS registration status remains "active"
```

### Scenario 3: Do not de-register DMR registrations
```gherkin
Given a patient has an active DMR registration
And the patient has no CAS consultation in the previous 12 months
When the housekeeping process runs
Then the DMR registration status remains unchanged
And no de-registration is recorded for the DMR registration
```

### Scenario 4: Migrate orphaned CAS registration to the new registration table
```gherkin
Given a patient has an orphaned CAS registration record
And the last CAS consultation in the orphaned record is more than 12 months ago
And the patient has no more recent CAS consultation in the new application
When the housekeeping process runs
Then a de-registration record is created in the new application's registration table
And the reason is recorded as "No CAS consultation within 12 months"
And the orphaned CAS registration record is removed
And the new registration record is the single source of truth for the patient's CAS status
```

### Scenario 5: Retain orphaned record when de-registration record cannot be created
```gherkin
Given a patient has an orphaned CAS registration record
And the patient is eligible for automatic de-registration
And creating the de-registration record in the new application's registration table fails
When the housekeeping process runs
Then the orphaned CAS registration record is not removed
And the failure is logged for investigation
```

### Scenario 6: Do not de-register orphaned record with a more recent consultation
```gherkin
Given a patient has an orphaned CAS registration record
And the last CAS consultation in the orphaned record is more than 12 months ago
And the patient has a CAS consultation in the new application within the previous 12 months
When the housekeeping process runs
Then no de-registration record is created
And the orphaned CAS registration record is not removed
```

### Scenario 7: Record audit trail for automatic de-registration
```gherkin
Given a patient's CAS registration has been automatically de-registered
When GET /api/v1/patients/{patientId}/service-registrations/audit-trail?serviceType=cas is called
Then the API returns status 200
And the audit trail includes the de-registration date and time
And the audit trail includes the reason "No CAS consultation within 12 months"
And the audit trail includes the status change from "active" to "deregistered"
```

### Scenario 8: Repeat run does not duplicate de-registration
```gherkin
Given a patient's CAS registration was automatically de-registered on a previous run
When PUT /api/v1/patients/{patientId}/service-registrations/cas is called with status "deregistered"
Then the API returns status 200
And the registration status remains "deregistered"
And no duplicate audit trail entry is created
```

### Scenario 9: Validation failure for invalid patient identifier
```gherkin
Given an invalid patient identifier (not UUID format)
When PUT /api/v1/patients/invalid-id/service-registrations/cas is called
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Invalid patient ID format. Expected UUID."
And no data is persisted
```

### Scenario 10: Validation failure for invalid status value
```gherkin
Given a valid patient identifier
When PUT /api/v1/patients/{patientId}/service-registrations/cas is called with status "active"
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "One or more validation errors occurred."
And the errors object contains: { "status": ["Status must be 'deregistered' for this endpoint"] }
And no data is persisted
```

### Scenario 11: Validation failure for missing reason
```gherkin
Given a valid patient identifier
When PUT /api/v1/patients/{patientId}/service-registrations/cas is called without a reason
Then the API returns status 400
And the response type is "ValidationException"
And the errors object contains: { "reason": ["Reason is required"] }
And no data is persisted
```

### Scenario 12: Patient not found
```gherkin
Given a patient identifier that does not exist
When PUT /api/v1/patients/{patientId}/service-registrations/cas is called
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message contains the patient ID that was not found
And no data is persisted
```

### Scenario 13: CAS registration not found
```gherkin
Given a patient exists with no CAS registration
When PUT /api/v1/patients/{patientId}/service-registrations/cas is called
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message is "CAS registration not found for patient {patientId}"
And no data is persisted
```

### Scenario 14: Unauthenticated request
```gherkin
Given the request has no valid authorization token
When PUT /api/v1/patients/{patientId}/service-registrations/cas is called
Then the API returns status 401
And the response type is "AuthenticationException"
And no data is persisted
```

### Scenario 15: Caller lacks permission
```gherkin
Given the caller is authenticated but does not have permission to change service registrations
When PUT /api/v1/patients/{patientId}/service-registrations/cas is called
Then the API returns status 403
And the response type is "UnauthorizedAccessException"
And no data is persisted
```

---

## Out of Scope
- The scheduling mechanism for the daily housekeeping process
- De-registration of DMR registrations
- Reporting screens for automatically de-registered patients (covered in a separate report story)
- Re-registration of a patient after automatic de-registration
- Notification to patients or pharmacies about de-registration

---

## Notes
- Overlaps with [617647-patient-registrations-api.md](617647-patient-registrations-api.md), which defines the `service-registrations` resource group. Align the `/service-registrations/cas` path and audit-trail endpoint with that story during review.
- Feature 617589 does not define a user-facing endpoint. This API is proposed as the mechanism the housekeeping process uses to record de-registrations. Confirm with the business owner before development.
