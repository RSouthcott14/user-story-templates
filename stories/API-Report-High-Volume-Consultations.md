# API - Retrieve High Consultation Volume Report

**Feature:** [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)  
**Related UI Story:** [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)  
**Story Points:** [TBD]  

## User Story

As a pharmacy manager,  
I want to retrieve a report flagging high consultation volumes (potential anomalies),  
So that I can identify unusual activity patterns and investigate potential issues.

## Context

[TBD - Define what constitutes "high" volume (absolute threshold, statistical anomaly, etc.)]

This report is one of 11 pre-built analytical reports available in the Reports Library tab of the User Reports - Dashboard feature.

## Acceptance Criteria

**Total Scenarios: 5**

---

### Scenario 1: Retrieve high-volume consultations with default filters

```gherkin
Given default filters
When a GET request is made to /api/v1/reports/consultations/high-volume
Then the API returns status 200
And the response includes consultations/staff/pharmacies flagged as high volume
And each record contains: [TBD - volume metric, threshold, percentage above threshold]
```

### Scenario 2: Filter by single pharmacy

```gherkin
Given a pharmacyId is specified
When a GET request is made to /api/v1/reports/consultations/high-volume?pharmacyId={pharmacyId}
Then the API returns status 200
And results reflect only that pharmacy's high-volume data
```

### Scenario 3: Filter by multiple pharmacies

```gherkin
Given multiple pharmacyIds are specified
When a GET request is made to /api/v1/reports/consultations/high-volume?pharmacyIds=id1,id2,id3
Then the API returns status 200
And results aggregate across all pharmacies
```

### Scenario 4: Apply date range filters

```gherkin
Given a dateRange parameter is specified
When a GET request is made to /api/v1/reports/consultations/high-volume?dateRange={dateRange}
Then the API returns status 200
And results reflect the specified period
```

### Scenario 5: Handle invalid pharmacy

```gherkin
Given an invalid pharmacyId is specified
When a GET request is made to /api/v1/reports/consultations/high-volume?pharmacyId={invalidId}
Then the API returns status 404
```

## API Specification

### Endpoint Path

```
GET /api/v1/reports/consultations/high-volume
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
    "title": "High Volume Consultations",
    "generatedDate": "2026-10-21T13:51:00Z",
    "periodCovered": {
      "startDate": "2026-09-21",
      "endDate": "2026-10-21",
      "label": "Last month"
    }
  },
  "data": [
    {
      "[TBD - dimension]": "[TBD - staff member, pharmacy, condition, etc.]",
      "volumeCount": "[TBD]",
      "threshold": "[TBD - expected/normal level]",
      "percentageAboveThreshold": "[TBD]",
      "flag": "[TBD - severity: warning, alert, critical]"
    }
  ]
}
```

## Implementation Notes

**[TBD] - Requires clarification:**
- How is "high volume" defined? (fixed threshold, percentile-based, statistical anomaly)
- What dimension is being measured? (per staff member, per service type, per pharmacy, per condition)
- How is the threshold calculated?
- What flags/severity levels should be used?
- Time period for comparison (vs historical average, vs peer group, etc.)

## Related Work Items

- Parent Feature: [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)
- Related UI Story: [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)

## Tags

Add tags: `AI Generated`, `Review (BR)`, `Report API`
