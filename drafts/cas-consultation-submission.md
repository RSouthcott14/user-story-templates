# API - POST /api/v1/patients/{patientId}/consultations/ccm - Submit CCM CAS Consultation

## Story Type: API | Feature: 577036 | Effort: [TBD]

---

## Description

### Story Card
**As a** Clinical System
**I want** to submit a completed CCM consultation record to the API
**So that** the consultation is persisted and available for clinical audit and reporting

### Background
The CCM service allows community pharmacists to assess and treat patients with common ailments via two routes: CAS (Common Ailments Service) and IP (Independent Prescriber). Both use the same submission endpoint. **This story covers the CAS route only** — IP is out of scope and will be delivered separately.

At the end of a CAS consultation the clinical system submits the complete record in a single call: consultation metadata, presenting symptoms, condition, clinical assessment (sore throat with FeverPAIN/Centor scoring, UTI with optional dip test, or general notes), medicines supplied, referral details if applicable, and a sensitivity flag.

---

## Endpoint Details

**HTTP Method:** POST
**Endpoint:** `POST /api/v1/patients/{patientId}/consultations/ccm`

> The endpoint is shared by both the CAS and IP service routes. The route is determined by `consultationDetails.serviceRoute` in the request body — `"cas"` for this story, `"ip"` for the IP story.

### Path Parameters
- `patientId`: [string (UUID), required] — Choose Pharmacy internal patient identifier

---

## Request

### Request Headers
```
Authorization: Bearer {token}
Content-Type: application/json
Accept: application/json
```

### Request Body

> The stable sections (`consultationDetails`, `symptomsAndHistory`, `condition`, `isSensitive`) are identical across all paths and shown once in the baseline. Only the sections that change per path are shown in the variant sections below.


---

#### Baseline — path 1 (CAS · sore-throat · FeverPAIN · supply)

```json
{
  "consultationDetails": {
    "gphcNumber": "2034567",
    "pharmacyCode": "601619C",                        // ODS code — used to identify the dispensing organisation
    "consultationDateTime": "2026-11-02T14:30:00+00:00",
    "consultationUndertaken": "face-to-face-pharmacy", // ConsultationUndertaken
    "referredBy": "self",                              // ReferredBy
    "serviceRoute": "cas"                              // ServiceRoute — cas only (ip is out of scope)
  },

  "symptomsAndHistory": {
    "presentingSymptoms": [
      {                                 // CodedElement — ValueSet: ChoosePharmacy-CCMSymptoms (56 SNOMED concepts)
        "code": "267036007",
        "display": "Dyspnoea",
        "system": "http://snomed.info/sct"
      }
    ],
    "medicalHistory": "string"
  },

  "condition": {
    "concept": {                        // CodedElement — ValueSet: ChoosePharmacy-CommonAilments (27 SNOMED concepts)
      "code": "267102003",
      "display": "Sore throat",
      "system": "http://snomed.info/sct"
    },
    "conditionNotFound": false
  },

  "clinicalAssessment": {
    "template": "sore-throat",          // ClinicalTemplate — sore-throat | uti | general

    "soreThroat": {
      "scoringTool": "FeverPAIN",       // ScoringTool — FeverPAIN | Centor
      "score": 4,                       // integer — computed by scoring endpoint

      "feverpainAnswers": {
        "coughOrColdSymptoms": "none",           // CoughOrColdSeverity
        "historyOfFeverInLast24Hours": true,
        "onsetOfIllness": "zero-to-three-days",  // IllnessOnsetDuration
        "inflamedTonsils": "severe",             // TonsilInflammationSeverity
        "pusOnTonsils": true
      },

      "centorAnswers": null,            // null when scoringTool = FeverPAIN

      "assessmentNotes": "string",
      "pointOfCareTest": {
        "patientConsent": true,
        "name": "alere-testpack",       // PointOfCareTestName
        "batchNumber": "string",
        "expiryDate": "2026-12",
        "testResult": true,
        "resultGivenToPatient": true,
        "reasonNotGiven": "string",     // populated when resultGivenToPatient = false
        "notes": "string"
      }
    }
  },

  "outcome": {
    "selection": "supply",             // ConsultationOutcome

    "supply": {
      "casSupply": {
        "medicinesSupplied": [
          {
            "drugId": "775",
            "medicineName": "Benzoyl peroxide 5% gel (30g)",
            "packDescription": "30G",
            "quantitySupplied": 2,
            "dosage": "string",
            "vpId": "41737111000001107",
            "vmppId": "18094211000001107",
            "vtmId": "774811000",
            "mdrppaCode": "06521103",
            "isAntibiotic": false,
            "isBackupSupply": null        // null when isAntibiotic = false
          }
        ]
      },
      "adviceToPatient": "string"
    }
  },

  "isSensitive": false
}
```

---

#### Variant 1 — Clinical assessment

Replace `clinicalAssessment` with the appropriate block for the template in use.

**Centor scoring (path 2)**
```json
"clinicalAssessment": {
  "template": "sore-throat",
  "soreThroat": {
    "scoringTool": "Centor",
    "score": 3,
    "feverpainAnswers": null,
    "centorAnswers": {
      "exudateOrSwellingOnTonsils": true,
      "tenderSwollenAnteriorCervicalLymphNodes": true,
      "temperatureAbove38": true,
      "cough": false                    // false (absence of cough) scores 1
    },
    "assessmentNotes": "string",
    "pointOfCareTest": { }              // same shape as baseline
  }
}
```

**UTI with dip test (path 3)**
```json
"clinicalAssessment": {
  "template": "uti",
  "uti": {
    "diagnosis": {
      "dysuria": true,
      "nocturia": true,
      "cloudyUrine": true
    },
    "clinicalAssessment": {
      "urgency": true,
      "frequency": true,
      "haematuria": true,
      "suprapubicTenderness": true,
      "temperature": 38,
      "clinicalNotes": "string",
      "dipTestRequired": true
    },
    "dipTest": {
      "consent": true,
      "testName": "siemens-multistik-8sg",  // DipStickTestName
      "batchNumber": "string",
      "expiryDate": "2026-12",
      "nitrates": true,
      "leukocytes": true,
      "redBloodCells": true,
      "notes": "string",
      "resultGivenToPatient": true,
      "reasonNotGiven": "string"
    }
  }
}
```

**UTI without dip test (path 4)** — `dipTestRequired: false`, `dipTest` omitted
```json
"clinicalAssessment": {
  "template": "uti",
  "uti": {
    "diagnosis": { },                   // same shape as above
    "clinicalAssessment": {
      "urgency": true,
      "frequency": true,
      "haematuria": true,
      "suprapubicTenderness": true,
      "temperature": 38,
      "clinicalNotes": "string",
      "dipTestRequired": false
    }
  }
}
```

**General (path 5)**
```json
"clinicalAssessment": {
  "template": "general",
  "general": {
    "clinicalAssessmentNotes": "string",
    "examinationNotes": "string"
  }
}
```

---

#### Variant 2 — Outcome

Replace `outcome` with the appropriate block for the selected outcome.

**supply-and-refer**
```json
"outcome": {
  "selection": "supply-and-refer",
  "supply": { },                        // same shape as baseline
  "referral": {
    "referredTo": "gp",                 // ReferralDestination
    "timescale": "within-24-hours",     // ReferralTimescale
    "information": "string",
    "adviceToPatient": "string"
  }
}
```

**refer**
```json
"outcome": {
  "selection": "refer",
  "referral": {
    "referredTo": "gp",
    "timescale": "within-24-hours",
    "information": "string",
    "adviceToPatient": "string"
  }
}
```

**advice-only**
```json
"outcome": {
  "selection": "advice-only",
  "advice": {
    "adviceToPatient": "string"
  }
}
```

---

#### Variant 3 — Condition not found (path 6)

Replace `condition` and `outcome` with:

```json
"condition": {
  "conditionNotFound": true             // concept omitted — only refer or advice-only are valid outcomes
},

"outcome": {
  "selection": "refer",                 // refer | advice-only only
  "referral": {
    "referredTo": "gp",
    "timescale": "within-24-hours",
    "information": "string",
    "adviceToPatient": "string"
  }
}
```

---

## Code Systems

### ConsultationUndertaken (Existing)

**File:** `/src/ChoosePharmacy.Domain.Types/Consultation/ConsultationUndertaken.cs`
**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/consultation-undertaken`

| Code | Display |
|------|---------|
| `face-to-face-pharmacy` | Face to face at the pharmacy |
| `telephone` | Telephone |
| `video` | Video |
| `face-to-face-other-location` | Face to face at a location other than the pharmacy |

### ReferredBy (Existing)

**File:** `/src/ChoosePharmacy.Domain.Types/Consultation/ReferredBy.cs`
**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/referred-by`

| Code | Display |
|------|---------|
| `self` | Self |
| `gp` | GP |
| `out-of-hours` | Out of hours |
| `optometrist` | Optometrist |
| `ae` | A&E |
| `nhs-direct` | NHS Direct |
| `other-healthcare` | Other healthcare professional |
| `other-pharmacy` | Other pharmacy |
| `other` | Other |

### ConsultationOutcome (Existing)

**File:** `/src/ChoosePharmacy.Domain.Types/Consultation/ConsultationOutcome.cs`
**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/consultation-outcome`

| Code | Display |
|------|---------|
| `supply` | Supply |
| `supply-and-refer` | Supply and Refer |
| `refer` | Refer |
| `advice-only` | Advice Only |

### ReferralDestination (Existing)

**File:** `/src/ChoosePharmacy.Domain.Types/Consultation/ReferralDestination.cs`
**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/referral-destination`

| Code | Display |
|------|---------|
| `gp` | GP |
| `dentist` | Dentist |
| `optometrist` | Optometrist |
| `out-of-hours` | Out of Hours |
| `ae` | A&E |
| `other` | Other |

### ReferralTimescale (Existing)

**File:** `/src/ChoosePharmacy.Domain.Types/Consultation/ReferralTimescale.cs`
**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/referral-timescale`

| Code | Display |
|------|---------|
| `within-24-hours` | Within 24 hours |
| `within-48-hours` | Within 48 hours |
| `within-7-days` | Within 7 days |
| `within-14-days` | Within 14 days |
| `within-28-days` | Within 28 days |

### ServiceRoute

**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/service-route`

> Identifies the service route. Only `cas` is in scope for this story. The `ip` value is reserved for the IP route story.

| Code | Display |
|------|---------|
| `cas` | Common Ailments Service |
| `ip` | Independent Prescriber *(out of scope)* |

### ClinicalTemplate

**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/clinical-template`

> Declares which clinical pathway was followed within the CAS consultation. Determines which `clinicalAssessment` sub-object is populated and which validation rules apply.

| Code | Display |
|------|---------|
| `sore-throat` | Sore Throat |
| `uti` | UTI |
| `general` | General Clinical Assessment |

### DipStickTestName

**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/dip-stick-test-name`

| Code | Display |
|------|---------|
| `siemens-multistik-8sg` | Siemens Multistik 8SG |
| `siemens-multistik-10sg` | Siemens Multistik 10SG |
| `sterilab-combiscreen-5-sys` | Sterilab CombiScreen 5 SYS test strips |
| `roche-combur` | Roche Combur - test strips |

### ScoringTool

**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/scoring-tool`

| Code | Display |
|------|---------|
| `FeverPAIN` | FeverPAIN |
| `Centor` | Centor |

### CoughOrColdSeverity (Existing — Domain Type)

**File:** `/src/ChoosePharmacy.Domain.Types/ClinicalAssessments/FeverPAIN/CoughOrColdSeverity.cs`

> Used in `feverpainAnswers.coughOrColdSymptoms`. FeverPAIN criterion: absence of cough or coryza — `none` scores 1.

| Value | Display |
|-------|---------|
| `none` | None |
| `mild` | Mild |
| `moderate` | Moderate |
| `severe` | Severe |

### IllnessOnsetDuration (Existing — Domain Type)

**File:** `/src/ChoosePharmacy.Domain.Types/ClinicalAssessments/FeverPAIN/IllnessOnsetDuration.cs`

> Used in `feverpainAnswers.onsetOfIllness`. FeverPAIN criterion: rapidly worsening onset — `zero-to-three-days` scores 1.

| Value | Display |
|-------|---------|
| `zero-to-three-days` | 0 to 3 days |
| `four-to-seven-days` | 4 to 7 days |
| `greater-than-seven-days` | Greater than 7 days |

### TonsilInflammationSeverity (Existing — Domain Type)

**File:** `/src/ChoosePharmacy.Domain.Types/ClinicalAssessments/FeverPAIN/TonsilInflammationSeverity.cs`

> Used in `feverpainAnswers.inflamedTonsils`. FeverPAIN criterion: severely inflamed tonsils — `severe` scores 1.

| Value | Display |
|-------|---------|
| `none` | None |
| `mild` | Mild |
| `moderate` | Moderate |
| `severe` | Severe |

### PointOfCareTestName

**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/point-of-care-test-name`

| Code | Display |
|------|---------|
| `alere-testpack` | Alere TestPack (Abbott) |
| `bd-veritor` | BD Veritor™ |
| `osom` | OSOM® (Sekisui/Veredus) |
| `nadal-mpi` | NADAL® MPI A1+A2 |
| `wondfo-one-step` | Wondfo One Step |

---

## Response

### Success Response (201 Created)

```json
{
  "consultationId": "b2f8f6d4-0f7c-4359-aafd-3ae02f317470"
}
```

**Fields:**
- `consultationId`: [string (UUID)] — Unique identifier of the created consultation record

---

## Happy Path Matrix

The request body shape varies across 4 independent dimensions for the CAS route. The table below identifies the recommended set of paths that give full structural coverage without testing every permutation.

| # | Condition | Template | Variant | Valid outcomes | Structural note |
|---|-----------|----------|---------|----------------|-----------------|
| 1 | found | sore-throat | FeverPAIN | supply, supply-and-refer, refer, advice-only | `feverpainAnswers` populated; `centorAnswers` null |
| 2 | found | sore-throat | Centor | supply, supply-and-refer, refer, advice-only | `centorAnswers` populated; `feverpainAnswers` null |
| 3 | found | UTI | with dip test | supply, supply-and-refer, refer, advice-only | `dipTest` object present |
| 4 | found | UTI | no dip test | supply, supply-and-refer, refer, advice-only | `dipTest` omitted |
| 5 | found | general | — | supply, supply-and-refer, refer, advice-only | `clinicalAssessment.general` sub-object only |
| 6 | not found | any | — | refer, advice-only | `conditionNotFound = true` — supply and supply-and-refer blocked |

---

## Acceptance Criteria

**Total Scenarios: 58**

> **Note:** Scenario 35 (serviceRoute "ip" — 501 Not Implemented) has been removed. The IP route is now covered by a separate story: `drafts/ip-consultation-submission.md`.

---

### CAS — Sore-throat

**Scenario 1: Happy path — FeverPAIN sore-throat consultation with supply**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS sore-throat consultation is submitted with scoringTool "FeverPAIN" and outcome "supply"
Then the API returns status 201
And the consultation is persisted with the soreThroat clinical assessment
And feverpainAnswers and the computed score are stored against the consultation
And the medicines supplied are recorded
```

**Scenario 2: Happy path — Centor sore-throat consultation with supply**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS sore-throat consultation is submitted with scoringTool "Centor" and outcome "supply"
Then the API returns status 201
And centorAnswers are persisted against the consultation
And feverpainAnswers is null
```

**Scenario 3: Happy path — Sore-throat consultation with supply-and-refer**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS sore-throat consultation is submitted with outcome "supply-and-refer"
Then the API returns status 201
And the medicines supplied are recorded against the consultation
And the referral details are recorded against the consultation
```

**Scenario 4: Happy path — Sore-throat consultation with refer**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS sore-throat consultation is submitted with outcome "refer"
Then the API returns status 201
And the referral details are persisted
And no supply record is created
```

**Scenario 5: Happy path — Sore-throat consultation with advice-only**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS sore-throat consultation is submitted with outcome "advice-only"
Then the API returns status 201
And the advice to patient is persisted
And no supply record is created
And no referral record is created
```

**Scenario 6: Happy path — Sore-throat consultation without point of care test**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS sore-throat consultation is submitted with the pointOfCareTest object omitted and outcome "supply"
Then the API returns status 201
And the consultation is persisted without a point of care test record
```

**Scenario 7: Happy path — Sore-throat consultation with point of care test consent declined**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS sore-throat consultation is submitted with a pointOfCareTest where patientConsent is "false" and test data (name, batchNumber, testResult) is omitted
Then the API returns status 201
And the consultation is persisted without point of care test result data
```

---

### CAS — UTI

**Scenario 8: Happy path — UTI consultation with dip test and supply**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS UTI consultation is submitted with dipTestRequired "true" and outcome "supply"
Then the API returns status 201
And the UTI clinical assessment is persisted
And the dip test results are recorded against the consultation
```

**Scenario 9: Happy path — UTI consultation without dip test and supply**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS UTI consultation is submitted with dipTestRequired "false" and outcome "supply"
Then the API returns status 201
And the UTI clinical assessment is persisted
And no dip test record is created
```

**Scenario 10: Happy path — UTI consultation with dip test and patient consent declined**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS UTI consultation is submitted with dipTestRequired "true" and dipTest.consent "false" and no test result data
Then the API returns status 201
And the consultation is persisted with the dip test consent recorded as declined
```

---

### CAS — General

**Scenario 11: Happy path — General assessment with supply**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS consultation is submitted with template "general" and outcome "supply"
Then the API returns status 201
And the general clinical assessment notes are persisted
```

---

### CAS — Condition not found

**Scenario 12: Happy path — Condition not found with refer**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS consultation is submitted with conditionNotFound "true" and outcome "refer"
Then the API returns status 201
And the consultation is persisted with conditionNotFound recorded
And the referral details are persisted
```

**Scenario 13: Happy path — Condition not found with advice-only**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS consultation is submitted with conditionNotFound "true" and outcome "advice-only"
Then the API returns status 201
And the consultation is persisted with conditionNotFound recorded
And the advice to patient is persisted
```

---

### CAS — Supply

**Scenario 14: Happy path — Antibiotic supply with isBackupSupply false**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS consultation is submitted with a supply outcome and a medicinesSupplied entry where isAntibiotic is "true" and isBackupSupply is "false"
Then the API returns status 201
And the consultation is persisted with isAntibiotic "true" and isBackupSupply "false" recorded against the medicine
```

> **Matrix coverage:** Scenarios 1–7 cover sore-throat (both scoring tools, all four outcomes, and the point of care test consent paths). Scenarios 8–10 cover UTI (with dip test, without dip test, and dip test consent declined). Scenario 11 covers general. Scenarios 12–13 cover condition not found. Scenario 14 covers the antibiotic supply branch.

---

### Validation

#### Patient & Organisation

**Scenario 15: Patient not found**
```gherkin
Given a patientId that does not exist in the system
When POST /api/v1/patients/{patientId}/consultations/cas is called
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message is "Patient with ID {patientId} not found"
And no data is persisted
```

**Scenario 16: Organisation not found**
```gherkin
Given a request body containing a pharmacyCode that does not exist in the system
When POST /api/v1/patients/{patientId}/consultations/cas is called
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message is "Organisation with ODS code {pharmacyCode} not found"
And no data is persisted
```

#### Required Fields

**Scenario 17: Required top-level and consultation fields missing**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with <field> null or omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| field                                  | errorMessage                                            |
| consultationDetails                    | "'consultationDetails' must not be empty."              |
| symptomsAndHistory                     | "'symptomsAndHistory' must not be empty."               |
| condition                              | "'condition' must not be empty."                        |
| clinicalAssessment                     | "'clinicalAssessment' must not be empty."               |
| outcome                                | "'outcome' must not be empty."                          |
| outcome.selection                      | "'selection' must not be empty."                        |
| isSensitive                            | "'isSensitive' must not be empty."                      |
| consultationDetails.consultationDateTime | "'consultationDateTime' must not be empty."            |
| consultationDetails.pharmacyCode       | "'pharmacyCode' must not be empty."                     |
| consultationDetails.gphcNumber         | "'gphcNumber' must not be empty."                       |
| consultationDetails.consultationUndertaken | "'consultationUndertaken' must not be empty."        |
| consultationDetails.referredBy         | "'referredBy' must not be empty."                       |
| consultationDetails.serviceRoute       | "'serviceRoute' must not be empty."                     |
| clinicalAssessment.template            | "'template' must not be empty."                         |
```

**Scenario 18: Sore-throat assessment required fields missing**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a sore-throat consultation is submitted with <field> null or omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| field                                  | errorMessage                                            |
| clinicalAssessment.soreThroat.scoringTool | "Scoring tool is required for a sore throat assessment" |
| clinicalAssessment.soreThroat.score    | "Score is required for a sore throat assessment"        |
```

**Scenario 19: Supply medicine required fields missing**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with a supply outcome and a medicinesSupplied entry where <field> is null or omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| field        | errorMessage                                                |
| medicineName | "Medicine name is required for each medicine supplied"      |
| drugId       | "Drug identifier is required for each medicine supplied"    |
```

**Scenario 20: Referral required fields missing**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with a referral object where <field> is null or omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| field       | errorMessage                           |
| referredTo  | "'referredTo' must not be empty."      |
| timescale   | "'timescale' must not be empty."       |
```

#### Consultation Details

**Scenario 21: consultationDateTime is in the future**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with a consultationDateTime that is after the current UTC time
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Consultation date cannot be in the future"
And no data is persisted
```

**Scenario 22: consultationDateTime is more than one year in the past**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with a consultationDateTime more than 365 days before the current UTC time
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Consultation date cannot be over a year in the past"
And no data is persisted
```

**Scenario 23: pharmacyCode fails format validation**
```gherkin
Given a valid patient exists with patientId {patientId}
When a consultation is submitted with a pharmacyCode that does not conform to the pharmacy account number format (7 characters, starting with "6", ending with a letter)
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Enter a valid pharmacy location number"
And no data is persisted
```

**Scenario 24: GPhC number fails format validation**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with a gphcNumber that violates <rule>
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| rule                                          | errorMessage                                |
| not exactly 7 characters                      | "GPhC number must be exactly 7 digits long" |
| contains letters, spaces, or special characters | "Enter a valid GPhC number"               |
```

#### Condition

**Scenario 25: presentingSymptoms submitted as empty array**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS consultation is submitted with an empty presentingSymptoms array
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "At least one presenting symptom must be provided for a CAS consultation"
And no data is persisted
```

**Scenario 26: conditionNotFound false submitted without condition concept**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS consultation is submitted with conditionNotFound "false" and the condition concept omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Condition concept is required when the condition has been found"
And no data is persisted
```

**Scenario 27: conditionNotFound true submitted with condition concept also provided**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS consultation is submitted with conditionNotFound "true" and a condition concept also populated
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Condition concept must not be provided when condition is not found"
And no data is persisted
```

**Scenario 28: Disallowed outcome when condition not found**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with conditionNotFound "true" and outcome <outcome>
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| outcome         | errorMessage                                                                                              |
| supply          | "Outcome 'supply' is not permitted when condition is not found. Valid outcomes are: refer, advice-only"   |
| supply-and-refer | "Outcome 'supply-and-refer' is not permitted when condition is not found. Valid outcomes are: refer, advice-only" |
```

**Scenario 29: conditionNotFound true submitted with a sore-throat or UTI template**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with conditionNotFound "true" and a clinical template of "sore-throat" or "uti"
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Clinical template must be 'general' when condition is not found"
And no data is persisted
```

**Scenario 30: Clinical template does not match the submitted condition**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with a condition concept that does not correspond to the clinical template (e.g. condition is "Urinary tract infection" but template is "sore-throat")
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "The clinical template does not match the submitted condition"
And no data is persisted
```

#### Clinical Assessment — Structure

**Scenario 31: Template submitted without required sub-object**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with template <template> and the corresponding sub-object omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| template    | errorMessage                                                      |
| sore-throat | "Sore throat assessment is required when template is sore-throat" |
| uti         | "UTI assessment is required when template is uti"                 |
| general     | "General assessment is required when template is general"         |
```

**Scenario 32: Template submitted with incorrect sub-object present**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with template <template> and an incorrect sub-object also provided
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| template    | errorMessage                                                                   |
| sore-throat | "Only the sore-throat assessment must be provided when template is sore-throat" |
| uti         | "Only the UTI assessment must be provided when template is uti"                |
| general     | "Only the general assessment must be provided when template is general"        |
```

#### Sore-Throat Assessment

**Scenario 33: Scoring tool answers required but missing**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a sore-throat consultation is submitted with scoringTool <tool> and the corresponding answers null
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| tool     | errorMessage                                                   |
| FeverPAIN | "FeverPAIN answers are required when scoring tool is FeverPAIN" |
| Centor   | "Centor answers are required when scoring tool is Centor"      |
```

**Scenario 34: Scoring tool answers must not be provided**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a sore-throat consultation is submitted with scoringTool <tool> and the opposing answers object populated
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| tool     | errorMessage                                                        |
| FeverPAIN | "Centor answers must not be provided when scoring tool is FeverPAIN" |
| Centor   | "FeverPAIN answers must not be provided when scoring tool is Centor" |
```

**Scenario 35: Score outside valid range**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a sore-throat consultation is submitted with scoringTool <tool> and a score value outside the valid range
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| tool     | errorMessage                              |
| FeverPAIN | "FeverPAIN score must be between 0 and 5" |
| Centor   | "Centor score must be between 0 and 4"   |
```

**Scenario 36: Submitted score does not match the score computed from the answers**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a sore-throat consultation is submitted where the score value does not match the score calculated from the provided scoring tool answers
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Score does not match the answers provided"
And no data is persisted
```

#### UTI Assessment

**Scenario 37: dipTestRequired true submitted without dipTest object**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a UTI consultation is submitted with dipTestRequired "true" and the dipTest object omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Dip test results are required when dipTestRequired is true"
And no data is persisted
```

**Scenario 38: dipTestRequired false submitted with dipTest object present**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a UTI consultation is submitted with dipTestRequired "false" and a dipTest object also provided
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Dip test results must not be provided when dipTestRequired is false"
And no data is persisted
```

#### Clinical Assessment — Temperature

**Scenario 39: Temperature out of valid range**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with a temperature value that is less than 30 or greater than 42
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Enter a valid temperature between 30°C and 42°C"
And no data is persisted
```

#### Test Kit

**Scenario 40: Test kit consent false but test data present**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with <testKit>.patientConsent "false" and test data also populated
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| testKit          | errorMessage                                                                              |
| pointOfCareTest  | "Point of care test data must not be provided when patient consent has not been given"    |
| dipTest          | "Dip test result data must not be provided when patient consent has not been given"       |
```

**Scenario 41: Test kit consent true but test data omitted**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with <testKit>.consent "true" and test data omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| testKit          | errorMessage                                                                      |
| pointOfCareTest  | "Point of care test data is required when patient consent has been given"         |
| dipTest          | "Dip test result data is required when patient consent has been given"            |
```

**Scenario 42: Test kit name field required when consent given**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with <testKit>.consent "true" and the kit name field <field> null
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| testKit          | field    | errorMessage                      |
| pointOfCareTest  | name     | "'name' must not be empty."       |
| dipTest          | testName | "'testName' must not be empty."   |
```

**Scenario 43: Test kit resultGivenToPatient false without reasonNotGiven**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with <testKit>.resultGivenToPatient "false" and reasonNotGiven omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| testKit          | errorMessage                                                                                              |
| pointOfCareTest  | "'reasonNotGiven' is required when the point of care test result was not given to the patient"            |
| dipTest          | "'reasonNotGiven' is required when the dip test result was not given to the patient"                      |
```

**Scenario 44: Test kit resultGivenToPatient true with reasonNotGiven populated**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with <testKit>.resultGivenToPatient "true" and reasonNotGiven also populated
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| testKit          | errorMessage                                                                                                      |
| pointOfCareTest  | "'reasonNotGiven' must not be provided when the point of care test result was given to the patient"               |
| dipTest          | "'reasonNotGiven' must not be provided when the dip test result was given to the patient"                         |
```

**Scenario 45: Test kit expiryDate is not a valid partial date format**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with a pointOfCareTest or dipTest where expiryDate is not a valid partial date value (e.g. "2026-13", "26-12", "hello")
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Enter a valid expiry date"
And no data is persisted
```

**Scenario 46: Test kit expiryDate in the past**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with a pointOfCareTest or dipTest where expiryDate is a valid partial date in the past
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Expiry date must not be in the past"
And no data is persisted
```

#### Outcome & Supply

**Scenario 47: Supply object required when outcome is supply or supply-and-refer**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with outcome <outcome> and the supply object omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Supply details are required when outcome is supply or supply-and-refer"
And no data is persisted

Examples:
| outcome         |
| supply          |
| supply-and-refer |
```

**Scenario 48: casSupply object omitted**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with outcome "supply" or "supply-and-refer" and the casSupply object omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "CAS supply details are required when outcome is supply or supply-and-refer"
And no data is persisted
```

**Scenario 49: medicinesSupplied empty when outcome is supply or supply-and-refer**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with outcome <outcome> and medicinesSupplied as an empty array
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "At least one medicine must be supplied when outcome is supply or supply-and-refer"
And no data is persisted

Examples:
| outcome         |
| supply          |
| supply-and-refer |
```

**Scenario 50: quantitySupplied of zero or less**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with a supply or supply-and-refer outcome and a medicinesSupplied entry where quantitySupplied is 0 or less
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "'quantitySupplied' must be greater than '0'."
And no data is persisted
```

**Scenario 51: isBackupSupply rules based on isAntibiotic**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with a supply outcome and a medicinesSupplied entry where isAntibiotic is <isAntibiotic> and isBackupSupply is <isBackupSupply>
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is <errorMessage>
And no data is persisted

Examples:
| isAntibiotic | isBackupSupply | errorMessage                                                             |
| true         | null           | "'isBackupSupply' must not be null when the medicine is an antibiotic"   |
| false        | not null       | "'isBackupSupply' must be null when the medicine is not an antibiotic"   |
```

**Scenario 52: Referral object required when outcome is refer or supply-and-refer**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with outcome <outcome> and the referral object omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Referral details are required when outcome is refer or supply-and-refer"
And no data is persisted

Examples:
| outcome         |
| refer           |
| supply-and-refer |
```

**Scenario 53: advice-only outcome submitted without advice object**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with outcome "advice-only" and the advice object omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Advice details are required when outcome is advice-only"
And no data is persisted
```

**Scenario 54: supply outcome submitted with referral object present**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with outcome "supply" and a referral object also provided
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Referral details must not be provided when outcome is supply"
And no data is persisted
```

**Scenario 55: refer outcome submitted with supply object present**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with outcome "refer" and a supply object also provided
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Supply details must not be provided when outcome is refer"
And no data is persisted
```

**Scenario 56: advice-only outcome submitted with supply or referral object present**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with outcome "advice-only" and a supply or referral object also provided
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Supply and referral details must not be provided when outcome is advice-only"
And no data is persisted
```

#### String Length

The error message format for all string length violations is: `"The length of '{field}' must be {n} characters or fewer. You entered {actual} characters."`

**Scenario 57: String field exceeds maximum character limit**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a consultation is submitted with <field> containing <limit + 1> characters
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "The length of '<fieldName>' must be <limit> characters or fewer. You entered <limit + 1> characters."
And no data is persisted

Examples:
| field                                                     | fieldName               | limit |
| outcome.referral.information                              | information             | 2000  |
| medicinesSupplied[].dosage                                | dosage                  | 500   |
| symptomsAndHistory.medicalHistory                         | medicalHistory          | 2000  |
| clinicalAssessment.soreThroat.assessmentNotes             | assessmentNotes         | 2000  |
| clinicalAssessment.uti.clinicalAssessment.clinicalNotes   | clinicalNotes           | 2000  |
| clinicalAssessment.general.clinicalAssessmentNotes        | clinicalAssessmentNotes | 2000  |
| clinicalAssessment.general.examinationNotes               | examinationNotes        | 2000  |
| clinicalAssessment.soreThroat.pointOfCareTest.notes       | notes                   | 500   |
| clinicalAssessment.uti.dipTest.notes                      | notes                   | 500   |
| pointOfCareTest.reasonNotGiven                            | reasonNotGiven          | 500   |
| dipTest.reasonNotGiven                                    | reasonNotGiven          | 500   |
| outcome.supply.adviceToPatient                            | adviceToPatient         | 2000  |
| outcome.referral.adviceToPatient                          | adviceToPatient         | 2000  |
| outcome.advice.adviceToPatient                            | adviceToPatient         | 2000  |
```

**Scenario 58: ipSupply block submitted on CAS route**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When a CAS consultation is submitted with a supply outcome and an ipSupply block provided instead of casSupply
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "'ipSupply' must not be provided on the CAS service route"
And no data is persisted
```

