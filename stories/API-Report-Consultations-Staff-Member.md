# API - Retrieve Consultation per Staff Member Report

**Feature:** [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)  
**Related UI Story:** [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)  
**Story Points:** [TBD]  

## User Story

As a pharmacy manager,  
I want to retrieve a report showing consultation volumes and activity metrics per staff member,  
So that I can monitor individual staff performance and identify high performers or training needs.

## Context

[TBD - Provide report description]

This report is one of 11 pre-built analytical reports available in the Reports Library tab of the User Reports - Dashboard feature. It supports filtering by pharmacy location(s) and date range to enable comparative analysis.

## Acceptance Criteria

**Total Scenarios: 5**

---

### Scenario 1: Retrieve staff consultation metrics with default filters

```gherkin
Given no pharmacy filter is specified (defaults to requesting user's pharmacy)
And no date range filter is specified (defaults to Last month)
When a GET request is made to /api/v1/reports/consultations/staff-member
Then the API returns status 200
And the response includes a list of staff members with [TBD - specific fields]
And each staff member record contains: [TBD - define fields, data types, sample values]
And all numeric values are non-negative integers or floats as appropriate
```

### Scenario 2: Filter by single pharmacy

```gherkin
Given a pharmacyId is specified in the query parameters
When a GET request is made to /api/v1/reports/consultations/staff-member?pharmacyId={pharmacyId}
Then the API returns status 200
And the response includes only staff members from that pharmacy
And metrics reflect only that pharmacy's data
```

### Scenario 3: Filter by multiple pharmacies

```gherkin
Given multiple pharmacyIds are specified (comma-separated)
When a GET request is made to /api/v1/reports/consultations/staff-member?pharmacyIds=id1,id2,id3
Then the API returns status 200
And the response includes staff members from all specified pharmacies
And metrics are aggregated appropriately [TBD - clarify aggregation logic]
```

### Scenario 4: Apply date range filters

```gherkin
Given a dateRange parameter with value "last-month"
When a GET request is made to /api/v1/reports/consultations/staff-member?dateRange=last-month
Then the API returns status 200
And the response includes metrics for the last 30 days
And other date range options (last-week, last-3-months, last-12-months, all) work correctly
```

### Scenario 5: Handle invalid pharmacy or data not found

```gherkin
Given a pharmacyId is specified
And the pharmacy does not exist
When a GET request is made to /api/v1/reports/consultations/staff-member?pharmacyId={invalidId}
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message contains the pharmacy ID that was not found
And no data is persisted
```

## API Specification

### Endpoint Path

```
GET /api/v1/reports/consultations/staff-member
```

### Query Parameters

| Parameter | Type | Required | Description | Default |
|-----------|------|----------|-------------|---------|
| `pharmacyId` | string (UUID) | No | Single pharmacy ID to filter | User's pharmacy |
| `pharmacyIds` | string | No | Comma-separated list of pharmacy IDs | User's pharmacy |
| `dateRange` | string | No | Period to aggregate data over | `last-month` |

### Response (200 OK)

```json
{
  "reportMetadata": {
    "title": "Consultations per Staff Member",
    "description": "[TBD]",
    "generatedDate": "2026-10-21T13:51:00Z",
    "periodCovered": {
      "startDate": "2026-09-21",
      "endDate": "2026-10-21",
      "label": "Last month"
    },
    "pharmaciesIncluded": ["[TBD - pharmacy names or count]"]
  },
  "data": [
    {
      "staffId": "[TBD - unique identifier]",
      "staffName": "[TBD - full name or display name]",
      "[TBD - field1]": "[TBD - value type and description]",
      "[TBD - field2]": "[TBD - value type and description]",
      "[TBD - field3]": "[TBD - value type and description]"
    }
  ],
  "summary": {
    "[TBD - summary field1]": "[TBD - value]",
    "[TBD - summary field2]": "[TBD - value]"
  }
}
```

**[TBD] - To be completed during refinement:**
- Exact field names in data array
- Field data types and descriptions
- Sample values
- Summary fields
- Sorting/ordering requirements
- Pagination requirements (if applicable)

### Error Responses

#### 404 Not Found (Invalid Pharmacy)

```json
{
  "type": "EntityNotFoundException",
  "title": "EntityNotFoundException",
  "status": 404,
  "detail": "Pharmacy with ID {pharmacyId} not found"
}
```

## Implementation Notes

**[TBD] - Requires clarification:**
- Data source: Which tables/views in the database provide staff member consultation data?
- Calculations: How are metrics calculated? (count, sum, average, percentage)
- Filtering: Should results include staff with zero consultations?
- Authorization: Should staff only see their own data or can they see all staff?
- Performance: Expected volume of staff members per pharmacy?

## Out of Scope

- Report export (CSV, PDF)
- Custom date ranges beyond predefined options
- Real-time data (point-in-time aggregates acceptable)

## Related Work Items

- Parent Feature: [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)
- Related UI Story: [617496](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617496)

## Tags

Add tags: `AI Generated`, `Review (BR)`, `Report API`
