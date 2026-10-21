# API - Retrieve Medications Outside of Spec Report (IPS)

**Feature:** [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)  
**Related UI Story:** [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)  
**Status:** Draft  
**Story Points:** [TBD]  

## User Story

As a pharmacy quality manager,  
I want to retrieve a report showing medications prescribed outside of specification in the Independent Prescriber Service,  
So that I can monitor prescribing compliance and identify quality issues.

## Context

[TBD - Define what "outside of spec" means for IPS medications]

This report is one of 11 pre-built analytical reports available in the Reports Library tab of the User Reports - Dashboard feature. **Note:** This report is currently in Draft status and not yet available for general use.

## Acceptance Criteria

**Total Scenarios: 5**

---

### Scenario 1: Retrieve out-of-spec medications with default filters

```gherkin
Given default filters
When a GET request is made to /api/v1/reports/medications/outside-spec
Then the API returns status 200
And the response includes medications flagged as outside specification
And each record contains: [TBD - define fields including specification details]
```

### Scenario 2: Filter by single pharmacy

```gherkin
Given a pharmacyId is specified
When a GET request is made to /api/v1/reports/medications/outside-spec?pharmacyId={pharmacyId}
Then the API returns status 200
And results reflect only that pharmacy's out-of-spec medications
```

### Scenario 3: Filter by multiple pharmacies

```gherkin
Given multiple pharmacyIds are specified
When a GET request is made to /api/v1/reports/medications/outside-spec?pharmacyIds=id1,id2,id3
Then the API returns status 200
And results aggregate across all pharmacies
```

### Scenario 4: Apply date range filters

```gherkin
Given a dateRange parameter is specified
When a GET request is made to /api/v1/reports/medications/outside-spec?dateRange={dateRange}
Then the API returns status 200
And results reflect the specified period
```

### Scenario 5: Handle invalid pharmacy

```gherkin
Given an invalid pharmacyId is specified
When a GET request is made to /api/v1/reports/medications/outside-spec?pharmacyId={invalidId}
Then the API returns status 404
```

## API Specification

### Endpoint Path

```
GET /api/v1/reports/medications/outside-spec
```

### Query Parameters

| Parameter | Type | Required | Description | Default |
|-----------|------|----------|-------------|---------|
| `pharmacyId` | string (UUID) | No | Single pharmacy ID | User's pharmacy |
| `pharmacyIds` | string | No | Comma-separated list | User's pharmacy |
| `dateRange` | string | No | Period to aggregate | `last-month` |

### Response (200 OK)

```json
{
  "reportMetadata": {
    "title": "Medications Provided Outside of Specification (IPS)",
    "status": "draft",
    "generatedDate": "2026-10-21T13:51:00Z"
  },
  "data": [
    {
      "medicationCode": "[TBD]",
      "medicationName": "[TBD]",
      "specification": "[TBD - what spec was violated]",
      "count": "[TBD]",
      "reason": "[TBD]"
    }
  ]
}
```

## Implementation Notes

**[TBD] - Requires clarification:**
- Definition of "outside of spec" for IPS medications
- Business rules for flagging violations
- Specification fields and values
- Reason/categorization for violations

## Related Work Items

- Parent Feature: [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)
- Related UI Story: [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)

## Tags

Add tags: `AI Generated`, `Review (BR)`, `Report API`, `Draft`
