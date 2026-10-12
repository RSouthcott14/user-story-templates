## Description

### Summary

Implements a clinical assessment scoring endpoint for the Centor prediction rule. Enables CCM consultations to submit Centor criteria (exudate presence, lymph node swelling, temperature elevation, and cough absence) and receive a calculated risk score (0–4) for bacterial pharyngitis probability.

### Method

`POST`

### Endpoint

`/api/v1/clinical-assessments/centor/scores`

---

### Request Body

| Field | Type | Mandatory | Description |
|---|---|---|---|
| exudateOrSwellingOnTonsils | Boolean | No | Exudate or swelling on tonsils. `true` scores 1. |
| tenderSwollenAnteriorCervicalLymphNodes | Boolean | No | Tender or swollen anterior cervical lymph nodes. `true` scores 1. |
| temperatureAbove38 | Boolean | No | Temperature above 38°C (100.4°F). `true` scores 1. |
| cough | Boolean | No | Presence of cough. `false` (absence of cough) scores 1. |
| currentTemperatureCelsius | Decimal | No | Patient's recorded temperature in °C. Must be a positive number if supplied. |

**Validation:** At least one Centor criterion must be present in the request.

---

### Scoring Rules

| Criterion | Field | Scoring Answer | Score |
|---|---|---|---|
| Exudate or swelling on tonsils | `exudateOrSwellingOnTonsils` | `true` | 1 |
| Tender/swollen anterior cervical lymph nodes | `tenderSwollenAnteriorCervicalLymphNodes` | `true` | 1 |
| Temperature above 38°C | `temperatureAbove38` | `true` | 1 |
| Cough (absence) | `cough` | `false` | 1 |

Maximum score: 4

**Note:** Criteria fields are optional; at least one must be provided. Only submitted criteria are evaluated and included in the response `criteria` array. The `totalScore` is calculated from the submitted criteria.

---

### Examples

#### Example Request – Progressive Scoring (User submitting assessment incrementally)

```
POST /api/v1/clinical-assessments/centor/scores
```

```json
{
  "exudateOrSwellingOnTonsils": true,
  "tenderSwollenAnteriorCervicalLymphNodes": true,
  "currentTemperatureCelsius": 37.2
}
```

#### Example Response

```json
{
  "totalScore": 2,
  "criteria": [
    { "criterionCode": "exudate", "scoreValue": 1 },
    { "criterionCode": "lymph-nodes", "scoreValue": 1 }
  ]
}
```

---

#### Example Request – Complete Scoring

```json
{
  "exudateOrSwellingOnTonsils": true,
  "tenderSwollenAnteriorCervicalLymphNodes": true,
  "temperatureAbove38": true,
  "cough": false,
  "currentTemperatureCelsius": 38.5
}
```

#### Example Response

```json
{
  "totalScore": 4,
  "criteria": [
    { "criterionCode": "exudate", "scoreValue": 1 },
    { "criterionCode": "lymph-nodes", "scoreValue": 1 },
    { "criterionCode": "temperature", "scoreValue": 1 },
    { "criterionCode": "cough", "scoreValue": 1 }
  ]
}
```

---

### Response Codes

| Status Code | Description |
|---|---|
| 200 | Score calculated and returned successfully |
| 400 | Invalid request (missing or malformed fields) |
| 401 | Authentication failed |
| 403 | User not authorised |

---

## Acceptance Criteria

**Total Scenarios: 9**

### Scenario 1 – Progressive Score Calculation (Partial Submission)
**Given** a valid set of Centor assessment responses with only some criteria provided is submitted  
**When** the consumer calls `POST /api/v1/clinical-assessments/centor/scores`  
**Then** the API shall return a `totalScore` calculated from the provided criteria only  
**And** the `criteria` array shall contain entries only for the submitted fields  
**And** the API shall return HTTP 200 OK

### Scenario 2 – Complete Score Calculation (Generic)
**Given** a valid set of all four Centor assessment criteria is submitted  
**When** the consumer calls `POST /api/v1/clinical-assessments/centor/scores`  
**Then** the API shall return a `totalScore` and a `criteria` array containing a `scoreValue` for each criterion  
**And** the API shall return HTTP 200 OK

### Scenario 3 – Specific Scoring Example (Partial Score)
**Given** a request is submitted with `exudateOrSwellingOnTonsils: true`, `tenderSwollenAnteriorCervicalLymphNodes: true`, `temperatureAbove38: false`, and `cough: true`  
**When** the API calculates the score  
**Then** the API shall return a `totalScore` of 2  
**And** `exudate` shall return a `scoreValue` of 1  
**And** `lymph-nodes` shall return a `scoreValue` of 1  
**And** `temperature` shall return a `scoreValue` of 0  
**And** `cough` shall return a `scoreValue` of 0  
**And** the API shall return HTTP 200 OK

### Scenario 4 – Non-Scoring Criteria Return Zero

**Given** a valid set of Centor assessment responses is submitted  
**When** the API calculates the score  
**Then** each criterion where the answer does not contribute a point shall return a `scoreValue` of 0 in the `criteria` array  
**And** `exudateOrSwellingOnTonsils: false` shall return a `scoreValue` of 0  
**And** `tenderSwollenAnteriorCervicalLymphNodes: false` shall return a `scoreValue` of 0  
**And** `temperatureAbove38: false` shall return a `scoreValue` of 0  
**And** `cough: true` (presence of cough) shall return a `scoreValue` of 0  
**And** the API shall return HTTP 200 OK

### Scenario 5 – Maximum Score

**Given** a request is submitted with `exudateOrSwellingOnTonsils: true`, `tenderSwollenAnteriorCervicalLymphNodes: true`, `temperatureAbove38: true`, and `cough: false`  
**When** the API calculates the score  
**Then** the API shall return a `totalScore` of 4  
**And** each entry in the `criteria` array shall have a `scoreValue` of 1  
**And** the API shall return HTTP 200 OK

### Scenario 6 – Minimum Score

**Given** a request is submitted with `exudateOrSwellingOnTonsils: false`, `tenderSwollenAnteriorCervicalLymphNodes: false`, `temperatureAbove38: false`, and `cough: true`  
**When** the API calculates the score  
**Then** the API shall return a `totalScore` of 0  
**And** each entry in the `criteria` array shall have a `scoreValue` of 0  
**And** the API shall return HTTP 200 OK

### Scenario 7 – Current Temperature is Optional

**Given** a valid set of Centor assessment responses is submitted without `currentTemperatureCelsius`  
**When** the API calculates the score  
**Then** the API shall return the `totalScore` and `criteria` array unaffected  
**And** the API shall return HTTP 200 OK

### Scenario 8 – No Criteria Submitted

**Given** a request is submitted with no Centor criteria fields  
**When** the consumer calls the API  
**Then** the API shall return HTTP 400 Bad Request with the following error response:

```json
{
  "type": "ValidationException",
  "title": "ValidationException",
  "status": 400,
  "detail": "One or more validation errors occurred.",
  "errors": {
    "": ["At least one Centor criterion must be provided"]
  }
}
```

### Scenario 9 – Invalid Temperature Value

**Given** a request is submitted with a `currentTemperatureCelsius` value that is not a positive number  
**When** the consumer calls the API  
**Then** the API shall return HTTP 400 Bad Request with the following error response:

```json
{
  "type": "ValidationException",
  "title": "ValidationException",
  "status": 400,
  "detail": "One or more validation errors occurred.",
  "errors": {
    "currentTemperatureCelsius": ["currentTemperatureCelsius must be a positive number"]
  }
}
```
