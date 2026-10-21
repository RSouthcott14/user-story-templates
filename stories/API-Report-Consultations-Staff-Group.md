# API - Retrieve Consultations per Staff Group Report

**Feature:** [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)  
**Related UI Story:** [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)  
**Story Points:** [TBD]  

## User Story

As a pharmacy manager,  
I want to retrieve a report showing consultation activity aggregated by staff group or role,  
So that I can understand team performance and identify team-level trends.

## Context

[TBD - Provide report description]

This report is one of 11 pre-built analytical reports available in the Reports Library tab of the User Reports - Dashboard feature. It supports filtering by pharmacy location(s) and date range.

## Acceptance Criteria

**Total Scenarios: 5**

---

### Scenario 1: Retrieve staff group consultation metrics with default filters

```gherkin
Given no pharmacy filter is specified (defaults to requesting user's pharmacy)
And no date range filter is specified (defaults to Last month)
When a GET request is made to /api/v1/reports/consultations/staff-group
Then the API returns status 200
And the response includes staff groups with [TBD - specific fields]
And each staff group record contains: [TBD - define fields, data types, sample values]
```

### Scenario 2: Filter by single pharmacy

```gherkin
Given a pharmacyId is specified
When a GET request is made to /api/v1/reports/consultations/staff-group?pharmacyId={pharmacyId}
Then the API returns status 200
And the response includes only staff groups from that pharmacy
```

### Scenario 3: Filter by multiple pharmacies

```gherkin
Given multiple pharmacyIds are specified (comma-separated)
When a GET request is made to /api/v1/reports/consultations/staff-group?pharmacyIds=id1,id2,id3
Then the API returns status 200
And the response includes aggregated data across all specified pharmacies
```

### Scenario 4: Apply date range filters

```gherkin
Given a dateRange parameter is specified
When a GET request is made to /api/v1/reports/consultations/staff-group?dateRange={dateRange}
Then the API returns status 200
And the response includes metrics for the specified period
```

### Scenario 5: Handle invalid pharmacy

```gherkin
Given an invalid pharmacyId is specified
When a GET request is made to /api/v1/reports/consultations/staff-group?pharmacyId={invalidId}
Then the API returns status 404
And the response type is "EntityNotFoundException"
```

## API Specification

### Endpoint Path

```
GET /api/v1/reports/consultations/staff-group
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
    "title": "Consultations per Staff Group",
    "description": "[TBD]",
    "generatedDate": "2026-10-21T13:51:00Z",
    "periodCovered": {
      "startDate": "2026-09-21",
      "endDate": "2026-10-21",
      "label": "Last month"
    }
  },
  "data": [
    {
      "staffGroup": "[TBD - group identifier or name]",
      "[TBD - field1]": "[TBD - value]",
      "[TBD - field2]": "[TBD - value]"
    }
  ],
  "summary": {
    "[TBD - summary field]": "[TBD - value]"
  }
}
```

## Implementation Notes

**[TBD] - Requires clarification:**
- How are "staff groups" defined? (e.g., pharmacists, technicians, administrators)
- What fields should each group include?
- Data source and calculation method

## Out of Scope

- Report export
- Custom date ranges

## Related Work Items

- Parent Feature: [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)
- Related UI Story: [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)

## Tags

Add tags: `AI Generated`, `Review (BR)`, `Report API`
