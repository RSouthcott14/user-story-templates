# API - Retrieve Consultation Duration Report

**Feature:** [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)  
**Related UI Story:** [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)  
**Story Points:** [TBD]  

## User Story

As a pharmacy manager,  
I want to retrieve a report showing consultation duration metrics,  
So that I can understand consultation efficiency and identify potential process improvements.

## Context

[TBD - Define how duration is calculated and what metrics are included]

This report is one of 11 pre-built analytical reports available in the Reports Library tab of the User Reports - Dashboard feature.

## Acceptance Criteria

**Total Scenarios: 5**

---

### Scenario 1: Retrieve consultation duration metrics with default filters

```gherkin
Given default filters
When a GET request is made to /api/v1/reports/consultations/duration
Then the API returns status 200
And the response includes duration statistics: [TBD - define metrics]
And each record contains: [TBD - average, min, max, median, percentiles, etc.]
```

### Scenario 2: Filter by single pharmacy

```gherkin
Given a pharmacyId is specified
When a GET request is made to /api/v1/reports/consultations/duration?pharmacyId={pharmacyId}
Then the API returns status 200
And results reflect only that pharmacy's consultation durations
```

### Scenario 3: Filter by multiple pharmacies

```gherkin
Given multiple pharmacyIds are specified
When a GET request is made to /api/v1/reports/consultations/duration?pharmacyIds=id1,id2,id3
Then the API returns status 200
And results aggregate across all pharmacies
```

### Scenario 4: Apply date range filters

```gherkin
Given a dateRange parameter is specified
When a GET request is made to /api/v1/reports/consultations/duration?dateRange={dateRange}
Then the API returns status 200
And results reflect the specified period
```

### Scenario 5: Handle invalid pharmacy

```gherkin
Given an invalid pharmacyId is specified
When a GET request is made to /api/v1/reports/consultations/duration?pharmacyId={invalidId}
Then the API returns status 404
```

## API Specification

### Endpoint Path

```
GET /api/v1/reports/consultations/duration
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
    "title": "Consultation Duration",
    "generatedDate": "2026-10-21T13:51:00Z",
    "periodCovered": {
      "startDate": "2026-09-21",
      "endDate": "2026-10-21",
      "label": "Last month"
    }
  },
  "data": [
    {
      "[TBD - dimension]": "[TBD - e.g., staff member, service type, condition]",
      "averageDuration": "[TBD - in seconds or minutes]",
      "minDuration": "[TBD]",
      "maxDuration": "[TBD]",
      "medianDuration": "[TBD]",
      "consultationCount": "[TBD]"
    }
  ]
}
```

## Implementation Notes

**[TBD] - Requires clarification:**
- How is duration calculated? (start/end timestamp, recorded duration, etc.)
- What time unit? (seconds, minutes, ISO 8601 duration)
- Should duration include [TBD] or exclude [TBD]?
- Grouping dimension (by service type, staff member, condition, etc.)
- Treatment of outliers or null values

## Related Work Items

- Parent Feature: [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)
- Related UI Story: [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)

## Tags

Add tags: `AI Generated`, `Review (BR)`, `Report API`
