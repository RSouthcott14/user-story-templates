# API Story: POST /api/v1/cas/housekeeping/deregister - Trigger CAS De-Registration Housekeeping

## Story Type: API | Feature: 617589 | Effort: 13 hours

---

## Description

### Story Card
**As a** Clinical System Administrator  
**I want** to trigger the automatic CAS de-registration housekeeping process  
**So that** inactive CAS registrations (no consultation in 12 months) are automatically removed, reducing manual administrative effort and maintaining registration accuracy

### Background
The feature "Automatic CAS Service De-Registration" requires a backend API endpoint to execute the scheduled housekeeping process that identifies and de-registers patients with no CAS consultation activity in the preceding 12 months. This includes handling legacy (orphaned) registrations from previous systems.

### Objective
Deliver an administrative endpoint that triggers the CAS de-registration housekeeping job, validating preconditions, executing de-registrations, and returning a summary of actions taken.

---

## Endpoint Details

**HTTP Method:** POST  
**Endpoint:** `/api/v1/cas/housekeeping/deregister`  
**Full URL:** `https://api.example.com/api/v1/cas/housekeeping/deregister`

### Authorization
This endpoint requires elevated permissions. Only administrators and scheduled service jobs may invoke it.

- **Required Role:** `SystemAdministrator` or `ScheduledJob`
- **Authentication:** Bearer Token (JWT)

---

## Request

### Request Headers
```
Authorization: Bearer {token}
Content-Type: application/json
Accept: application/json
```

### Request Body
```json
{
  "inactivityThresholdDays": 365,
  "includeOrphanedRecords": true,
  "dryRun": false
}
```

**Fields:**
- `inactivityThresholdDays`: [integer, required, default: 365] — Number of days of inactivity before de-registration (must be ≥ 1, ≤ 1825)
- `includeOrphanedRecords`: [boolean, required, default: true] — Whether to process orphaned registrations from legacy systems
- `dryRun`: [boolean, required, default: false] — If true, validate and report without persisting changes

### Request Example
```
POST /api/v1/cas/housekeeping/deregister
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "inactivityThresholdDays": 365,
  "includeOrphanedRecords": true,
  "dryRun": false
}
```

---

## Response

### Success Response (202 Accepted)
```json
{
  "jobId": "550e8400-e29b-41d4-a716-446655440000",
  "status": "processing",
  "startedDateTime": "2026-11-02T15:54:56Z",
  "summary": {
    "totalPatientsCandidates": 1250,
    "activeRegistrationsReviewed": 1250,
    "deregistrationsProcessed": 87,
    "orphanedRecordsProcessed": 5,
    "orphanedRecordsDeleted": 5,
    "errorsEncountered": 0,
    "inactivityThresholdDays": 365
  },
  "dryRunIndicator": false,
  "jobStatusUrl": "/api/v1/cas/housekeeping/jobs/550e8400-e29b-41d4-a716-446655440000"
}
```

**Fields:**
- `jobId`: [UUID string] — Unique identifier for this housekeeping job
- `status`: [string enum: "processing", "completed", "failed"] — Current job status
- `startedDateTime`: [ISO 8601 timestamp] — When the job started
- `summary`: [object] — Summary of actions taken:
  - `totalPatientsCandidates`: [integer] — Total patients with active CAS registrations
  - `activeRegistrationsReviewed`: [integer] — Registrations evaluated by the process
  - `deregistrationsProcessed`: [integer] — CAS registrations automatically de-registered
  - `orphanedRecordsProcessed`: [integer] — Orphaned records from legacy system reviewed
  - `orphanedRecordsDeleted`: [integer] — Orphaned records deleted after de-registration
  - `errorsEncountered`: [integer] — Count of errors during processing
  - `inactivityThresholdDays`: [integer] — Inactivity threshold used
- `dryRunIndicator`: [boolean] — Whether this was a dry-run (no data persisted)
- `jobStatusUrl`: [string] — URL to poll for job completion and final results

**HTTP Status:** `202 Accepted` — Job queued for asynchronous processing

### Dry-Run Response (202 Accepted)
```json
{
  "jobId": "550e8400-e29b-41d4-a716-446655440000",
  "status": "processing",
  "startedDateTime": "2026-11-02T15:54:56Z",
  "summary": {
    "totalPatientsCandidates": 1250,
    "activeRegistrationsReviewed": 1250,
    "deregistrationsProcessed": 87,
    "orphanedRecordsProcessed": 5,
    "orphanedRecordsDeleted": 5,
    "errorsEncountered": 0,
    "inactivityThresholdDays": 365
  },
  "dryRunIndicator": true,
  "jobStatusUrl": "/api/v1/cas/housekeeping/jobs/550e8400-e29b-41d4-a716-446655440000"
}
```

---

## Error Handling

| Status Code | Error Type | Detail Message |
|---|---|---|
| 400 | `ValidationException` | `Inactivity threshold must be between 1 and 1825 days` |
| 400 | `ValidationException` | `Inactivity threshold is required` |
| 401 | `AuthenticationException` | `Invalid or expired authorization token` |
| 403 | `UnauthorizedAccessException` | `User does not have permission to trigger housekeeping jobs` |
| 409 | `ConflictException` | `A housekeeping job is already running; previous job must complete first` |
| 500 | `InternalServerError` | `An unexpected error occurred while queuing the housekeeping job` |

---

## Acceptance Criteria

**Total Scenarios: 11**

### Scenario 1: Successful Housekeeping Execution
```gherkin
Given an authenticated user with SystemAdministrator role
And there are 1250 active CAS registrations in the system
And 87 patients have not received a CAS consultation in 365+ days
When POST /api/v1/cas/housekeeping/deregister is called with inactivityThresholdDays=365, includeOrphanedRecords=true, dryRun=false
Then the API returns status 202
And the response includes a jobId
And the response summary shows deregistrationsProcessed=87
And the status is "processing"
And the jobStatusUrl points to a valid job polling endpoint
```

### Scenario 2: Dry-Run Mode (Validation Without Persistence)
```gherkin
Given an authenticated user with SystemAdministrator role
And there are 1250 active CAS registrations in the system
And 87 patients have not received a CAS consultation in 365+ days
When POST /api/v1/cas/housekeeping/deregister is called with dryRun=true
Then the API returns status 202
And the response includes summary with deregistrationsProcessed=87
And the dryRunIndicator is true
And no CAS registrations are de-registered
And no orphaned records are deleted
```

### Scenario 3: Orphaned Records Processing
```gherkin
Given the housekeeping process is configured with includeOrphanedRecords=true
And there are 5 orphaned CAS registration records in the legacy table
And these orphaned records show no consultation activity in 365+ days
And the new application has no more recent consultation records for these patients
When the housekeeping process runs
Then each orphaned record is evaluated
And the patient's last consultation date is compared against both the orphaned record and new application records
And a corresponding de-registration record is created in the new application
And the orphaned record is deleted from the legacy table
And the response summary shows orphanedRecordsProcessed=5 and orphanedRecordsDeleted=5
```

### Scenario 4: Orphaned Records Excluded
```gherkin
Given the housekeeping process is configured with includeOrphanedRecords=false
And there are 5 orphaned CAS registration records in the legacy table
When the housekeeping process runs
Then the orphaned records are not evaluated
And the response summary shows orphanedRecordsProcessed=0 and orphanedRecordsDeleted=0
And no changes are made to the orphaned records table
```

### Scenario 5: De-Registration Date Recording
```gherkin
Given a patient is identified for CAS de-registration
When the housekeeping process de-registers the patient
Then the de-registration date is recorded as the current date/time
And the de-registration reason is set to "No CAS consultation within [X] months"
And the registration status is updated to "de-registered"
And the patient's service registration record is updated
```

### Scenario 6: Audit Trail Recording
```gherkin
Given patients are de-registered during the housekeeping process
When the process completes
Then an audit entry is created for each de-registration
And each audit entry records: date, time, patient ID, de-registration reason, system indicator
And all audit entries have the reason set to "No CAS consultation within 12 months" (or configured value)
And the audit trail is available for reporting and compliance
```

### Scenario 7: Validation - Invalid Inactivity Threshold (Low)
```gherkin
Given an authenticated user with SystemAdministrator role
When POST /api/v1/cas/housekeeping/deregister is called with inactivityThresholdDays=0
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Inactivity threshold must be between 1 and 1825 days"
And the errors object contains: { "inactivityThresholdDays": ["Inactivity threshold must be between 1 and 1825 days"] }
And no data is persisted
```

### Scenario 8: Validation - Invalid Inactivity Threshold (High)
```gherkin
Given an authenticated user with SystemAdministrator role
When POST /api/v1/cas/housekeeping/deregister is called with inactivityThresholdDays=2000
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Inactivity threshold must be between 1 and 1825 days"
And the errors object contains: { "inactivityThresholdDays": ["Inactivity threshold must be between 1 and 1825 days"] }
And no data is persisted
```

### Scenario 9: Missing Authentication
```gherkin
Given a request with no Authorization header
When POST /api/v1/cas/housekeeping/deregister is called
Then the API returns status 401
And the response type is "AuthenticationException"
And the detail message is "Invalid or expired authorization token"
And no data is persisted
```

### Scenario 10: Insufficient Permissions
```gherkin
Given an authenticated user without SystemAdministrator role
When POST /api/v1/cas/housekeeping/deregister is called
Then the API returns status 403
And the response type is "UnauthorizedAccessException"
And the detail message is "User does not have permission to trigger housekeeping jobs"
And no data is persisted
```

### Scenario 11: Job Already Running
```gherkin
Given a housekeeping job is currently in "processing" status
When POST /api/v1/cas/housekeeping/deregister is called
Then the API returns status 409
And the response type is "ConflictException"
And the detail message is "A housekeeping job is already running; previous job must complete first"
And no data is persisted
```

---

## Definition of Done

- [ ] Endpoint implemented per specification
- [ ] Request validation implemented (inactivityThresholdDays range: 1-1825)
- [ ] Authentication/authorization verified (SystemAdministrator role required)
- [ ] Dry-run mode working correctly (validates without persisting changes)
- [ ] Active CAS registrations correctly identified and evaluated
- [ ] Consultation history correctly retrieved and compared (365+ day threshold)
- [ ] De-registration status correctly updated for qualifying patients
- [ ] De-registration date and reason recorded against each registration
- [ ] Orphaned registration records correctly processed (when enabled)
- [ ] Orphaned records deleted after successful de-registration
- [ ] Audit trail entries created for all de-registrations
- [ ] Job status tracking implemented; jobId and polling URL provided
- [ ] Concurrent job execution prevented (only one housekeeping job at a time)
- [ ] Response format matches specification (202 + summary + jobStatusUrl)
- [ ] All error scenarios return appropriate HTTP status codes and messages
- [ ] Unit tests passing (80%+ coverage)
- [ ] Integration tests passing
- [ ] Performance testing: process scales to 10,000+ active registrations
- [ ] API documentation generated
- [ ] Ready for integration testing

---

## Related Stories

- **API Story 2:** GET /api/v1/cas/housekeeping/deregistrations — Retrieve CAS de-registration audit logs

---

## Notes

- The housekeeping process is designed to run as a scheduled job (e.g., via Azure Service Bus or similar mechanism) but is also exposed via this manual trigger endpoint for testing and on-demand execution.
- The `dryRun` flag is critical for validating the housekeeping logic without risking unintended de-registrations.
- The 12-month (365-day) threshold is the standard but is configurable via `inactivityThresholdDays`.
- Orphaned records represent CAS registrations migrated from legacy systems; they must be cleaned up after successful migration to the new application.
- The audit trail is essential for compliance and governance, supporting post-execution reporting and verification.
