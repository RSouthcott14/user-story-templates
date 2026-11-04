# API User Stories for Feature 617589: Automatic CAS Service De-Registration

## Summary

Two comprehensive API user stories have been created to support the automatic CAS de-registration housekeeping feature. These stories define the backend API endpoints required to execute and audit the automated de-registration process.

---

## Stories Created

### Story 1: POST /api/v1/cas/housekeeping/deregister
**File:** `stories/API-617589-01-CAS-Deregistration-Housekeeping.md`

**Purpose:** Trigger the automatic CAS de-registration housekeeping process

**Key Features:**
- **Endpoint:** `POST /api/v1/cas/housekeeping/deregister`
- **Authentication:** SystemAdministrator role required
- **Request Parameters:**
  - `inactivityThresholdDays`: Configurable inactivity threshold (1-1825, default 365)
  - `includeOrphanedRecords`: Process legacy orphaned registrations (default true)
  - `dryRun`: Validate without persisting (default false)
- **Response:** 202 Accepted with jobId and summary metrics
- **Processing:** Asynchronous job processing with polling URL
- **Coverage:** 11 comprehensive acceptance criteria scenarios
- **Effort:** 13 hours

**Key Capabilities:**
- Identifies patients with no CAS consultation in preceding 12 months
- Handles active CAS registrations
- Processes legacy (orphaned) registration records from previous systems
- Supports dry-run validation before actual de-registration
- Records de-registration date and reason
- Maintains audit trail of all automated actions
- Prevents concurrent job execution
- Comprehensive validation and error handling

---

### Story 2: GET /api/v1/cas/housekeeping/deregistrations
**File:** `stories/API-617589-02-CAS-Deregistration-Audit.md`

**Purpose:** Retrieve and report on CAS de-registration audit logs

**Key Features:**
- **Endpoint:** `GET /api/v1/cas/housekeeping/deregistrations`
- **Authentication:** CASAuditRead permission required
- **Query Parameters:**
  - `from`, `to`: Date range filtering (ISO 8601)
  - `q`: Free-text search (patient ID, NHS number)
  - `limit`: Pagination (1-500, default 50)
  - `skip`: Pagination offset
  - `sortBy`: Sort by deregistrationDate, patientId, or nhsNumber
- **Response:** 200 OK with paginated results and metadata
- **Coverage:** 10 comprehensive acceptance criteria scenarios
- **Effort:** 8 hours

**Key Capabilities:**
- Lists all de-registration records with full details
- Filters by date range for audit period queries
- Supports free-text search by patient ID or NHS number
- Paginated results with total count
- Distinguishes active vs. orphaned record types
- Shows last consultation date and inactivity days
- Includes job correlation (jobId reference)
- Comprehensive validation and error handling

---

## API Standards Compliance

Both stories follow the established Choose Pharmacy NextGen API standards:

✅ **Path Conventions:** Follow domain-based naming (kebab-case, lowercase, plural)  
✅ **Error Handling:** Use standardized ProblemDetails format with mapped HTTP status codes  
✅ **Response Patterns:** Paginated responses with consistent metadata structure  
✅ **Authentication/Authorization:** Role-based and permission-based access control  
✅ **Query Parameters:** Consistent camelCase naming (q, limit, skip, sortBy)  
✅ **HTTP Methods:** Appropriate method selection (POST for commands, GET for queries)  
✅ **Status Codes:** Proper status code semantics (202 for async, 400 for validation, etc.)  
✅ **Gherkin Format:** Strict markdown formatting per GHERKIN-FORMAT.md guidelines

---

## Feature Coverage

These stories comprehensively cover the feature requirements:

| Feature Requirement | Covered By |
|---|---|
| Scheduled housekeeping process identification | Story 1 (Scenario 1) |
| Determine patient's most recent consultation | Story 1 (Scenario 1) |
| Auto de-register for 12+ month inactivity | Story 1 (Scenarios 1, 2) |
| Record de-registration date/reason | Story 1 (Scenario 5) |
| Update registration status | Story 1 (Scenario 5) |
| Legacy orphaned record handling | Story 1 (Scenarios 3, 4) |
| Compare legacy + new consultation dates | Story 1 (Scenario 3) |
| Create de-registration record in new system | Story 1 (Scenario 3) |
| Delete orphaned record after migration | Story 1 (Scenario 3) |
| Record date/time of de-registration | Story 1 (Scenario 6) |
| Record reason for de-registration | Story 1 (Scenarios 5, 6) |
| Maintain audit trail | Story 1 (Scenario 6), Story 2 (All) |
| Support reporting on de-registrations | Story 2 (All scenarios) |

---

## Acceptance Criteria Overview

### Story 1: 11 Scenarios
1. Successful Housekeeping Execution
2. Dry-Run Mode (Validation Without Persistence)
3. Orphaned Records Processing
4. Orphaned Records Excluded
5. De-Registration Date Recording
6. Audit Trail Recording
7. Validation - Invalid Inactivity Threshold (Low)
8. Validation - Invalid Inactivity Threshold (High)
9. Missing Authentication
10. Insufficient Permissions
11. Job Already Running

### Story 2: 10 Scenarios
1. Retrieve All De-Registrations (No Filter)
2. Filter by Date Range
3. Pagination Support
4. Free-Text Search
5. Sorting by De-Registration Date (Descending)
6. Sorting by Patient ID
7. Distinguishing Active vs. Orphaned Records
8. Validation - Invalid Date Range
9. Validation - Invalid Pagination Limit
10. Missing Authentication

---

## Implementation Notes

- **Asynchronous Processing:** Story 1 uses 202 Accepted with jobId and polling URL for background processing
- **Dry-Run Support:** Critical for testing; validates logic without data persistence
- **Orphaned Record Migration:** Supports legacy system data cleanup during modernization
- **Concurrent Job Prevention:** Only one housekeeping job executes at a time
- **Audit Trail:** Every de-registration is recorded for compliance and governance
- **Performance:** Story 2 designed for efficient querying with 10,000+ audit records
- **Pagination:** Default 50 records with 500 maximum for batch exports

---

## Related Work

- **Feature ID:** 617589
- **Feature Name:** Automatic CAS Service De-Registration
- **Related API Stories:** These two stories are interdependent (execution + audit)
- **Branch:** `garethwillmott-api-cas-deregistration-housekeeping`
- **Commit:** `3706fd5`

---

## Next Steps

1. **Review & Feedback:** Review stories for completeness and alignment with architecture
2. **ADO Submission:** Submit both stories to Azure DevOps with Feature link (617589)
3. **Estimation:** Refine effort estimates during planning refinement
4. **Implementation:** Begin API development per specification
5. **Testing:** Execute all 21 scenarios during integration testing
6. **Documentation:** Generate API documentation from OpenAPI spec

---

## Files Created

- `stories/API-617589-01-CAS-Deregistration-Housekeeping.md` (13,061 characters)
- `stories/API-617589-02-CAS-Deregistration-Audit.md` (11,744 characters)

---

## Validation Checklist

- ✅ Both stories follow API-Template.md pattern
- ✅ Endpoint paths comply with API-PATH-CONVENTIONS.md
- ✅ Error handling follows API-ERROR-HANDLING.md
- ✅ Response formats follow API-RESPONSE-PATTERNS.md
- ✅ Acceptance criteria use strict Gherkin format per GHERKIN-FORMAT.md
- ✅ All scenarios are specific and testable
- ✅ Validation scenarios include exact error messages
- ✅ Both stories link to Feature 617589
- ✅ Authentication and authorization properly specified
- ✅ Pagination and filtering properly designed
- ✅ Related story links included
- ✅ Definition of Done comprehensive
- ✅ All scenarios end with "And no data is persisted" (where applicable)

---

**Status:** ✅ Ready for Azure DevOps Submission  
**Created:** 2026-11-02  
**Branch:** garethwillmott-api-cas-deregistration-housekeeping
