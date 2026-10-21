# API - Retrieve Dashboard Metrics

**Feature:** [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)  
**Related UI Story:** [617495](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617495)  
**Story Points:** 13  

## User Story

As a pharmacy administrator,  
I want to retrieve dashboard metrics that show consultation and patient registration activity,  
So that I can monitor service performance and patient engagement across multiple services with flexible date range and pharmacy filtering.

## Context

The Choose Pharmacy NextGen platform requires an API endpoint to support the Dashboard UI (617495). The dashboard displays key performance indicators across five consultation services (CAS, IPS, EMS, CS, DMR) and patient registrations. Users need to filter metrics by pharmacy (single or multiple) and date range to see how performance varies over time and across locations.

## Acceptance Criteria

**Total Scenarios: 8**

---

### Scenario 1: Retrieve dashboard metrics for all pharmacies with default date range

```gherkin
Given no pharmacy filter is specified
And no date range filter is specified (defaults to Last week)
When a GET request is made to /api/v1/dashboard/metrics
Then the API returns status 200
And the response includes:
| Field | Type | Description |
| --- | --- | --- |
| consultationMetrics | object | Consultation activity across all services |
| consultationMetrics.cas | integer | Total CAS consultations |
| consultationMetrics.ips | integer | Total IPS consultations |
| consultationMetrics.ems | integer | Total EMS consultations |
| consultationMetrics.cs | integer | Total CS (Contraception Service) consultations |
| consultationMetrics.dmr | integer | Total DMR consultations |
| consultationMetrics.total | integer | Total consultations across all services |
| registrationMetrics | object | Patient registration activity |
| registrationMetrics.cas | integer | Total patients registered for CAS |
| registrationMetrics.dmr | integer | Total patients registered for DMR |
| registrationMetrics.total | integer | Total patients registered across all services |
| periodCovered | object | Date range information |
| periodCovered.startDate | string (ISO 8601) | Start date of the period |
| periodCovered.endDate | string (ISO 8601) | End date of the period |
| periodCovered.label | string | Human-readable period label (e.g., "Last week", "Last month") |
And all numeric values are non-negative integers
```

### Scenario 2: Retrieve dashboard metrics for specific pharmacy

```gherkin
Given a pharmacyId is specified in the query parameters
When a GET request is made to /api/v1/dashboard/metrics?pharmacyId={pharmacyId}
Then the API returns status 200
And the response includes metrics filtered to only that pharmacy
And the consultation and registration totals reflect only that pharmacy's data
```

### Scenario 3: Retrieve dashboard metrics for multiple pharmacies

```gherkin
Given multiple pharmacyIds are specified (comma-separated or as array parameter)
When a GET request is made to /api/v1/dashboard/metrics?pharmacyIds=id1,id2,id3
Then the API returns status 200
And the response includes aggregated metrics across all specified pharmacies
And the consultation and registration totals are the sum of all specified pharmacies
```

### Scenario 4: Apply date range filter - Last month

```gherkin
Given a dateRange parameter with value "last-month"
When a GET request is made to /api/v1/dashboard/metrics?dateRange=last-month
Then the API returns status 200
And the response includes metrics for the last 30 days
And periodCovered.label is "Last month"
And periodCovered.startDate is approximately 30 days ago
And periodCovered.endDate is today
```

### Scenario 5: Apply date range filter - Last 3 months

```gherkin
Given a dateRange parameter with value "last-3-months"
When a GET request is made to /api/v1/dashboard/metrics?dateRange=last-3-months
Then the API returns status 200
And the response includes metrics for the last 90 days
And periodCovered.label is "Last 3 months"
And periodCovered.startDate is approximately 90 days ago
And periodCovered.endDate is today
```

### Scenario 6: Apply date range filter - Last 12 months

```gherkin
Given a dateRange parameter with value "last-12-months"
When a GET request is made to /api/v1/dashboard/metrics?dateRange=last-12-months
Then the API returns status 200
And the response includes metrics for the last 365 days
And periodCovered.label is "Last 12 months"
And periodCovered.startDate is approximately 365 days ago
And periodCovered.endDate is today
```

### Scenario 7: Apply date range filter - All time

```gherkin
Given a dateRange parameter with value "all"
When a GET request is made to /api/v1/dashboard/metrics?dateRange=all
Then the API returns status 200
And the response includes metrics from the earliest recorded data
And periodCovered.label is "All time"
And periodCovered.startDate is the earliest data collection date
And periodCovered.endDate is today
```

### Scenario 8: Invalid pharmacy ID

```gherkin
Given a pharmacyId is specified
And the pharmacy does not exist in the system
When a GET request is made to /api/v1/dashboard/metrics?pharmacyId={invalidId}
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message contains the pharmacy ID that was not found
And no data is persisted
```

## API Specification

### Endpoint Path

```
GET /api/v1/dashboard/metrics
```

- **Base Path:** `/api/v1/`
- **Resource:** `dashboard/metrics` (dashboard domain resource)

### Query Parameters

| Parameter | Type | Required | Description | Valid Values | Default |
|-----------|------|----------|-------------|--------------|---------|
| `pharmacyId` | string (UUID) | No | Single pharmacy ID to filter metrics | UUID format | Omit for all pharmacies |
| `pharmacyIds` | string | No | Comma-separated list of pharmacy IDs | UUID,UUID,UUID | Omit for all pharmacies |
| `dateRange` | string | No | Period to aggregate metrics over | `last-week`, `last-month`, `last-3-months`, `last-12-months`, `all` | `last-week` |

**Note:** Specify either `pharmacyId` for a single pharmacy or `pharmacyIds` for multiple, not both. If both are provided, `pharmacyIds` takes precedence.

### Request Example

```
GET /api/v1/dashboard/metrics
GET /api/v1/dashboard/metrics?pharmacyId=3fa85f64-5717-4562-b3fc-2c963f66afa6
GET /api/v1/dashboard/metrics?pharmacyIds=3fa85f64-5717-4562-b3fc-2c963f66afa6,4gb96f75-6728-4563-c4gd-3d964c77bh07
GET /api/v1/dashboard/metrics?dateRange=last-3-months
GET /api/v1/dashboard/metrics?pharmacyId=3fa85f64-5717-4562-b3fc-2c963f66afa6&dateRange=last-month
```

### Response (200 OK)

```json
{
  "consultationMetrics": {
    "cas": 142,
    "ips": 87,
    "ems": 56,
    "cs": 23,
    "dmr": 134,
    "total": 442
  },
  "registrationMetrics": {
    "cas": 1205,
    "dmr": 892,
    "total": 2097
  },
  "periodCovered": {
    "startDate": "2026-10-14",
    "endDate": "2026-10-21",
    "label": "Last week"
  }
}
```

**Response Field Descriptions:**
- `consultationMetrics.cas`: Total number of CAS (Common Ailments Service) consultations
- `consultationMetrics.ips`: Total number of IPS (Independent Prescriber Service) consultations
- `consultationMetrics.ems`: Total number of EMS (Emergency Medicine Service) consultations
- `consultationMetrics.cs`: Total number of CS (Contraception Service) consultations
- `consultationMetrics.dmr`: Total number of DMR (Medicines Review) consultations
- `consultationMetrics.total`: Sum of all consultation services
- `registrationMetrics.cas`: Count of unique patients registered for CAS
- `registrationMetrics.dmr`: Count of unique patients registered for DMR
- `registrationMetrics.total`: Sum of unique patients across all tracked services
- `periodCovered.startDate`: ISO 8601 date (YYYY-MM-DD) indicating the start of the aggregation period
- `periodCovered.endDate`: ISO 8601 date (YYYY-MM-DD) indicating the end of the aggregation period
- `periodCovered.label`: Human-readable label for the period

### Error Responses

#### 404 Not Found (Invalid Pharmacy ID)

```json
{
  "type": "EntityNotFoundException",
  "title": "EntityNotFoundException",
  "status": 404,
  "detail": "Pharmacy with ID 3fa85f64-5717-4562-b3fc-2c963f66afa6 not found",
  "instance": "/api/v1/dashboard/metrics?pharmacyId=3fa85f64-5717-4562-b3fc-2c963f66afa6"
}
```

#### 400 Bad Request (Invalid Date Range)

```json
{
  "type": "ValidationException",
  "title": "ValidationException",
  "status": 400,
  "detail": "One or more validation errors occurred.",
  "errors": {
    "dateRange": ["Date range must be one of: last-week, last-month, last-3-months, last-12-months, all"]
  },
  "instance": "/api/v1/dashboard/metrics?dateRange=invalid"
}
```

## Implementation Notes

### Date Range Calculation

- **last-week:** Today minus 7 days through today
- **last-month:** Today minus 30 days through today
- **last-3-months:** Today minus 90 days through today
- **last-12-months:** Today minus 365 days through today
- **all:** Earliest recorded consultation/registration through today

### Metrics Aggregation

- **Consultation Metrics:** Count of `Consultation` records by service type where `Status = "Completed"` or `Status = "Recorded"`
- **Registration Metrics:** Count of unique patients with active `PatientService` records for each service type
- **Dates:** Use consultation recorded date or patient registration date as applicable

### Pharmacy Filtering

- If no `pharmacyId` or `pharmacyIds` is specified, include all pharmacies in the aggregation
- If `pharmacyId` is specified, filter to only that pharmacy
- If `pharmacyIds` is specified, aggregate across all specified pharmacies
- If an invalid pharmacy ID is provided, return 404 Not Found

### Performance Considerations

- Pre-aggregate metrics in a separate analytics table or materialized view if needed for performance
- Consider caching results with a 30-minute TTL to reduce database load
- Use indexes on pharmacy_id, service_type, and recorded_date columns for optimal query performance

## Out of Scope

- Custom date range selection (only predefined ranges supported)
- Breakdown of metrics by individual clinician or staff member
- Historical metric tracking or trend analysis beyond the selected date range
- Export of metrics to CSV, PDF, or other formats
- Real-time metric updates (point-in-time snapshots are acceptable)

## Related Work Items

- Parent Feature: [608725](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/608725)
- Related UI Story: [617495](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617495)

## Tags

Add tags: `AI Generated`, `Review (BR)`
