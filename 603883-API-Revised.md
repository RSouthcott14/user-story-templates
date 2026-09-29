# API Example: GET /api/v1/consultations/ccm/conditions - Retrieve CCM Consultation Condition Concepts

## Story Type: API | Feature: 604314 | Effort: 13 hours

---

## Description

### Story Card
**As a** Clinical System  
**I want** to retrieve Independent Prescriber Set (IPS) condition concepts via the terminology server  
**So that** clinical users can search and access standardised condition codes for consultation recording

### Background
The system needs to retrieve condition concepts from the DHCW FHIR Terminology Server, filtering by the IPS ValueSet to ensure only approved concepts are available. This supports the Clinical Conditions Management (CCM) journey where prescribers record presenting complaints and diagnoses.

### Objective
Deliver a search endpoint that retrieves IPS condition concepts by partial search term (name or SNOMED code), with result limiting and support for both in-scope (service spec) and out-of-scope conditions.

---

## Endpoint Details

**HTTP Method:** GET  
**Endpoint:** `/api/v1/consultations/ccm/conditions`

### Query Parameters
- `q`: [string, required] - Search query (condition name or SNOMED code)
- `limit`: [integer, optional, default: 10] - Maximum results to return (1-100)

### Terminology Reference
**ValueSet Name:** `[TBD - confirm with Terminology team]`  
**ValueSet ID:** `[TBD - confirm with Terminology team]`  
**FHIR URI:** `[TBD - confirm with Terminology team]`

---

## Request

### Request Headers
```
Authorization: Bearer {token}
Content-Type: application/json
Accept: application/fhir+json
```

### Request Example
```
GET /api/v1/consultations/ccm/conditions?q=diab&limit=10
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

### Scenario 1: Retrieve matching conditions by search query
```gherkin
Given a valid search query representing a condition name or SNOMED code
When GET /api/v1/consultations/ccm/conditions?q={term}&limit=10 is called
Then the API returns status 200
And the response is a JSON object with a "conditions" property
And each result includes: code (SNOMED code), display, system (http://snomed.info/sct)
And the results are ordered by relevance (best match first)
```

### Scenario 2: Limit results to specified maximum
```gherkin
Given the system has valid condition search results
When GET /api/v1/consultations/ccm/conditions?q=diab&limit=10 is called
Then the API returns status 200
And the response.conditions array contains no more than 10 items
And results respect the limit parameter
```

### Scenario 3: Retrieve conditions ordered by relevance
```gherkin
Given multiple conditions match the search query
When GET /api/v1/consultations/ccm/conditions?q=diab&limit=10 is called
Then results are ordered by relevance (best match first)
And partial matches appear before exact matches
```

### Scenario 4: Validation - search query missing
```gherkin
Given the search query parameter is not provided
When GET /api/v1/consultations/ccm/conditions?limit=10 is called (no q parameter)
Then the API returns status 400
And the response type is "ValidationException"
And the errors object contains: { "q": ["Search query must be provided"] }
And no data is persisted
```

### Scenario 5: Validation - invalid limit parameter
```gherkin
Given a limit parameter outside the valid range (1-100)
When GET /api/v1/consultations/ccm/conditions?q=diab&limit=150 is called
Then the API returns status 400
And the response type is "ValidationException"
And the errors object contains: { "limit": ["Limit must be between 1 and 100"] }
And no data is persisted
```

### Scenario 6: No results found
```gherkin
Given a search query that matches no condition concepts
When GET /api/v1/consultations/ccm/conditions?q=xyz is called
Then the API returns status 200
And the response is: { "conditions": [] }
```

### Scenario 7: Authentication failure
```gherkin
Given no authorization token is provided
When GET /api/v1/consultations/ccm/conditions?q=diab is called without Authorization header
Then the API returns status 401
And the response type is "AuthenticationException"
And the detail message indicates missing or invalid authentication
And no data is persisted
```

---

## Related Stories

- **Parent Feature:** [604314](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/604314)
- **Related:** [603126](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/603126) — IPS Conditions UI story

