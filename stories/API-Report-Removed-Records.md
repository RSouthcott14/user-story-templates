# API - Retrieve Removed/Deleted Records Report

**Feature:** [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)  
**Related UI Story:** [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)  
**Story Points:** [TBD]  

## User Story

As a pharmacy compliance officer,  
I want to retrieve a report showing records that have been removed or deleted,  
So that I can audit data quality and maintain governance compliance.

## Context

[TBD - Define what types of records are tracked (consultations, patient registrations, etc.)]

This report is one of 11 pre-built analytical reports available in the Reports Library tab of the User Reports - Dashboard feature.

## Acceptance Criteria

**Total Scenarios: 5**

---

### Scenario 1: Retrieve deleted/removed records with default filters

```gherkin
Given default filters
When a GET request is made to /api/v1/reports/audit/removed-records
Then the API returns status 200
And the response includes records flagged as deleted/removed
And each record contains: [TBD - deletion metadata, reason, who deleted, when]
```

### Scenario 2: Filter by single pharmacy

```gherkin
Given a pharmacyId is specified
When a GET request is made to /api/v1/reports/audit/removed-records?pharmacyId={pharmacyId}
Then the API returns status 200
And results reflect only that pharmacy's removed records
```

### Scenario 3: Filter by multiple pharmacies

```gherkin
Given multiple pharmacyIds are specified
When a GET request is made to /api/v1/reports/audit/removed-records?pharmacyIds=id1,id2,id3
Then the API returns status 200
And results aggregate across all pharmacies
```

### Scenario 4: Apply date range filters

```gherkin
Given a dateRange parameter is specified
When a GET request is made to /api/v1/reports/audit/removed-records?dateRange={dateRange}
Then the API returns status 200
And results reflect the specified deletion date period
```

### Scenario 5: Handle invalid pharmacy

```gherkin
Given an invalid pharmacyId is specified
When a GET request is made to /api/v1/reports/audit/removed-records?pharmacyId={invalidId}
Then the API returns status 404
```

## API Specification

### Endpoint Path

```
GET /api/v1/reports/audit/removed-records
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
    "title": "Removed/Deleted Records",
    "generatedDate": "2026-10-21T13:51:00Z",
    "periodCovered": {
      "startDate": "2026-09-21",
      "endDate": "2026-10-21",
      "label": "Last month"
    }
  },
  "data": [
    {
      "recordId": "[TBD]",
      "recordType": "[TBD - consultation, registration, etc.]",
      "deletedDate": "[TBD - ISO 8601]",
      "deletedBy": "[TBD - user/staff identifier]",
      "reason": "[TBD - reason for deletion]",
      "details": "[TBD]"
    }
  ],
  "summary": {
    "totalDeleted": "[TBD]",
    "byType": "[TBD]"
  }
}
```

## Implementation Notes

**[TBD] - Requires clarification:**
- Which record types to include (consultations, registrations, etc.)
- How are deletions tracked? (soft delete flag, audit table, etc.)
- Required metadata (who, when, why)
- Sensitive data handling in audit logs
- Retention policy for deleted record history

## Related Work Items

- Parent Feature: [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)
- Related UI Story: [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)

## Tags

Add tags: `AI Generated`, `Review (BR)`, `Report API`, `Audit`
