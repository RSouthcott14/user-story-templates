# API: POST /api/v1/patients/{patientId}/discharge-advice-letters - Notify New DAL

## Story Type: API | Feature: 606819 | Effort: 13 hours

---

## Description

### Story Card
**As an** External System (MTED Integration)  
**I want** to notify Choose Pharmacy when a Discharge Advice Letter (DAL) is available for a patient  
**So that** the patient's assigned pharmacy can access and process the DAL for Discharge Medicines Review

### Background
The Discharge Medicines Review (DMR) service requires community pharmacists to access electronic DALs that are generated when patients are discharged from hospital. Currently, the MTED Integration Services application sends DALs to GP practices. This API extends that existing integration to also notify Choose Pharmacy when a patient has a new DAL.

The integration must be **backward-compatible** — external systems using the existing Choose Pharmacy application endpoint should not need to change their implementation. This endpoint receives notifications in the same format as the current "Notify of new Discharge Advice Letter" API, following the architecture defined in SAD-DMR v1.1 (SUC-DMR-INSE01).

### Objective
Implement a RESTful API endpoint that accepts DAL notifications from external systems and creates a notification record in Choose Pharmacy's database. The endpoint must validate patient identity, DAL reference, and pharmacy registration status before recording the notification.

### Integration Pattern
- **Sender:** MTED Integration Services (InSe) Adaptor
- **Protocol:** HTTP POST with JSON body
- **Authentication:** Azure AD service-to-service or API key (TBD)
- **Notification Trigger:** Patient discharged + DAL/Medicines workflow complete in MTED
- **Expected Behavior:** Choose Pharmacy records the notification; pharmacy is notified separately via ENAS email (out of scope for this API)

---

## Endpoint Details

**HTTP Method:** POST  
**Endpoint:** `/api/v1/patients/{patientId}/discharge-advice-letters`  
**Full URL:** `https://api.example.com/api/v1/patients/{patientId}/discharge-advice-letters`

### URL Parameters
- `{patientId}`: [string, required, UUID format] - Choose Pharmacy patient record identifier

### Request Headers
```
Authorization: Bearer <token> or X-API-Key: <api-key>
Content-Type: application/json
Accept: application/json
```

---

## Request

### Request Body

The external system sends the NHS Number, NDR Document Supersession Set ID, and optional metadata:

```json
{
  "nhsNumber": "9123456789",
  "ndrDocumentSupersessionSetId": "550e8400-e29b-41d4-a716-446655440000",
  "hospitalCode": "W140",
  "sentDateTime": "2026-11-02T10:30:00Z"
}
```

**Field Definitions:**
- `nhsNumber`: [string, required] - NHS Number of the patient (10 digits, must be valid format)
- `ndrDocumentSupersessionSetId`: [string, required, UUID] - Unique identifier of the DAL in NDR (National Document Repository)
- `hospitalCode`: [string, optional] - ODS code of the hospital where discharge occurred
- `sentDateTime`: [string, optional, ISO 8601] - Timestamp when the notification was sent by external system; defaults to current server time if omitted

### Request Validation Rules
- NHS Number must be exactly 10 digits and pass the NHS Number check digit algorithm (Modulus 11)
- `ndrDocumentSupersessionSetId` must be a valid UUID
- `hospitalCode` (if provided) must match a valid ODS Code in the reference data
- `sentDateTime` (if provided) must not be in the future (> current server time + 5 minutes tolerance)

### Request Example
```
POST /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/discharge-advice-letters
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "nhsNumber": "9123456789",
  "ndrDocumentSupersessionSetId": "550e8400-e29b-41d4-a716-446655440000",
  "hospitalCode": "W140",
  "sentDateTime": "2026-11-02T10:30:00Z"
}
```

---

## Response

### Success Response (201 Created)

The notification has been recorded successfully. The response includes the notification reference and patient's current DMR status.

```json
{
  "notificationId": "605e8400-e29b-41d4-a716-446655440111",
  "patientId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "nhsNumber": "9123456789",
  "ndrDocumentSupersessionSetId": "550e8400-e29b-41d4-a716-446655440000",
  "recordedDateTime": "2026-11-02T10:35:22Z",
  "dmrStatus": {
    "isRegisteredForDmr": true,
    "registeredPharmacyId": "605514G",
    "registeredPharmacyName": "Boots - Queen Street",
    "pharmacyDmrEnabled": true,
    "lastNotificationDateTime": "2026-11-02T10:35:22Z"
  }
}
```

**Field Definitions:**
- `notificationId`: [string, UUID] - Unique identifier for this notification record in Choose Pharmacy
- `patientId`: [string, UUID] - The Choose Pharmacy patient record identifier (from URL parameter)
- `nhsNumber`: [string] - Echoed NHS Number (for confirmation)
- `ndrDocumentSupersessionSetId`: [string, UUID] - Echoed NDR document ID
- `recordedDateTime`: [string, ISO 8601] - Server timestamp when notification was recorded
- `dmrStatus`: [object] - Patient's current DMR registration status
  - `isRegisteredForDmr`: [boolean] - Whether patient is registered for DMR service
  - `registeredPharmacyId`: [string] - ODS/AccNum of registered pharmacy (null if not registered)
  - `registeredPharmacyName`: [string] - Display name of registered pharmacy (null if not registered)
  - `pharmacyDmrEnabled`: [boolean] - Whether registered pharmacy is enabled for electronic DMRs (null if not registered)
  - `lastNotificationDateTime`: [string, ISO 8601] - Timestamp of most recent DAL notification for this patient

**HTTP Status:** `201 Created`

**Response Headers:**
```
Content-Type: application/json
Cache-Control: private, no-cache
Location: /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/discharge-advice-letters/605e8400-e29b-41d4-a716-446655440111
```

---

## Error Responses

### 400 Bad Request - Validation Error

Returned when request data fails validation.

```json
{
  "type": "ValidationException",
  "title": "ValidationException",
  "status": 400,
  "detail": "One or more validation errors occurred.",
  "errors": {
    "nhsNumber": ["NHS Number must be exactly 10 digits"],
    "ndrDocumentSupersessionSetId": ["Invalid UUID format"]
  }
}
```

**Validation Error Messages:**
- NHS Number: `"NHS Number must be exactly 10 digits"`
- NHS Number: `"NHS Number failed check digit validation"`
- NDR Document ID: `"Invalid UUID format"`
- Hospital Code: `"Hospital code W999 is not recognized"`
- Sent DateTime: `"Timestamp cannot be in the future"`

**HTTP Status:** `400 Bad Request`

### 401 Unauthorized

Returned when authentication credentials are missing or invalid.

```json
{
  "type": "AuthenticationException",
  "title": "AuthenticationException",
  "status": 401,
  "detail": "Invalid or expired authorization token"
}
```

**HTTP Status:** `401 Unauthorized`

### 403 Forbidden

Returned when the authenticated caller lacks permission to submit DAL notifications.

```json
{
  "type": "UnauthorizedAccessException",
  "title": "UnauthorizedAccessException",
  "status": 403,
  "detail": "Caller does not have permission to submit discharge advice letter notifications"
}
```

**HTTP Status:** `403 Forbidden`

### 404 Not Found - Patient Not Found

Returned when the patient record identified by `{patientId}` does not exist.

```json
{
  "type": "EntityNotFoundException",
  "title": "EntityNotFoundException",
  "status": 404,
  "detail": "Patient with ID 3fa85f64-5717-4562-b3fc-2c963f66afa6 not found"
}
```

**HTTP Status:** `404 Not Found`

### 409 Conflict - Duplicate Notification

Returned when a notification for the same NDR Document ID has already been recorded for this patient within the last 24 hours (idempotency window).

```json
{
  "type": "EntityAlreadyExistsException",
  "title": "EntityAlreadyExistsException",
  "status": 409,
  "detail": "Notification for DAL 550e8400-e29b-41d4-a716-446655440000 already recorded at 2026-11-02T09:15:00Z"
}
```

**HTTP Status:** `409 Conflict`

**Note:** If the same request is retried within the idempotency window, return `200 OK` with the original notification record (idempotent behavior). Return `409 Conflict` only if a **different** request attempts to create a second notification for the same DAL.

### 500 Internal Server Error

Returned when an unexpected server error occurs.

```json
{
  "type": "Exception",
  "title": "Exception",
  "status": 500,
  "detail": "An unexpected error occurred while processing the notification"
}
```

**HTTP Status:** `500 Internal Server Error`

---

## Acceptance Criteria

**Total Scenarios: 10**

### Page Structure & Content

**Scenario 1: Successful Notification Recording**

```gherkin
Given an authenticated external system
And a valid patient record exists with patientId
And the patient is registered for DMR
And a valid DAL notification request is provided
When POST /api/v1/patients/{patientId}/discharge-advice-letters is called
Then the API returns status 201
And the response includes: notificationId, patientId, ndrDocumentSupersessionSetId, recordedDateTime
And the response includes: dmrStatus with isRegisteredForDmr=true
And the notification is persisted to the database
```

**Scenario 2: DMR Status Included in Response**

```gherkin
Given a DAL notification has been successfully recorded
When POST /api/v1/patients/{patientId}/discharge-advice-letters is called
Then the response includes dmrStatus object
And dmrStatus.isRegisteredForDmr matches the patient's registration status
And dmrStatus.registeredPharmacyId matches the patient's assigned pharmacy
And dmrStatus.pharmacyDmrEnabled indicates whether that pharmacy is enabled for eDMR
```

### Default State

**Scenario 3: Notification without Optional Fields**

```gherkin
Given a notification request with only required fields (nhsNumber, ndrDocumentSupersessionSetId)
When POST /api/v1/patients/{patientId}/discharge-advice-letters is called
Then the API returns status 201
And the response recordedDateTime is set to current server time
And the hospitalCode field is not required
And no data is persisted if validation fails
```

### User Interaction

**Scenario 4: Idempotent Retry within 24 Hours**

```gherkin
Given a DAL notification was recorded at 2026-11-02T10:00:00Z
When the exact same request is sent again within 24 hours (e.g., 2026-11-02T14:00:00Z)
Then the API returns status 200
And the response contains the original notificationId
And the recordedDateTime reflects the first submission, not the retry
And only one database record exists (no duplicate)
```

**Scenario 5: Conflict on Different Request, Same DAL**

```gherkin
Given a DAL notification was recorded for ndrDocumentSupersessionSetId=X at 2026-11-02T10:00:00Z
When a different request is sent with the same ndrDocumentSupersessionSetId but different metadata
And the second request is sent within 24 hours
Then the API returns status 409
And the detail message is "Notification for DAL X already recorded at 2026-11-02T10:00:00Z"
And no data is persisted
```

### Validation

**Scenario 6: Invalid NHS Number Format**

```gherkin
Given a request with an invalid NHS Number (not 10 digits or fails check digit)
When POST /api/v1/patients/{patientId}/discharge-advice-letters is called with nhsNumber="123"
Then the API returns status 400
And the response type is "ValidationException"
And the errors.nhsNumber includes "NHS Number must be exactly 10 digits"
And no data is persisted
```

**Scenario 7: Invalid NDR Document ID**

```gherkin
Given a request with ndrDocumentSupersessionSetId not in UUID format
When POST /api/v1/patients/{patientId}/discharge-advice-letters is called with ndrDocumentSupersessionSetId="not-a-uuid"
Then the API returns status 400
And the response type is "ValidationException"
And the errors.ndrDocumentSupersessionSetId includes "Invalid UUID format"
And no data is persisted
```

**Scenario 8: Future Timestamp**

```gherkin
Given a request with sentDateTime in the future (> current time + 5 minutes)
When POST /api/v1/patients/{patientId}/discharge-advice-letters is called with sentDateTime="2099-01-01T00:00:00Z"
Then the API returns status 400
And the response type is "ValidationException"
And the errors.sentDateTime includes "Timestamp cannot be in the future"
And no data is persisted
```

### Navigation

**Scenario 9: Patient Not Found**

```gherkin
Given a request for a patient that does not exist
When POST /api/v1/patients/00000000-0000-0000-0000-000000000000/discharge-advice-letters is called
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message contains the patientId that was not found
And no data is persisted
```

### Accessibility

**Scenario 10: Authentication Required**

```gherkin
Given a request without an Authorization header
When POST /api/v1/patients/{patientId}/discharge-advice-letters is called
Then the API returns status 401
And the response type is "AuthenticationException"
And the detail message is "Invalid or expired authorization token"
And no data is persisted
```

---

## Out of Scope

- **Pharmacy notification via ENAS email:** Email notifications are handled separately by the pharmacy notification service (not this API)
- **DAL retrieval from NDR:** This API does not fetch or validate DAL content from the National Document Repository
- **Patient matching:** The API assumes the patient record already exists in Choose Pharmacy; it does not create new records or perform MPI (Master Patient Index) lookups
- **Consent validation:** This API does not validate patient consent; consent verification is assumed to have occurred during the MTED discharge process
- **DAL viewing by pharmacist:** Frontend/UI functionality for pharmacists to view DALs is out of scope
- **WebSocket/real-time updates:** This API is request-response only; real-time notification delivery is handled by separate mechanisms

---

## Related and Dependent Stories

### Related Stories
- Consultation/pharmacy notification service that reads DAL notifications and sends ENAS emails to registered pharmacies — relates to: This API records the notification; the notification service consumes these records
- UI story for Pharmacist DAL Inbox — relates to: Pharmacists view DALs that were notified via this API

### Dependent Stories
- Feature 606819 (Discharge Medicines Review Integration) — blocks: This API is a prerequisite for MTED Integration to notify Choose Pharmacy of new DALs

---

## Definition of Done

- [ ] Endpoint implemented per specification
- [ ] URL path follows API-PATH-CONVENTIONS (patient-scoped, lowercase, kebab-case)
- [ ] Request validation implemented (NHS Number format, UUID format, timestamp validation)
- [ ] Response format follows API-RESPONSE-PATTERNS (201 Created, includes dmrStatus)
- [ ] Error handling implemented per API-ERROR-HANDLING (400, 401, 403, 404, 409, 500)
- [ ] Idempotency implemented (24-hour window, retry returns 200 with original record)
- [ ] All acceptance criteria met (10 scenarios passing)
- [ ] Request/response validation working with exact error messages
- [ ] Authentication/authorization verified (Azure AD or API key)
- [ ] Unit tests passing (80%+ coverage, including validation and edge cases)
- [ ] Integration tests passing (database persistence, idempotency)
- [ ] Database schema supports: notificationId, patientId, nhsNumber, ndrDocumentSupersessionSetId, hospitalCode, recordedDateTime, lastNotificationDateTime
- [ ] Performance benchmarks met (< 200ms response time, 95th percentile)
- [ ] API documentation generated (OpenAPI/Swagger)
- [ ] Ready for integration with MTED InSe Adaptor

---

## Technical Notes

### Database Schema
The `DalNotifications` table (or similar) must store:
- `NotificationId` [GUID] — Primary key
- `PatientId` [GUID] — Foreign key to Patient table
- `NhsNumber` [varchar(10)] — NHS Number
- `NdrDocumentSupersessionSetId` [GUID] — Unique DAL reference
- `HospitalCode` [varchar(10)] — Optional ODS code
- `SentDateTime` [datetime] — External system timestamp (nullable)
- `RecordedDateTime` [datetime] — Server timestamp (now())
- `CreatedDateTime` [datetime] — For audit trail
- `UpdatedDateTime` [datetime] — For audit trail
- **Unique constraint:** (PatientId, NdrDocumentSupersessionSetId) to detect duplicates within idempotency window

### Idempotency Implementation
- Check for existing notification with same (PatientId, NdrDocumentSupersessionSetId) within last 24 hours
- If exact match found: return 200 OK with existing notification
- If different request (different metadata) for same DAL: return 409 Conflict
- Use request hash or request body comparison to detect "same" vs "different" requests

### DMR Status Query
The `dmrStatus` object in the response must query:
- Patient's DMR registration record (if exists)
- Associated pharmacy's details from NRDS (or local cache)
- Pharmacy's `DMRenabled` flag (from NRDS CASpharmacyDMRenabled view or similar)

---

## Related Standards and Documentation

- [API-PATH-CONVENTIONS.md](../standards/API-PATH-CONVENTIONS.md) — URL structure and naming
- [API-RESPONSE-PATTERNS.md](../standards/API-RESPONSE-PATTERNS.md) — Success response format
- [API-ERROR-HANDLING.md](../standards/API-ERROR-HANDLING.md) — Error response structure
- **SAD-DMR v1.1** — Architecture reference, Section 5.3 "DMR registration Service API" and Section 6.2 "Notify of new Discharge Advice Letter"

---

## Notes

- This API replaces the legacy "Notify of new Discharge Advice Letter" endpoint mentioned in the SAD-DMR. External systems continue using the same integration flow with no required changes to their implementation.
- The NHS Number is transmitted in the request body for redundancy; it should match the patient record identified by `{patientId}`. Mismatch validation is recommended but not strictly required (implementation detail).
- The response includes `dmrStatus` to provide feedback to the external system about whether the notification will be actionable (i.e., patient is registered for DMR and their pharmacy is enabled).
- Future enhancement: Implement webhook callbacks to notify the external system when the pharmacy has viewed or processed the DAL.
