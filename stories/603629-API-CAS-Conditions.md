# API - Retrieve CAS Conditions

**Work Item:** [603629](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/603629)  
**Feature:** [577034](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/577034)  
**Story Points:** 8  
**Status:** Active

## User Story

As a system,  
I want to retrieve the Common Ailments Service (CAS) conditions from the DHCW FHIR Terminology Server ValueSet,  
So that clinical and pharmacy decision support systems can display condition lists during triage and consultation workflows.

## Context

The Choose Pharmacy NextGen platform requires access to a standardized set of condition concepts that clinicians and pharmacists use within the Common Ailments Service pathway. These conditions must be retrieved from a canonical FHIR ValueSet exposed via the DHCW Terminology Server to ensure consistency across all consumers.

## Acceptance Criteria

**Total Scenarios: 5**

---

### ~~Scenario 1: Retrieve all CAS condition values~~ 
**REMOVED - Search term is required**

~~Given the CAS Conditions ValueSet is available from the DHCW FHIR Terminology Server~~  
~~When a GET request is made to /api/v1/cas/conditions~~  
~~Then the API returns the available SNOMED CT CAS Condition concepts~~  
~~And each concept contains a system, code and display value~~  
~~And the API returns 200 OK~~

---

### Scenario 1: Retrieve symptom suggestions using a partial search term

```gherkin
Given a partial search term representing a symptom name or SNOMED code
And the CAS Conditions ValueSet is available from the DHCW FHIR Terminology Server
When a GET request is made to /api/v1/cas/conditions?q={searchTerm}
Then the API returns matching results from the CAS conditions ValueSet
And each concept contains a system, code and display value
And the API returns 200 OK
```

### Scenario 2: Missing search term validation

```gherkin
Given a search term is not provided
When a GET request is made to /api/v1/cas/conditions
Then the API returns status 400
And the response type is "ValidationException"
And the errors object contains: { "q": ["Search term is required"] }
And no data is persisted
```

### Scenario 3: No matching condition values found

```gherkin
Given a search term is provided
And the supplied search term does not match any concepts in the CAS Conditions ValueSet
When a GET request is made to /api/v1/cas/conditions?q={searchTerm}
Then the API returns an empty array
And the API returns 200 OK
```

### Scenario 4: ValueSet unavailable or internal error

```gherkin
Given a search term is provided
And the CAS Conditions ValueSet is unavailable from the DHCW FHIR Terminology Server
When a GET request is made to /api/v1/cas/conditions?q={searchTerm}
Then the API returns a 500 Internal Server Error
And the error response contains a descriptive error message
And no data is persisted
```

## API Specification

### Endpoint Path

```
GET /api/v1/cas/conditions
```

- **Base Path:** `/api/v1/`
- **Resource:** `cas/conditions` (lowercase, singular concept domain)

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `q` | string | Yes | Search term to filter condition concepts (by name or SNOMED code) |

### Response (200 OK)

```json
{
  "data": [
    {
      "system": "http://snomed.info/sct",
      "code": "82034002",
      "display": "Sore throat"
    },
    {
      "system": "http://snomed.info/sct",
      "code": "25064002",
      "display": "Headache"
    }
  ]
}
```

### Error Responses

#### 400 Bad Request (Missing Search Term)

```json
{
  "type": "ValidationException",
  "title": "ValidationException",
  "status": 400,
  "detail": "One or more validation errors occurred.",
  "errors": {
    "q": ["Search term is required"]
  },
  "instance": "/api/v1/cas/conditions"
}
```

#### 500 Internal Server Error

```json
{
  "type": "https://fhir.nhs.wales/ChoosePharmacy/ProblemDetails/TerminologyServerError",
  "title": "Terminology Server Error",
  "status": 500,
  "detail": "Failed to retrieve CAS Conditions ValueSet from DHCW FHIR Terminology Server.",
  "instance": "/api/v1/cas/conditions?q={searchTerm}"
}
```

## Implementation Notes

- Retrieve CAS Conditions from the DHCW FHIR Terminology Server as a pre-built or cached ValueSet
- Support partial matching on concept display names and SNOMED codes
- Return results in the standardized `CodedElement` response format
- Implement appropriate caching strategy to minimize calls to the Terminology Server
- Include security/authorization checks if required by policy

## Out of Scope

- Modification or creation of CAS Condition concepts (read-only operation)
- Patient-specific condition filtering
- Historical versioning of condition values
- Custom condition mapping or transformation logic

## Related Work Items

- Parent Feature: [577034](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/577034)
- Implementation Tasks: 615030, 615031, 615032, 615033, 615034, 615029, 615027
