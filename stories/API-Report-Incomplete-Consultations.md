# API - Retrieve Incomplete Consultations Report

**Feature:** [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)  
**Related UI Story:** [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)  
**Story Points:** [TBD]  

## User Story

As a pharmacy manager,  
I want to retrieve a report showing incomplete or abandoned consultations,  
So that I can track consultation completion rates and identify process bottlenecks.

## Context

[TBD - Define what constitutes "incomplete" (abandoned, timed out, partial completion, etc.)]

This report is one of 11 pre-built analytical reports available in the Reports Library tab of the User Reports - Dashboard feature.

## Acceptance Criteria

**Total Scenarios: 5**

---

### Scenario 1: Retrieve incomplete consultations with default filters

```gherkin
Given default filters
When a GET request is made to /api/v1/reports/consultations/incomplete
Then the API returns status 200
And the response includes consultations flagged as incomplete
And each record contains: [TBD - status, reason for incompleteness, when started/abandoned]
```

### Scenario 2: Filter by single pharmacy

```gherkin
Given a pharmacyId is specified
When a GET request is made to /api/v1/reports/consultations/incomplete?pharmacyId={pharmacyId}
Then the API returns status 200
And results reflect only that pharmacy's incomplete consultations
```

### Scenario 3: Filter by multiple pharmacies

```gherkin
Given multiple pharmacyIds are specified
When a GET request is made to /api/v1/reports/consultations/incomplete?pharmacyIds=id1,id2,id3
Then the API returns status 200
And results aggregate across all pharmacies
```

### Scenario 4: Apply date range filters

```gherkin
Given a dateRange parameter is specified
When a GET request is made to /api/v1/reports/consultations/incomplete?dateRange={dateRange}
Then the API returns status 200
And results reflect the specified period
```

### Scenario 5: Handle invalid pharmacy

```gherkin
Given an invalid pharmacyId is specified
When a GET request is made to /api/v1/reports/consultations/incomplete?pharmacyId={invalidId}
Then the API returns status 404
```

## API Specification

### Endpoint Path

```
GET /api/v1/reports/consultations/incomplete
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
    "title": "Incomplete Consultations",
    "generatedDate": "2026-10-21T13:51:00Z",
    "periodCovered": {
      "startDate": "2026-09-21",
      "endDate": "2026-10-21",
      "label": "Last month"
    }
  },
  "data": [
    {
      "consultationId": "[TBD]",
      "patientId": "[TBD]",
      "serviceType": "[TBD]",
      "staffMember": "[TBD]",
      "status": "[TBD - abandoned, incomplete, timeout, etc.]",
      "startedAt": "[TBD - ISO 8601]",
      "abandonedAt": "[TBD - ISO 8601]",
      "reason": "[TBD]"
    }
  ],
  "summary": {
    "totalIncomplete": "[TBD]",
    "completionRate": "[TBD - percentage]",
    "topReasons": "[TBD]"
  }
}
```

## Implementation Notes

**[TBD] - Requires clarification:**
- Definition of "incomplete" status values in the system
- Reasons for incompleteness (categorization/enumeration)
- Should results include abandoned vs timeout differently?
- Time thresholds (how long before a consultation is considered abandoned?)
- Completion rate baseline for comparison
- Sensitive data handling (PII in consultation records)

## Related Work Items

- Parent Feature: [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)
- Related UI Story: [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)

## Tags

Add tags: `AI Generated`, `Review (BR)`, `Report API`
