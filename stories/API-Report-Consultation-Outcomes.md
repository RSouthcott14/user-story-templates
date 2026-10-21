# API - Retrieve Consultation Outcomes Report

**Feature:** [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)  
**Related UI Story:** [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)  
**Story Points:** [TBD]  

## User Story

As a pharmacy analyst,  
I want to retrieve a report showing consultation outcomes (e.g., resolved, referred, supply provided),  
So that I can understand which outcomes are most common and track service effectiveness.

## Context

[TBD - Provide report description and define outcome categories]

This report is one of 11 pre-built analytical reports available in the Reports Library tab of the User Reports - Dashboard feature.

## Acceptance Criteria

**Total Scenarios: 5**

---

### Scenario 1: Retrieve consultation outcomes with default filters

```gherkin
Given default filters
When a GET request is made to /api/v1/reports/consultations/outcomes
Then the API returns status 200
And the response includes outcome categories with counts/percentages
And each outcome record contains: [TBD - define fields]
```

### Scenario 2: Filter by single pharmacy

```gherkin
Given a pharmacyId is specified
When a GET request is made to /api/v1/reports/consultations/outcomes?pharmacyId={pharmacyId}
Then the API returns status 200
And results reflect only that pharmacy's outcomes
```

### Scenario 3: Filter by multiple pharmacies

```gherkin
Given multiple pharmacyIds are specified
When a GET request is made to /api/v1/reports/consultations/outcomes?pharmacyIds=id1,id2,id3
Then the API returns status 200
And results aggregate across all pharmacies
```

### Scenario 4: Apply date range filters

```gherkin
Given a dateRange parameter is specified
When a GET request is made to /api/v1/reports/consultations/outcomes?dateRange={dateRange}
Then the API returns status 200
And results reflect the specified period
```

### Scenario 5: Handle invalid pharmacy

```gherkin
Given an invalid pharmacyId is specified
When a GET request is made to /api/v1/reports/consultations/outcomes?pharmacyId={invalidId}
Then the API returns status 404
```

## API Specification

### Endpoint Path

```
GET /api/v1/reports/consultations/outcomes
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
    "title": "Consultation Outcomes",
    "generatedDate": "2026-10-21T13:51:00Z",
    "periodCcovered": {
      "startDate": "2026-09-21",
      "endDate": "2026-10-21",
      "label": "Last month"
    }
  },
  "data": [
    {
      "outcome": "[TBD - outcome category]",
      "count": "[TBD - number of consultations]",
      "percentage": "[TBD - percentage of total]"
    }
  ],
  "totalConsultations": "[TBD]"
}
```

## Implementation Notes

**[TBD] - Requires clarification:**
- What are the valid outcome categories? (e.g., Resolved, Referred, Supply Provided, etc.)
- How are outcomes defined in the data model?
- Should percentages be included?

## Related Work Items

- Parent Feature: [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)
- Related UI Story: [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)

## Tags

Add tags: `AI Generated`, `Review (BR)`, `Report API`
