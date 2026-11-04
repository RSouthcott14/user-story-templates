# API - POST /api/v1/patients/{patientId}/consultations/ccm - Submit CCM IP Consultation

## Story Type: API | Feature: 577036 | Effort: [TBD]

> **Follows on from:** [Submit CCM CAS Consultation](cas-consultation-submission.md) (Work Item 622108). This story extends `POST /api/v1/patients/{patientId}/consultations/ccm` to support the IP service route; the CAS implementation must be in place first.

---

## Description

### Story Card
**As a** Clinical System
**I want** to submit a completed CCM consultation record via the IP (Independent Prescriber) service route
**So that** the consultation is persisted and available for clinical audit and reporting

### Background
The CCM service delivers two pharmacy service routes from the same submission endpoint: CAS (Common Ailments Service) and IP (Independent Prescriber). This story covers the **IP route only** — CAS is delivered by a separate story.

The IP route differs from CAS in three structural ways:
1. **Template selection is manual** — the clinician explicitly selects a clinical template at step 3b, or proceeds without one (`template: null`). CAS auto-routes by condition.
2. **Working Diagnosis** — after the clinical assessment, the clinician is prompted to confirm a working diagnosis (a SNOMED CT concept). This section is absent from CAS.
3. **IP supply** — medicines are selected via a DM+D typeahead search (full catalogue, generic/branded toggle) rather than a fixed CAS formulary. The supply payload uses different field names and carries no antibiotic-specific identifiers.

The IP route does not support the `conditionNotFound` escape hatch. A condition concept is always required.

---

## Endpoint Details

**HTTP Method:** POST
**Endpoint:** `POST /api/v1/patients/{patientId}/consultations/ccm`

> The endpoint is shared by both the CAS and IP service routes. The route is determined by `consultationDetails.serviceRoute` in the request body — `"ip"` for this story, `"cas"` for the CAS story.

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

> The stable sections (`consultationDetails`, `symptomsAndHistory`, `condition`, `isSensitive`) share the same shape as the CAS story. Only IP-specific differences are highlighted. The `workingDiagnosis` section and `ipSupply` supply shape are new to this story.

---

#### Baseline — path 1 (IP · sore-throat · FeverPAIN · supply · working diagnosis confirmed)

```json
{
  "consultationDetails": {
    "gphcNumber": "2034567",
    "pharmacyCode": "601619C",
    "consultationDateTime": "2026-11-02T14:30:00+00:00",
    "consultationUndertaken": "face-to-face-pharmacy",   // ConsultationUndertaken
    "referredBy": "self",                                 // ReferredBy
    "serviceRoute": "ip"                                  // ServiceRoute — ip only (cas is out of scope for this story)
  },

  "symptomsAndHistory": {
    "presentingSymptoms": [
      {                                   // CodedElement — ValueSet: ChoosePharmacy-CCMSymptoms
        "code": "267036007",
        "display": "Dyspnoea",
        "system": "http://snomed.info/sct"
      }
    ],
    "medicalHistory": "string"
  },

  "condition": {
    "concept": {                          // CodedElement — ValueSet: ChoosePharmacy-CommonAilments
      "code": "267102003",
      "display": "Sore throat",
      "system": "http://snomed.info/sct"
    }
    // conditionNotFound is not permitted on the IP route
  },

  "clinicalAssessment": {
    "template": "sore-throat",            // ClinicalTemplate — manually selected on IP route
                                          // null when no template chosen

    "soreThroat": {
      "scoringTool": "FeverPAIN",         // ScoringTool
      "score": 4,

      "feverpainAnswers": {
        "coughOrColdSymptoms": "none",           // CoughOrColdSeverity
        "historyOfFeverInLast24Hours": true,
        "onsetOfIllness": "zero-to-three-days",  // IllnessOnsetDuration
        "inflamedTonsils": "severe",             // TonsilInflammationSeverity
        "pusOnTonsils": true
      },

      "centorAnswers": null,
      "assessmentNotes": "string",
      "pointOfCareTest": {
        "patientConsent": true,
        "name": "alere-testpack",         // PointOfCareTestName
        "batchNumber": "string",
        "expiryDate": "2026-12",
        "testResult": true,
        "resultGivenToPatient": true,
        "reasonNotGiven": "string",
        "notes": "string"
      }
    }
  },

  "workingDiagnosis": {
    "confirmDiagnosis": true,
    "diagnosisScope": "covered",          // DiagnosisScope — required when confirmDiagnosis = true
    "concept": {                          // CodedElement — SNOMED CT; required when confirmDiagnosis = true
      "code": "267102003",
      "display": "Sore throat",
      "system": "http://snomed.info/sct"
    }
  },

  "outcome": {
    "selection": "supply",               // ConsultationOutcome

    "supply": {
      "ipSupply": {
        "medicinesSupplied": [
          {
            "vpId": "42269311000001100",    // VMP SNOMED CT identifier — always required
            "ampId": null,                   // AMP identifier — required when branded; null for generic
            "vmppId": "1201711000001104",   // VMPP pack identifier — always required
            "amppId": null,                  // AMPP pack identifier — required when ampId provided; null for generic
            "medicineName": "string",
            "quantitySupplied": 1,           // integer — number of packs dispensed
            "dosage": "string",
            "quantityInstruction": "string"  // optional — pack name e.g. "Citalopram 10mg tablets 28 tablet"
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

#### Variant 1 — Working Diagnosis

Replace `workingDiagnosis` with the appropriate block.

**Working diagnosis not confirmed (path — no diagnosis)**
```json
"workingDiagnosis": {
  "confirmDiagnosis": false
  // diagnosisScope and concept must be omitted
}
```

**Working diagnosis confirmed — outside covered conditions (path — outside)**
```json
"workingDiagnosis": {
  "confirmDiagnosis": true,
  "diagnosisScope": "outside",          // DiagnosisScope
  "concept": {
    "code": "1102931",
    "display": "Migraine",
    "system": "http://snomed.info/sct"
  }
}
```

---

#### Variant 2 — Clinical assessment

Same shape as the CAS story. Replace `clinicalAssessment` with the appropriate block.

**Centor scoring**
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
      "cough": false
    },
    "assessmentNotes": "string",
    "pointOfCareTest": { }
  }
}
```

**UTI with dip test**
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
      "testName": "siemens-multistik-8sg",
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

**UTI without dip test**
```json
"clinicalAssessment": {
  "template": "uti",
  "uti": {
    "diagnosis": { },
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

**General**
```json
"clinicalAssessment": {
  "template": "general",
  "general": {
    "clinicalAssessmentNotes": "string",
    "examinationNotes": "string"
  }
}
```

**No template selected**
```json
"clinicalAssessment": {
  "template": null
  // all clinical assessment sub-objects omitted
}
```

---

#### Variant 3 — Outcome

Replace `outcome` with the appropriate block. Shapes are identical to the CAS story, except `casSupply` becomes `ipSupply`.

**supply-and-refer**
```json
"outcome": {
  "selection": "supply-and-refer",
  "supply": {
    "ipSupply": {
      "medicinesSupplied": [
        { /* same shape as baseline */ }
      ]
    },
    "adviceToPatient": "string"
  },
  "referral": {
    "referredTo": "gp",                   // ReferralDestination
    "timescale": "within-24-hours",       // ReferralTimescale
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

## Code Systems

All code systems used in this story are documented below. Those marked **(Existing)** are also implemented in the CAS story. `DiagnosisScope` is new to this story.

### ConsultationUndertaken (Existing)
**File:** `/src/ChoosePharmacy.Domain.Types/Consultation/ConsultationUndertaken.cs`
**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/consultation-undertaken`
*(Also implemented in the [CAS story](cas-consultation-submission.md))*

| Code | Display |
|------|---------|
| `face-to-face-pharmacy` | Face to face at the pharmacy |
| `telephone` | Telephone |
| `video` | Video |
| `face-to-face-other-location` | Face to face at a location other than the pharmacy |

### ReferredBy (Existing)
**File:** `/src/ChoosePharmacy.Domain.Types/Consultation/ReferredBy.cs`
**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/referred-by`
*(Also implemented in the [CAS story](cas-consultation-submission.md))*

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
*(Also implemented in the [CAS story](cas-consultation-submission.md))*

| Code | Display |
|------|---------|
| `supply` | Supply |
| `supply-and-refer` | Supply and Refer |
| `refer` | Refer |
| `advice-only` | Advice Only |

### ReferralDestination (Existing)
**File:** `/src/ChoosePharmacy.Domain.Types/Consultation/ReferralDestination.cs`
**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/referral-destination`
*(Also implemented in the [CAS story](cas-consultation-submission.md))*

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
*(Also implemented in the [CAS story](cas-consultation-submission.md))*

| Code | Display |
|------|---------|
| `within-24-hours` | Within 24 hours |
| `within-48-hours` | Within 48 hours |
| `within-7-days` | Within 7 days |
| `within-14-days` | Within 14 days |
| `within-28-days` | Within 28 days |

### ServiceRoute (Existing)
**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/service-route`
*(Also implemented in the [CAS story](cas-consultation-submission.md))*

| Code | Display |
|------|---------|
| `cas` | Common Ailments Service *(out of scope for this story)* |
| `ip` | Independent Prescriber |

### ClinicalTemplate (Existing)
**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/clinical-template`
*(Also implemented in the [CAS story](cas-consultation-submission.md))*

| Code | Display |
|------|---------|
| `sore-throat` | Sore Throat |
| `uti` | UTI |
| `general` | General Clinical Assessment |
| *(JSON null)* | No template selected *(IP route only)* |

### ScoringTool (Existing)
**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/scoring-tool`
*(Also implemented in the [CAS story](cas-consultation-submission.md))*

| Code | Display |
|------|---------|
| `FeverPAIN` | FeverPAIN |
| `Centor` | Centor |

### PointOfCareTestName (Existing)
**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/point-of-care-test-name`
*(Also implemented in the [CAS story](cas-consultation-submission.md))*

| Code | Display |
|------|---------|
| `alere-testpack` | Alere TestPack (Abbott) |
| `bd-veritor` | BD Veritor™ |
| `osom` | OSOM® (Sekisui/Veredus) |
| `nadal-mpi` | NADAL® MPI A1+A2 |
| `wondfo-one-step` | Wondfo One Step |

### DipStickTestName (Existing)
**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/dip-stick-test-name`
*(Also implemented in the [CAS story](cas-consultation-submission.md))*

| Code | Display |
|------|---------|
| `siemens-multistik-8sg` | Siemens Multistik 8SG |
| `siemens-multistik-10sg` | Siemens Multistik 10SG |
| `sterilab-combiscreen-5-sys` | Sterilab CombiScreen 5 SYS test strips |
| `roche-combur` | Roche Combur - test strips |

### DiagnosisScope (New)
**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/diagnosis-scope`

> Indicates whether the clinician's working diagnosis falls within the CCM conditions scope (conditions the IP service is designed to treat), or outside it. Required when `workingDiagnosis.confirmDiagnosis = true`.

| Code | Display |
|------|---------|
| `covered` | Within CCM conditions scope |
| `outside` | Outside CCM conditions scope |

#### Domain Types

Domain type records for enumeration values. Each inherits from `Lookup<string>` and does not require a FHIR URI — they are used for internal validation only and are not persisted with encounter data.

##### CoughOrColdSeverity (Existing)
**File:** `/src/ChoosePharmacy.Domain.Types/ClinicalAssessments/FeverPAIN/CoughOrColdSeverity.cs`

| Id | Code | Name |
|---|---|---|
| `none` | `none` | None |
| `mild` | `mild` | Mild |
| `moderate` | `moderate` | Moderate |
| `severe` | `severe` | Severe |

##### IllnessOnsetDuration (Existing)
**File:** `/src/ChoosePharmacy.Domain.Types/ClinicalAssessments/FeverPAIN/IllnessOnsetDuration.cs`

| Id | Code | Name |
|---|---|---|
| `zero-to-three-days` | `zero-to-three-days` | 0 to 3 days |
| `four-to-seven-days` | `four-to-seven-days` | 4 to 7 days |
| `greater-than-seven-days` | `greater-than-seven-days` | Greater than 7 days |

##### TonsilInflammationSeverity (Existing)
**File:** `/src/ChoosePharmacy.Domain.Types/ClinicalAssessments/FeverPAIN/TonsilInflammationSeverity.cs`

| Id | Code | Name |
|---|---|---|
| `none` | `none` | None |
| `mild` | `mild` | Mild |
| `moderate` | `moderate` | Moderate |
| `severe` | `severe` | Severe |

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

| # | Template | Scoring / variant | Working Dx | Outcome |
|---|----------|-------------------|------------|---------|
| 1 | sore-throat | FeverPAIN | confirmed (covered) | supply |
| 2 | sore-throat | Centor | confirmed (covered) | supply |
| 3 | uti | with dip test | confirmed (outside) | supply |
| 4 | uti | no dip test | not confirmed | supply |
| 5 | general | — | confirmed (covered) | supply |
| 6 | sore-throat | FeverPAIN | confirmed (covered) | supply-and-refer |
| 7 | null (no template) | — | not confirmed | refer |
| 8 | null (no template) | — | not confirmed | advice-only |

---

## Acceptance Criteria

**Total Scenarios: 35**

> Validation scenarios for shared behaviour (Patient & Organisation, scoring tool answers, UTI assessment, outcome cross-field mutual exclusion, referral fields, and string lengths for shared fields) are covered by the CAS story and are not repeated here.
>
> All happy path scenarios assume a valid `workingDiagnosis` block is present unless `workingDiagnosis` itself is the subject of the scenario.

---

### IP — Sore-throat

**Scenario 1: Happy path — FeverPAIN sore-throat consultation with supply and working diagnosis confirmed (covered)**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP sore-throat consultation is submitted with scoringTool "FeverPAIN", workingDiagnosis.confirmDiagnosis "true", diagnosisScope "covered", and outcome "supply"
Then the API returns status 201
And the consultation is persisted with serviceRoute "ip"
And the soreThroat clinical assessment is stored against the consultation
And feverpainAnswers and the computed score are stored against the consultation
And the working diagnosis concept, diagnosisScope "covered", and confirmDiagnosis "true" are persisted
And the medicines supplied are recorded via the ipSupply block
```

**Scenario 2: Happy path — Centor sore-throat consultation with supply**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP sore-throat consultation is submitted with scoringTool "Centor" and outcome "supply"
Then the API returns status 201
And centorAnswers are persisted against the consultation
And feverpainAnswers is null
```

**Scenario 3: Happy path — Sore-throat consultation with supply-and-refer**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP sore-throat consultation is submitted with outcome "supply-and-refer"
Then the API returns status 201
And the medicines supplied are recorded via the ipSupply block
And the referral details are recorded against the consultation
```

**Scenario 4: Happy path — Sore-throat consultation with refer**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP sore-throat consultation is submitted with outcome "refer"
Then the API returns status 201
And the referral details are persisted
And no supply record is created
```

**Scenario 5: Happy path — Sore-throat consultation with advice-only**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP sore-throat consultation is submitted with outcome "advice-only"
Then the API returns status 201
And the advice to patient is persisted
And no supply record is created
And no referral record is created
```

**Scenario 6: Happy path — Sore-throat consultation without point of care test**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP sore-throat consultation is submitted with the pointOfCareTest object omitted and outcome "supply"
Then the API returns status 201
And the consultation is persisted without a point of care test record
```

---

### IP — UTI

**Scenario 7: Happy path — UTI consultation with dip test, working diagnosis confirmed (outside), and supply**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP UTI consultation is submitted with dipTestRequired "true", workingDiagnosis.confirmDiagnosis "true", diagnosisScope "outside", and outcome "supply"
Then the API returns status 201
And the UTI clinical assessment is persisted
And the dip test results are recorded against the consultation
And the working diagnosis is persisted with diagnosisScope "outside"
```

**Scenario 8: Happy path — UTI consultation without dip test and working diagnosis not confirmed**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP UTI consultation is submitted with dipTestRequired "false", workingDiagnosis.confirmDiagnosis "false", and outcome "supply"
Then the API returns status 201
And the UTI clinical assessment is persisted
And no dip test record is created
And the consultation is persisted with confirmDiagnosis "false" and no working diagnosis concept
```

**Scenario 9: Happy path — UTI consultation with dip test and patient consent declined**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP UTI consultation is submitted with dipTestRequired "true" and dipTest.consent "false" and no test result data
Then the API returns status 201
And the consultation is persisted with the dip test consent recorded as declined
```

---

### IP — General

**Scenario 10: Happy path — General assessment with supply and working diagnosis confirmed**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with template "general", workingDiagnosis.confirmDiagnosis "true", diagnosisScope "covered", and outcome "supply"
Then the API returns status 201
And the general clinical assessment notes are persisted
And the working diagnosis is persisted with diagnosisScope "covered"
```

---

### IP — No template

**Scenario 11: Happy path — No template selected with refer**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with clinicalAssessment.template null, workingDiagnosis.confirmDiagnosis "false", and outcome "refer"
Then the API returns status 201
And the consultation is persisted with no clinical assessment sub-object
And the referral details are persisted
```

**Scenario 12: Happy path — No template selected with advice-only**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with clinicalAssessment.template null, workingDiagnosis.confirmDiagnosis "false", and outcome "advice-only"
Then the API returns status 201
And the consultation is persisted with no clinical assessment sub-object
And the advice to patient is persisted
```

---

### Working Diagnosis

**Scenario 13: workingDiagnosis object omitted on IP route**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with the workingDiagnosis object omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "'workingDiagnosis' must not be empty."
And no data is persisted
```

**Scenario 14: confirmDiagnosis omitted from workingDiagnosis**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with the workingDiagnosis object present but confirmDiagnosis null or omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "'confirmDiagnosis' must not be empty."
And no data is persisted
```

**Scenario 15: confirmDiagnosis true submitted without concept**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with workingDiagnosis.confirmDiagnosis "true" and the concept omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "'concept' is required when confirmDiagnosis is true"
And no data is persisted
```

**Scenario 16: confirmDiagnosis true submitted without diagnosisScope**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with workingDiagnosis.confirmDiagnosis "true" and diagnosisScope null or omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "'diagnosisScope' is required when confirmDiagnosis is true"
And no data is persisted
```

**Scenario 17: confirmDiagnosis false submitted with concept populated**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with workingDiagnosis.confirmDiagnosis "false" and a concept also populated
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "'concept' must not be provided when confirmDiagnosis is false"
And no data is persisted
```

**Scenario 18: confirmDiagnosis false submitted with diagnosisScope populated**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with workingDiagnosis.confirmDiagnosis "false" and diagnosisScope also populated
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "'diagnosisScope' must not be provided when confirmDiagnosis is false"
And no data is persisted
```

---

### Validation — Condition

**Scenario 19: conditionNotFound flag submitted on IP route**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with conditionNotFound "true"
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "The conditionNotFound flag is not permitted on the IP service route"
And no data is persisted
```

**Scenario 20: Condition concept omitted on IP route**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with the condition concept omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Condition concept is required for an IP consultation"
And no data is persisted
```

---

### Validation — Clinical Assessment (null template)

**Scenario 21: Null template submitted with a clinical assessment sub-object present**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with clinicalAssessment.template null and a soreThroat, uti, or general sub-object also provided
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Clinical assessment sub-objects must not be provided when no template is selected"
And no data is persisted
```

**Scenario 22: Null template submitted with a supply outcome**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with clinicalAssessment.template null and outcome "supply"
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Outcome 'supply' is not permitted when no clinical template is selected. Valid outcomes are: refer, advice-only"
And no data is persisted
```

**Scenario 23: Null template submitted with a supply-and-refer outcome**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with clinicalAssessment.template null and outcome "supply-and-refer"
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Outcome 'supply-and-refer' is not permitted when no clinical template is selected. Valid outcomes are: refer, advice-only"
And no data is persisted
```

---

### Validation — IP Supply

**Scenario 24: Supply outcome submitted without ipSupply object**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with outcome "supply" or "supply-and-refer" and the ipSupply object omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "IP supply details are required when outcome is supply or supply-and-refer"
And no data is persisted
```

**Scenario 25: ipSupply submitted with empty medicinesSupplied array**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with outcome "supply" or "supply-and-refer" and medicinesSupplied as an empty array
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "At least one medicine must be supplied when outcome is supply or supply-and-refer"
And no data is persisted
```

**Scenario 26: medicinesSupplied entry submitted without vpId**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with a supply outcome and a medicinesSupplied entry where vpId is null or omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "VMP identifier (vpId) is required for each medicine supplied"
And no data is persisted
```

**Scenario 27: medicinesSupplied entry submitted without vmppId**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with a supply outcome and a medicinesSupplied entry where vmppId is null or omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Pack identifier (vmppId) is required for each medicine supplied"
And no data is persisted
```

**Scenario 28: medicinesSupplied entry submitted with ampId but without amppId**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with a supply outcome and a medicinesSupplied entry where ampId is populated but amppId is null or omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "AMPP identifier (amppId) is required when ampId is provided"
And no data is persisted
```

**Scenario 29: medicinesSupplied entry submitted with amppId but without ampId**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with a supply outcome and a medicinesSupplied entry where amppId is populated but ampId is null or omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "AMP identifier (ampId) is required when amppId is provided"
And no data is persisted
```

**Scenario 30: medicinesSupplied entry submitted without medicineName**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with a supply outcome and a medicinesSupplied entry where medicineName is null or omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "Medicine name is required for each medicine supplied"
And no data is persisted
```

**Scenario 31: medicinesSupplied entry submitted without quantitySupplied**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with a supply outcome and a medicinesSupplied entry where quantitySupplied is null or omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "'quantitySupplied' must not be empty."
And no data is persisted
```

**Scenario 32: medicinesSupplied entry submitted without dosage**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with a supply outcome and a medicinesSupplied entry where dosage is null or omitted
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "'dosage' must not be empty."
And no data is persisted
```

**Scenario 33: quantitySupplied submitted as less than 1**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with a medicinesSupplied entry where quantitySupplied is 0 or a negative number
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "'quantitySupplied' must be greater than '0'."
And no data is persisted
```

**Scenario 34: dosage exceeds 100 character limit**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with a medicinesSupplied entry where dosage contains 101 characters
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "The length of 'dosage' must be 100 characters or fewer. You entered 101 characters."
And no data is persisted
```

**Scenario 35: casSupply block submitted on IP route**
```gherkin
Given a valid patient exists with patientId {patientId}
And the dispensing organisation is registered and active
When an IP consultation is submitted with a supply outcome and a casSupply block provided instead of ipSupply
Then the API returns status 400
And the response type is "ValidationException"
And the detail message is "'casSupply' must not be provided on the IP service route"
And no data is persisted
```

