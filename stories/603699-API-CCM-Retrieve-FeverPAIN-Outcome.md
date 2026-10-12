## Summary

Implements a clinical assessment scoring endpoint for the FeverPAIN prediction rule. Enables CCM consultations to submit FeverPAIN criteria (cough/cold severity, fever history, illness onset, tonsillar inflammation, pus presence, and temperature) and receive a calculated risk score (0–5) for bacterial pharyngitis probability.

---

## Description

### Method

`POST`

### Endpoint

`/api/v1/clinical-assessments/feverpain/scores`

---

### Request Body

| Field | Type | Mandatory | Description |
|---|---|---|---|
| coughOrColdSymptoms | String (none/mild/moderate/severe) | No | Severity of cough or cold symptoms. `none` scores 1. |
| historyOfFeverInLast24Hours | Boolean | No | History of fever in the last 24 hours. `true` scores 1. |
| onsetOfIllness | String (zero-to-three-days/four-to-seven-days/greater-than-seven-days) | No | Duration since onset of illness. `zero-to-three-days` scores 1. |
| inflamedTonsils | String (none/mild/moderate/severe) | No | Severity of tonsillar inflammation. `severe` scores 1. |
| pusOnTonsils | Boolean | No | Presence of pus on tonsils. True scores 1. |
| currentTemperatureCelsius | Decimal | No | Patient's recorded temperature in °C. Must be a positive number if supplied. |

**Validation:** At least one of the five FeverPAIN criteria fields (`coughOrColdSymptoms`, `historyOfFeverInLast24Hours`, `onsetOfIllness`, `inflamedTonsils`, or `pusOnTonsils`) must be present in the request.

---

### Domain Types

Domain type records that must be created to support this endpoint for internal request validation. Each inherits from `Lookup<string>` and is used for API validation only — they are not FHIR code systems and are not persisted with encounter data.

#### CoughOrColdSeverity

**File Path:** `/src/ChoosePharmacy.Domain.Types/ClinicalAssessments/FeverPAIN/CoughOrColdSeverity.cs`

| Id | Code | Name |
|---|---|---|
| `none` | `none` | None |
| `mild` | `mild` | Mild |
| `moderate` | `moderate` | Moderate |
| `severe` | `severe` | Severe |

#### IllnessOnsetDuration

**File Path:** `/src/ChoosePharmacy.Domain.Types/ClinicalAssessments/FeverPAIN/IllnessOnsetDuration.cs`

| Id | Code | Name |
|---|---|---|
| `zero-to-three-days` | `zero-to-three-days` | 0–3 Days |
| `four-to-seven-days` | `four-to-seven-days` | 4–7 Days |
| `greater-than-seven-days` | `greater-than-seven-days` | Greater than 7 Days |

#### TonsilInflammationSeverity

**File Path:** `/src/ChoosePharmacy.Domain.Types/ClinicalAssessments/FeverPAIN/TonsilInflammationSeverity.cs`

| Id | Code | Name |
|---|---|---|
| `none` | `none` | None |
| `mild` | `mild` | Mild |
| `moderate` | `moderate` | Moderate |
| `severe` | `severe` | Severe |

---

### Scoring Rules

| Criterion | Field | Scoring Answer | Score |
|---|---|---|---|
| Cough or cold symptoms | `coughOrColdSymptoms` | `none` | 1 |
| History of fever in last 24 hours | `historyOfFeverInLast24Hours` | `true` | 1 |
| Onset of illness | `onsetOfIllness` | `zero-to-three-days` | 1 |
| Inflamed tonsils | `inflamedTonsils` | `severe` | 1 |
| Pus on tonsils | `pusOnTonsils` | `true` | 1 |

Maximum score: 5

**Note:** Only criteria fields present in the request are evaluated and included in the response `criteria` array. The `totalScore` is calculated from the submitted criteria only.

---

### Examples

#### Example Request – Progressive Scoring (User submitting assessment incrementally)

```
POST /api/v1/clinical-assessments/feverpain/scores
```

```json
{
  "coughOrColdSymptoms": "none",
  "historyOfFeverInLast24Hours": true,
  "currentTemperatureCelsius": 38.5
}
```

#### Example Response

```json
{
  "totalScore": 2,
  "criteria": [
    { "criterionCode": "cough", "scoreValue": 1 },
    { "criterionCode": "fever-24", "scoreValue": 1 }
  ]
}
```

---

#### Example Request – Complete Assessment

```json
{
  "coughOrColdSymptoms": "none",
  "historyOfFeverInLast24Hours": true,
  "onsetOfIllness": "zero-to-three-days",
  "inflamedTonsils": "severe",
  "pusOnTonsils": true,
  "currentTemperatureCelsius": 38.5
}
```

#### Example Response

```json
{
  "totalScore": 5,
  "criteria": [
    { "criterionCode": "cough", "scoreValue": 1 },
    { "criterionCode": "fever-24", "scoreValue": 1 },
    { "criterionCode": "onset", "scoreValue": 1 },
    { "criterionCode": "inflamed-tonsils", "scoreValue": 1 },
    { "criterionCode": "pus", "scoreValue": 1 }
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
**Given** a valid set of FeverPAIN assessment responses with only some criteria provided is submitted  
**When** the consumer calls `POST /api/v1/clinical-assessments/feverpain/scores`  
**Then** the API shall return a `totalScore` calculated from the provided criteria only  
**And** the `criteria` array shall contain entries only for the submitted fields  
**And** the API shall return HTTP 200 OK

### Scenario 2 – Complete Score Calculation (Generic)
**Given** a valid set of all five FeverPAIN assessment criteria is submitted  
**When** the consumer calls `POST /api/v1/clinical-assessments/feverpain/scores`  
**Then** the API shall return a `totalScore` and a `criteria` array containing a `scoreValue` for each criterion  
**And** the API shall return HTTP 200 OK

### Scenario 3 – Specific Scoring Example (Partial Score)
**Given** a request is submitted with `coughOrColdSymptoms: mild`, `historyOfFeverInLast24Hours: true`, `onsetOfIllness: zero-to-three-days`, `inflamedTonsils: moderate`, and `pusOnTonsils: true`  
**When** the API calculates the score  
**Then** the API shall return a `totalScore` of 3  
**And** `cough` shall return a `scoreValue` of 0  
**And** `fever-24` shall return a `scoreValue` of 1  
**And** `onset` shall return a `scoreValue` of 1  
**And** `inflamed-tonsils` shall return a `scoreValue` of 0  
**And** `pus` shall return a `scoreValue` of 1  
**And** the API shall return HTTP 200 OK

### Scenario 4 – Non-Scoring Criteria Return Zero
**Given** a valid set of FeverPAIN assessment responses is submitted  
**When** the API calculates the score  
**Then** each criterion where the answer does not contribute a point shall return a `scoreValue` of 0 in the `criteria` array  
**And** `coughOrColdSymptoms: mild`, `moderate`, or `severe` shall each return a `scoreValue` of 0  
**And** `historyOfFeverInLast24Hours: false` shall return a `scoreValue` of 0  
**And** `onsetOfIllness: four-to-seven-days` or `greater-than-seven-days` shall each return a `scoreValue` of 0  
**And** `inflamedTonsils: none`, `mild`, or `moderate` shall each return a `scoreValue` of 0  
**And** `pusOnTonsils: false` shall return a `scoreValue` of 0  
**And** the API shall return HTTP 200 OK

### Scenario 5 – Maximum Score
**Given** a request is submitted with `coughOrColdSymptoms: none`, `historyOfFeverInLast24Hours: true`, `onsetOfIllness: zero-to-three-days`, `inflamedTonsils: severe`, and `pusOnTonsils: true`  
**When** the API calculates the score  
**Then** the API shall return a `totalScore` of 5  
**And** each entry in the `criteria` array shall have a `scoreValue` of 1  
**And** the API shall return HTTP 200 OK

### Scenario 6 – Minimum Score
**Given** a request is submitted with `coughOrColdSymptoms: mild`, `historyOfFeverInLast24Hours: false`, `onsetOfIllness: four-to-seven-days`, `inflamedTonsils: none`, and `pusOnTonsils: false`  
**When** the API calculates the score  
**Then** the API shall return a `totalScore` of 0  
**And** each entry in the `criteria` array shall have a `scoreValue` of 0  
**And** the API shall return HTTP 200 OK

### Scenario 7 – Current Temperature is Optional
**Given** a valid set of FeverPAIN assessment responses is submitted without `currentTemperatureCelsius`  
**When** the API calculates the score  
**Then** the API shall return the `totalScore` and `criteria` array unaffected  
**And** the API shall return HTTP 200 OK

### Scenario 8 – No FeverPAIN Criteria Provided
**Given** a request is submitted without any of the five FeverPAIN criteria fields  
**When** the consumer calls the API  
**Then** the API shall return HTTP 400 Bad Request with the following error response:

```json
{
  "type": "ValidationException",
  "title": "ValidationException",
  "status": 400,
  "detail": "One or more validation errors occurred.",
  "errors": {
    "": ["At least one FeverPAIN criterion must be provided"]
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
