# API Example: GET /api/v1/consultations/ccm/conditions - Retrieve CCM Consultation Condition Concepts

## Story Type: API | Feature: 604314 | Effort: 13 hours

---

## Description

### Story Card
**As a** Clinical System  
**I want** to retrieve Independent Prescriber Set (IPS) condition concepts via the terminology server  
**So that** clinical users can search and access standardised condition codes for consultation recording

### Background
The system needs to retrieve condition concepts from the DHCW FHIR Terminology Server using one of two ValueSets depending on the clinical context:
- **Service Spec ValueSet**: Service specification specific clinical concepts approved for standard IPS pathway use
- **Extended Condition ValueSet**: Broader set of condition concepts for out-of-scope clinical scenarios

A `mode` query parameter allows callers to specify which ValueSet to search against. This supports the Clinical Conditions Management (CCM) journey where prescribers record presenting complaints and diagnoses, with flexibility for both standard and extended condition searching.

### Objective
Deliver a search endpoint that retrieves condition concepts by partial search term (name or SNOMED code) from either the Service Specification or Extended ValueSet based on the `mode` parameter, with result limiting and proper validation.

---

## Endpoint Details

**HTTP Method:** GET  
**Endpoint:** `/api/v1/consultations/ccm/conditions`

### Query Parameters
- `q`: [string, required] - Search query (condition name or SNOMED code)
- `limit`: [integer, optional, default: 10] - Maximum results to return (1-100)
- `mode`: [string, required, enum: `service-spec` | `extended`] - Determines which ValueSet to search
  - `service-spec`: Search Service Specification specific clinical concepts
  - `extended`: Search Extended Condition ValueSet (out-of-scope conditions)

### Terminology Reference

#### Service Spec Mode
**ValueSet Name:** `IPS Service Specification Conditions`  
**ValueSet ID:** `[TBD - confirm with Terminology team]`  
**FHIR URI:** `[TBD - confirm with Terminology team]`  
**Description:** Service specification specific clinical concept conditions approved for use within standard IPS pathway

#### Extended Mode
**ValueSet Name:** `IPS Extended Condition Concepts`  
**ValueSet ID:** `[TBD - confirm with Terminology team]`  
**FHIR URI:** `[TBD - confirm with Terminology team]`  
**Description:** Extended set of condition concepts beyond the standard service specification scope

---

## Request

### Request Headers
```
Authorization: Bearer {token}
Content-Type: application/json
Accept: application/fhir+json
```

### Request Example - Service Spec Mode
```
GET /api/v1/consultations/ccm/conditions?q=diab&mode=service-spec&limit=10
Authorization: Bearer eyJhbGciOiJSUzI1NiIs...
Content-Type: application/json
```

### Request Example - Extended Mode
```
GET /api/v1/consultations/ccm/conditions?q=diab&mode=extended&limit=10
Authorization: Bearer eyJhbGciOiJSUzI1NiIs...
Content-Type: application/json
```

---

## Response

### Success Response (200 OK)
```json
{
  "conditions": [
    {
      "code": "73211009",
      "display": "Diabetes mellitus (disorder)",
      "system": "http://snomed.info/sct"
    },
    {
      "code": "44054006",
      "display": "Diabetes mellitus type 2 (disorder)",
      "system": "http://snomed.info/sct"
    },
    {
      "code": "250798004",
      "display": "Type 1 diabetes mellitus (disorder)",
      "system": "http://snomed.info/sct"
    }
  ]
}
```

### Validation Error Response (400)
```json
{
  "type": "ValidationException",
  "title": "ValidationException",
  "status": 400,
  "detail": "One or more validation errors occurred.",
  "errors": {
    "q": ["Search query must be provided"]
  }
}
```

### No Results Response (200 with empty array)
```json
{
  "conditions": []
}
```

---

## Error Handling

| Status Code | Exception Type | Description | Scenario |
|---|---|---|---|
| 400 | ValidationException | Query parameter validation fails | Search query missing, limit out of range |
| 401 | AuthenticationException | Missing or invalid authentication token | No Authorization header or expired token |
| 403 | UnauthorizedAccessException | User lacks permission to access CCM data | User role does not permit access |
| 500 | (any unhandled exception) | Upstream terminology server error or unexpected error | Terminology server unavailable, database error |

---

## Acceptance Criteria

### Scenario 1: Search Service Spec ValueSet by condition name
```gherkin
Given a partial search term representing a condition name or SNOMED code
When GET /api/v1/consultations/ccm/conditions?q=diab&mode=service-spec&limit=10 is called
Then the API returns status 200
And the system queries the Service Specification ValueSet from the DHCW FHIR Terminology Server
And the response contains matching SNOMED CT concepts from that ValueSet
And each result includes: code, display, system (http://snomed.info/sct)
And results are ordered by relevance (best match first)
```

### Scenario 2: Search Extended ValueSet by condition name
```gherkin
Given a partial search term representing a condition name or SNOMED code
When GET /api/v1/consultations/ccm/conditions?q=diab&mode=extended&limit=10 is called
Then the API returns status 200
And the system queries the Extended Condition ValueSet from the DHCW FHIR Terminology Server
And the response contains matching SNOMED CT concepts from that ValueSet
And each result includes: code, display, system (http://snomed.info/sct)
And results are ordered by relevance (best match first)
```

### Scenario 3: Limit results to specified maximum (Service Spec mode)
```gherkin
Given the system has valid condition search results from the Service Spec ValueSet
When GET /api/v1/consultations/ccm/conditions?q=diab&mode=service-spec&limit=5 is called
Then the API returns status 200
And the response.conditions array contains no more than 5 items
And results respect the limit parameter
```

### Scenario 4: Limit results to specified maximum (Extended mode)
```gherkin
Given the system has valid condition search results from the Extended ValueSet
When GET /api/v1/consultations/ccm/conditions?q=diab&mode=extended&limit=5 is called
Then the API returns status 200
And the response.conditions array contains no more than 5 items
And results respect the limit parameter
```

### Scenario 5: Validation - search query missing
```gherkin
Given the search query parameter is not provided
When GET /api/v1/consultations/ccm/conditions?mode=service-spec&limit=10 is called (no q parameter)
Then the API returns status 400
And the response type is "ValidationException"
And the errors object contains: { "q": ["Search query must be provided"] }
```

### Scenario 6: Validation - mode parameter missing
```gherkin
Given the mode parameter is not provided
When GET /api/v1/consultations/ccm/conditions?q=diab&limit=10 is called (no mode parameter)
Then the API returns status 400
And the response type is "ValidationException"
And the errors object contains: { "mode": ["Mode must be specified (service-spec or extended)"] }
```

### Scenario 7: Validation - invalid mode value
```gherkin
Given the mode parameter contains an invalid value
When GET /api/v1/consultations/ccm/conditions?q=diab&mode=invalid&limit=10 is called
Then the API returns status 400
And the response type is "ValidationException"
And the errors object contains: { "mode": ["Mode must be either 'service-spec' or 'extended'"] }
```

### Scenario 8: Validation - invalid limit parameter
```gherkin
Given a limit parameter outside the valid range (1-100)
When GET /api/v1/consultations/ccm/conditions?q=diab&mode=service-spec&limit=150 is called
Then the API returns status 400
And the response type is "ValidationException"
And the errors object contains: { "limit": ["Limit must be between 1 and 100"] }
```

### Scenario 9: No results found
```gherkin
Given a search query that matches no condition concepts in the selected ValueSet
When GET /api/v1/consultations/ccm/conditions?q=xyz&mode=service-spec is called
Then the API returns status 200
And the response is: { "conditions": [] }
```

### Scenario 10: Authentication failure
```gherkin
Given no authorization token is provided
When GET /api/v1/consultations/ccm/conditions?q=diab&mode=service-spec is called without Authorization header
Then the API returns status 401
And the response type is "AuthenticationException"
And the detail message indicates missing or invalid authentication
```

---

## Related Stories

- **Parent Feature:** [604314](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/604314)
- **Related:** [603126](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/603126) — IPS Conditions UI story

