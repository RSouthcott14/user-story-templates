# API - Retrieve Consultations per Pharmacy Report

**Feature:** [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)  
**Related UI Story:** [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)  
**Story Points:** [TBD]  

## User Story

As a regional manager,  
I want to retrieve a report showing consultation volumes aggregated by pharmacy location,  
So that I can compare performance across multiple pharmacies and identify high/low performers.

## Context

[TBD - Provide report description]

This report is one of 11 pre-built analytical reports available in the Reports Library tab of the User Reports - Dashboard feature. It is specifically designed to support multi-pharmacy comparisons.

## Acceptance Criteria

**Total Scenarios: 5**

---

### Scenario 1: Retrieve consultations per pharmacy with default filters

```gherkin
Given default filters (last month)
When a GET request is made to /api/v1/reports/consultations/pharmacy
Then the API returns status 200
And the response includes all pharmacies (or user-accessible pharmacies)
And each pharmacy record contains: [TBD - define fields]
```

### Scenario 2: Filter by single pharmacy

```gherkin
Given a pharmacyId is specified
When a GET request is made to /api/v1/reports/consultations/pharmacy?pharmacyId={pharmacyId}
Then the API returns status 200
And results reflect only that single pharmacy
```

### Scenario 3: Filter by multiple pharmacies

```gherkin
Given multiple pharmacyIds are specified
When a GET request is made to /api/v1/reports/consultations/pharmacy?pharmacyIds=id1,id2,id3
Then the API returns status 200
And results include only specified pharmacies with comparative metrics
```

### Scenario 4: Apply date range filters

```gherkin
Given a dateRange parameter is specified
When a GET request is made to /api/v1/reports/consultations/pharmacy?dateRange={dateRange}
Then the API returns status 200
And results reflect the specified period
```

### Scenario 5: Handle invalid pharmacy

```gherkin
Given an invalid pharmacyId is specified
When a GET request is made to /api/v1/reports/consultations/pharmacy?pharmacyId={invalidId}
Then the API returns status 404
```

## API Specification

### Endpoint Path

```
GET /api/v1/reports/consultations/pharmacy
```

### Query Parameters

| Parameter | Type | Required | Description | Default |
|-----------|------|----------|-------------|---------|
| `pharmacyId` | string (UUID) | No | Single pharmacy ID | All user-accessible pharmacies |
| `pharmacyIds` | string | No | Comma-separated list | All user-accessible pharmacies |
| `dateRange` | string | No | Period to aggregate | `last-month` |

### Response (200 OK)

```json
{
  "reportMetadata": {
    "title": "Consultations per Pharmacy",
    "generatedDate": "2026-10-21T13:51:00Z",
    "periodCovered": {
      "startDate": "2026-09-21",
      "endDate": "2026-10-21",
      "label": "Last month"
    }
  },
  "data": [
    {
      "pharmacyId": "[TBD]",
      "pharmacyName": "[TBD]",
      "location": "[TBD]",
      "[TBD - field1]": "[TBD - value]",
      "[TBD - field2]": "[TBD - value]"
    }
  ]
}
```

## Implementation Notes

**[TBD] - Requires clarification:**
- Should results include zero-consultation pharmacies?
- Required pharmacy details (address, ODS code, etc.)
- Metrics and calculations per pharmacy
- Authorization: which pharmacies can user access?

## Related Work Items

- Parent Feature: [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)
- Related UI Story: [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)

## Tags

Add tags: `AI Generated`, `Review (BR)`, `Report API`
