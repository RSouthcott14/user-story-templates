# API - Retrieve Consultations per Condition Report

**Feature:** [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)  
**Related UI Story:** [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)  
**Story Points:** [TBD]  

## User Story

As a pharmacy analyst,  
I want to retrieve a report showing consultation volumes broken down by clinical condition,  
So that I can identify which conditions are most common and understand service utilization patterns.

## Context

[TBD - Provide report description]

This report is one of 11 pre-built analytical reports available in the Reports Library tab of the User Reports - Dashboard feature.

## Acceptance Criteria

**Total Scenarios: 5**

---

### Scenario 1: Retrieve condition-based consultation metrics

```gherkin
Given default filters (user's pharmacy, last month)
When a GET request is made to /api/v1/reports/consultations/condition
Then the API returns status 200
And the response includes conditions with [TBD - specific fields]
And each condition record contains: [TBD - define fields]
```

### Scenario 2: Filter by single pharmacy

```gherkin
Given a pharmacyId is specified
When a GET request is made to /api/v1/reports/consultations/condition?pharmacyId={pharmacyId}
Then the API returns status 200
And results reflect only that pharmacy's consultations
```

### Scenario 3: Filter by multiple pharmacies

```gherkin
Given multiple pharmacyIds are specified
When a GET request is made to /api/v1/reports/consultations/condition?pharmacyIds=id1,id2,id3
Then the API returns status 200
And results are aggregated across all pharmacies
```

### Scenario 4: Apply date range filters

```gherkin
Given a dateRange parameter is specified
When a GET request is made to /api/v1/reports/consultations/condition?dateRange={dateRange}
Then the API returns status 200
And results reflect the specified date period
```

### Scenario 5: Handle invalid pharmacy

```gherkin
Given an invalid pharmacyId is specified
When a GET request is made to /api/v1/reports/consultations/condition?pharmacyId={invalidId}
Then the API returns status 404
```

## API Specification

### Endpoint Path

```
GET /api/v1/reports/consultations/condition
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
    "title": "Consultations per Condition",
    "generatedDate": "2026-10-21T13:51:00Z",
    "periodCovered": {
      "startDate": "2026-09-21",
      "endDate": "2026-10-21",
      "label": "Last month"
    }
  },
  "data": [
    {
      "conditionCode": "[TBD - SNOMED or internal code]",
      "conditionName": "[TBD - display name]",
      "[TBD - field1]": "[TBD - value]",
      "[TBD - field2]": "[TBD - value]"
    }
  ]
}
```

## Implementation Notes

**[TBD] - Requires clarification:**
- Which conditions should be included? (Service-specific or all?)
- How are conditions identified? (SNOMED codes, internal enums)
- Required fields and calculations

## Related Work Items

- Parent Feature: [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)
- Related UI Story: [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)

## Tags

Add tags: `AI Generated`, `Review (BR)`, `Report API`
