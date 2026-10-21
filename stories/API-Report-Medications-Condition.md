# API - Retrieve Medications per Condition Report

**Feature:** [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)  
**Related UI Story:** [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)  
**Story Points:** [TBD]  

## User Story

As a pharmacy analyst,  
I want to retrieve a report showing medications recommended or provided, grouped by clinical condition,  
So that I can understand medication usage patterns and identify prescribing trends.

## Context

[TBD - Provide report description]

This report is one of 11 pre-built analytical reports available in the Reports Library tab of the User Reports - Dashboard feature.

## Acceptance Criteria

**Total Scenarios: 5**

---

### Scenario 1: Retrieve medications per condition with default filters

```gherkin
Given default filters
When a GET request is made to /api/v1/reports/medications/condition
Then the API returns status 200
And the response includes medications grouped by condition
And each record contains: [TBD - define fields]
```

### Scenario 2: Filter by single pharmacy

```gherkin
Given a pharmacyId is specified
When a GET request is made to /api/v1/reports/medications/condition?pharmacyId={pharmacyId}
Then the API returns status 200
And results reflect only that pharmacy's medication data
```

### Scenario 3: Filter by multiple pharmacies

```gherkin
Given multiple pharmacyIds are specified
When a GET request is made to /api/v1/reports/medications/condition?pharmacyIds=id1,id2,id3
Then the API returns status 200
And results are aggregated across all pharmacies
```

### Scenario 4: Apply date range filters

```gherkin
Given a dateRange parameter is specified
When a GET request is made to /api/v1/reports/medications/condition?dateRange={dateRange}
Then the API returns status 200
And results reflect the specified period
```

### Scenario 5: Handle invalid pharmacy

```gherkin
Given an invalid pharmacyId is specified
When a GET request is made to /api/v1/reports/medications/condition?pharmacyId={invalidId}
Then the API returns status 404
```

## API Specification

### Endpoint Path

```
GET /api/v1/reports/medications/condition
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
    "title": "Medications provided per Condition",
    "generatedDate": "2026-10-21T13:51:00Z",
    "periodCovered": {
      "startDate": "2026-09-21",
      "endDate": "2026-10-21",
      "label": "Last month"
    }
  },
  "data": [
    {
      "conditionCode": "[TBD]",
      "conditionName": "[TBD]",
      "medicationCode": "[TBD]",
      "medicationName": "[TBD]",
      "[TBD - field1]": "[TBD - value]"
    }
  ]
}
```

## Implementation Notes

**[TBD] - Requires clarification:**
- Data structure: nested by condition then medication, or flat list?
- Which medication fields are required?
- Metric definition (count, frequency, percentage)

## Related Work Items

- Parent Feature: [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)
- Related UI Story: [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)

## Tags

Add tags: `AI Generated`, `Review (BR)`, `Report API`
