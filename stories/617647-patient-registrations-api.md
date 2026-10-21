# API: Patient Service Registration Management

## Story Type: API | Feature: 617640 | Effort: 13 hours

---

## Description

### Story Card
**As a** Clinical System  
**I want** to retrieve and manage a patient's service registrations (CAS and DMR)  
**So that** the Patient Management Hub can display current registration status and allow pharmacy users to register or deregister patients from supported pharmacy services

### Background
The Patient Management Hub requires a backend API to support the patient service registration management feature. Pharmacy users need to view, register, and deregister patients from the Common Ailments Service (CAS) and Discharge Medicines Review (DMR). The API must maintain an audit trail of all registration changes and provide real-time access to current registration status.

### Objective
Deliver a set of REST endpoints that support:
- Retrieving current service registrations for a patient
- Registering a patient for a service
- Deregistering a patient from a service
- Retrieving registration history/audit trail

---

## Endpoint Details

### Endpoint 1: Get Patient Service Registrations

**HTTP Method:** GET  
**Endpoint:** `/api/v1/patients/{patientId}/service-registrations`  
**Full URL:** `https://api.example.com/api/v1/patients/{patientId}/service-registrations`

#### Query Parameters
- `serviceType`: [string, optional] - Filter by service type (e.g., `cas`, `dmr`). If omitted, all active registrations are returned.

#### Request
```
GET /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations
Authorization: ******
Content-Type: application/json
```

#### Response (200 OK)
```json
{
  "registrations": [
    {
      "serviceType": "cas",
      "registrationStatus": "registered",
      "registrationDate": "2026-09-15T10:30:00Z",
      "registeredBy": "PHAR001",
      "currentPharmacy": "PHM789"
    },
    {
      "serviceType": "dmr",
      "registrationStatus": "not-registered",
      "registrationDate": null,
      "registeredBy": null,
      "currentPharmacy": null
    }
  ]
}
```

#### Fields
- `serviceType`: [string] — Service identifier (e.g., `cas`, `dmr`)
- `registrationStatus`: [string] — Either `registered` or `not-registered`
- `registrationDate`: [datetime, nullable] — ISO 8601 timestamp of registration or null if not registered
- `registeredBy`: [string, nullable] — User identifier who registered the patient or null
- `currentPharmacy`: [string, nullable] — Pharmacy ODS/account number or null if not registered

---

### Endpoint 2: Register Patient for Service

**HTTP Method:** POST  
**Endpoint:** `/api/v1/patients/{patientId}/service-registrations`  
**Full URL:** `https://api.example.com/api/v1/patients/{patientId}/service-registrations`

#### Request
```
POST /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations
Authorization: ******
Content-Type: application/json

{
  "serviceType": "cas",
  "registrationDate": "2026-10-21T14:30:00Z",
  "registeredBy": "PHAR001",
  "currentPharmacy": "PHM789"
}
```

#### Request Body
- `serviceType`: [string, required] — Service to register for (e.g., `cas`, `dmr`)
- `registrationDate`: [datetime, required] — ISO 8601 timestamp of registration
- `registeredBy`: [string, required] — User identifier registering the patient
- `currentPharmacy`: [string, required] — Pharmacy ODS/account number

#### Response (201 Created)
```json
{
  "id": "reg-550e8400-e29b-41d4-a716-446655440000",
  "patientId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "serviceType": "cas",
  "registrationStatus": "registered",
  "registrationDate": "2026-10-21T14:30:00Z",
  "registeredBy": "PHAR001",
  "currentPharmacy": "PHM789",
  "createdDateTime": "2026-10-21T14:30:00Z"
}
```

---

### Endpoint 3: Deregister Patient from Service

**HTTP Method:** DELETE  
**Endpoint:** `/api/v1/patients/{patientId}/service-registrations/{serviceType}`  
**Full URL:** `https://api.example.com/api/v1/patients/{patientId}/service-registrations/cas`

#### Request
```
DELETE /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations/cas
Authorization: ******
Content-Type: application/json

{
  "deregistrationDate": "2026-10-21T14:45:00Z",
  "deregisteredBy": "PHAR001"
}
```

#### Request Body
- `deregistrationDate`: [datetime, required] — ISO 8601 timestamp of deregistration
- `deregisteredBy`: [string, required] — User identifier deregistering the patient

#### Response (200 OK)
```json
{
  "patientId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "serviceType": "cas",
  "registrationStatus": "not-registered",
  "deregistrationDate": "2026-10-21T14:45:00Z",
  "deregisteredBy": "PHAR001",
  "updatedDateTime": "2026-10-21T14:45:00Z"
}
```

---

### Endpoint 4: Get Registration Audit Trail

**HTTP Method:** GET  
**Endpoint:** `/api/v1/patients/{patientId}/service-registrations/audit-trail`  
**Full URL:** `https://api.example.com/api/v1/patients/{patientId}/service-registrations/audit-trail`

#### Query Parameters
- `serviceType`: [string, optional] — Filter by service type (e.g., `cas`, `dmr`). If omitted, all audit records are returned.
- `skip`: [integer, optional, default: 0] — Number of records to skip for pagination
- `limit`: [integer, optional, default: 50, max: 200] — Maximum records to return

#### Request
```
GET /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations/audit-trail?serviceType=cas&skip=0&limit=50
Authorization: ******
```

#### Response (200 OK)
```json
{
  "items": [
    {
      "id": "audit-1",
      "patientId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
      "serviceType": "cas",
      "action": "register",
      "actionDate": "2026-10-21T14:30:00Z",
      "actionBy": "PHAR001",
      "pharmacy": "PHM789",
      "previousStatus": "not-registered",
      "newStatus": "registered"
    },
    {
      "id": "audit-2",
      "patientId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
      "serviceType": "cas",
      "action": "deregister",
      "actionDate": "2026-10-21T14:45:00Z",
      "actionBy": "PHAR001",
      "pharmacy": "PHM789",
      "previousStatus": "registered",
      "newStatus": "not-registered"
    }
  ],
  "total": 2,
  "skip": 0,
  "limit": 50
}
```

#### Fields
- `id`: [string] — Unique audit record identifier
- `patientId`: [string] — Patient identifier
- `serviceType`: [string] — Service identifier (e.g., `cas`, `dmr`)
- `action`: [string] — Action type (`register` or `deregister`)
- `actionDate`: [datetime] — ISO 8601 timestamp of the action
- `actionBy`: [string] — User identifier who performed the action
- `pharmacy`: [string] — Pharmacy ODS/account number
- `previousStatus`: [string] — Status before action (`registered` or `not-registered`)
- `newStatus`: [string] — Status after action (`registered` or `not-registered`)

---

## Error Handling

All errors follow the [API-ERROR-HANDLING.md](../standards/API-ERROR-HANDLING.md) standard.

### Common Error Scenarios

| Status Code | Error | Description |
|---|---|---|
| 400 | INVALID_SERVICE_TYPE | Service type not supported (must be `cas` or `dmr`) |
| 400 | INVALID_REQUEST_DATA | Required fields missing or invalid format |
| 401 | UNAUTHORIZED | Invalid or expired authorization token |
| 403 | FORBIDDEN | User lacks permission to manage registrations |
| 404 | PATIENT_NOT_FOUND | Patient with the specified ID not found |
| 409 | ALREADY_REGISTERED | Patient is already registered for the service |
| 409 | NOT_REGISTERED | Patient is not registered for the service |
| 500 | SERVER_ERROR | Unexpected server error |

---

## Acceptance Criteria

**Total Scenarios: 15**

### Get Current Registrations

**Scenario 1: Retrieve all service registrations for a patient**

```gherkin
Given a patient with ID "3fa85f64-5717-4562-b3fc-2c963f66afa6" exists
And the patient has an active CAS registration
And the patient does not have a DMR registration
When GET /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations is called
Then the API returns status 200
And the response contains an array of registrations with 2 items (one for each service type)
And the first registration has: serviceType = "cas", registrationStatus = "registered", registrationDate is present
And the second registration has: serviceType = "dmr", registrationStatus = "not-registered", registrationDate is null
And all registrations include: currentPharmacy, registeredBy fields (null where not registered)
```

**Scenario 2: Filter registrations by service type**

```gherkin
Given a patient with ID "3fa85f64-5717-4562-b3fc-2c963f66afa6" exists
And the patient has registrations for both CAS and DMR
When GET /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations?serviceType=cas is called
Then the API returns status 200
And the response contains only registrations with serviceType = "cas"
And the response array contains exactly 1 item
```

**Scenario 3: Patient not found**

```gherkin
Given a patient with ID "nonexistent-id" does not exist
When GET /api/v1/patients/nonexistent-id/service-registrations is called
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message contains the patient ID that was not found
And no data is persisted
```

**Scenario 4: Invalid query parameter**

```gherkin
Given a valid patient ID is provided
When GET /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations?serviceType=invalid is called
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Service type must be either 'cas' or 'dmr'"
And no data is persisted
```

### Register Patient for Service

**Scenario 5: Successfully register patient for a service**

```gherkin
Given a patient with ID "3fa85f64-5717-4562-b3fc-2c963f66afa6" exists
And the patient is not currently registered for CAS
And the current user has permission to manage registrations
When POST /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations is called with:
- serviceType = "cas"
- registrationDate = "2026-10-21T14:30:00Z"
- registeredBy = "PHAR001"
- currentPharmacy = "PHM789"
Then the API returns status 201
And the response includes: id, patientId, serviceType, registrationStatus = "registered"
And the response includes: registrationDate, registeredBy, currentPharmacy, createdDateTime
And the registration is persisted in the database
And an audit record is created with action = "register"
```

**Scenario 6: Cannot register patient already registered for service (BR1 violation)**

```gherkin
Given a patient with ID "3fa85f64-5717-4562-b3fc-2c963f66afa6" exists
And the patient is already registered for CAS
When POST /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations is called with:
- serviceType = "cas"
- registrationDate = "2026-10-21T14:30:00Z"
- registeredBy = "PHAR001"
- currentPharmacy = "PHM789"
Then the API returns status 409
And the response type is "EntityAlreadyExistsException"
And the detail message is "Patient is already registered for CAS service"
And no data is persisted
```

**Scenario 7: Missing required field in registration request**

```gherkin
Given a valid patient ID is provided
When POST /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations is called without the "serviceType" field
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "One or more validation errors occurred."
And the errors object contains: { "serviceType": ["The serviceType field is required"] }
And no data is persisted
```

**Scenario 8: Invalid service type in registration request**

```gherkin
Given a valid patient ID is provided
When POST /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations is called with:
- serviceType = "invalid"
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Service type must be either 'cas' or 'dmr'"
And no data is persisted
```

**Scenario 9: User lacks permission to register patient**

```gherkin
Given a patient with ID "3fa85f64-5717-4562-b3fc-2c963f66afa6" exists
And the current user does not have permission to manage registrations
When POST /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations is called
Then the API returns status 403
And the response type is "UnauthorizedAccessException"
And the detail message is "User does not have permission to manage patient registrations"
And no data is persisted
```

**Scenario 10: Patient not found during registration**

```gherkin
Given a patient with ID "nonexistent-id" does not exist
When POST /api/v1/patients/nonexistent-id/service-registrations is called
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message contains the patient ID that was not found
And no data is persisted
```

### Deregister Patient from Service

**Scenario 11: Successfully deregister patient from a service**

```gherkin
Given a patient with ID "3fa85f64-5717-4562-b3fc-2c963f66afa6" exists
And the patient is currently registered for CAS
And the current user has permission to manage registrations
When DELETE /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations/cas is called with:
- deregistrationDate = "2026-10-21T14:45:00Z"
- deregisteredBy = "PHAR001"
Then the API returns status 200
And the response includes: patientId, serviceType, registrationStatus = "not-registered"
And the response includes: deregistrationDate, deregisteredBy, updatedDateTime
And the registration status is updated in the database
And an audit record is created with action = "deregister"
```

**Scenario 12: Cannot deregister patient not registered for service**

```gherkin
Given a patient with ID "3fa85f64-5717-4562-b3fc-2c963f66afa6" exists
And the patient is not registered for DMR
When DELETE /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations/dmr is called
Then the API returns status 409
And the response type is "EntityModifiedException"
And the detail message is "Patient is not registered for DMR service"
And no data is persisted
```

**Scenario 13: Missing required field in deregistration request**

```gherkin
Given a valid patient ID and service type are provided
When DELETE /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations/cas is called without the "deregisteredBy" field
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "One or more validation errors occurred."
And the errors object contains: { "deregisteredBy": ["The deregisteredBy field is required"] }
And no data is persisted
```

### Audit Trail

**Scenario 14: Retrieve complete audit trail for patient**

```gherkin
Given a patient with ID "3fa85f64-5717-4562-b3fc-2c963f66afa6" exists
And the patient has 5 registration audit records
When GET /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations/audit-trail?skip=0&limit=50 is called
Then the API returns status 200
And the response includes: items array with all audit records
And the response includes: total = 5, skip = 0, limit = 50
And each audit record includes: id, patientId, serviceType, action, actionDate, actionBy, pharmacy, previousStatus, newStatus
```

**Scenario 15: Filter audit trail by service type with pagination**

```gherkin
Given a patient with ID "3fa85f64-5717-4562-b3fc-2c963f66afa6" exists
And the patient has audit records for both CAS and DMR
When GET /api/v1/patients/3fa85f64-5717-4562-b3fc-2c963f66afa6/service-registrations/audit-trail?serviceType=cas&skip=0&limit=10 is called
Then the API returns status 200
And the response includes only audit records with serviceType = "cas"
And each record has action = "register" or action = "deregister"
And the response includes: total (total count of CAS records), skip = 0, limit = 10
```

---

## Response Patterns

All responses follow [API-RESPONSE-PATTERNS.md](../standards/API-RESPONSE-PATTERNS.md):
- Success responses (200, 201) include the resource data
- Error responses follow `ProblemDetails` format
- Pagination responses wrap results with `items`, `total`, `skip`, `limit`
- All timestamps use ISO 8601 format
- All responses include `Content-Type: application/json`

---

## Notes

- **Business Rules Enforced by API:**
  - BR1: A patient may only have one active CAS registration (enforce in POST scenario 6)
  - BR2: A patient may only have one active DMR registration (enforce in POST)
  - BR3: Registration changes must be audited (create audit record on every POST/DELETE)
  - BR6: Users must have appropriate permissions (check authorization in all write operations)

- **Security:**
  - All endpoints require valid `Authorization` header
  - `registeredBy` and `deregisteredBy` must be captured from authenticated user context or request body
  - Permission checks must validate user role (e.g., `PharmacyStaff`, `Manager`)

- **Future Enhancements:**
  - Additional service types (future services beyond CAS and DMR)
  - Bulk registration operations
  - Registration eligibility checks (out of scope for this story)

