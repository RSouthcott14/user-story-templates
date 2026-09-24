# Code Systems & Domain Enumerations

**API:** Choose Pharmacy NextGen

Code systems are the FHIR representation of **domain enumerations** — finite, predefined sets of values that carry specific clinical or operational meaning. Every code system in a user story corresponds 1:1 to a domain enumeration in the codebase.

---

## When to Create a Code System

Create a code system when a field meets **all** of the following:

1. **Finite and predefined** — the set of valid values is known upfront and is not user-defined or free text
2. **Each value has distinct domain meaning** — values are not interchangeable; removing or adding one would require a clinical or operational decision
3. **Drives system behaviour** — the value determines what other fields are required, forbidden, or how the system responds (e.g. `consultationOutcome` controls which of `supply`, `referral`, or `adviceToPatient` are required)
4. **Needs external interpretability** — the value must be understood by other systems (FHIR interoperability, GP record integration, reporting)

If a field fails any of these, it is either a **validation rule** (format/range constraint) or a **free-text field**, not a code system.

---

## When NOT to Create a Code System

| Field type | Reason | Example |
|---|---|---|
| Boolean | Only two states; no FHIR vocabulary needed | `isEligible` |
| Free text | Unbounded; meaning is supplied by the user | `adviceToPatient`, `referralInformation` |
| Numeric | Constrained by range, not domain meaning | `quantity`, `limit` |
| Date/time | Format-constrained, not vocabulary-constrained | `consultationDateTime` |
| Identifier | Unique reference, not a constrained vocabulary | `gphcNumber`, `patientId`, `nhsNumber` |
| User-managed reference data | Values are configurable, not fixed | pharmacy names, GP practice lists |

---

## Naming Rules

Code system names map directly to domain enumeration names. The name is identical — only the casing differs between contexts.

| Context | Format | Example |
|---|---|---|
| Code system name (story) | PascalCase | `ConsultationOutcome` |
| URI slug | kebab-case | `consultation-outcome` |
| Domain enum | PascalCase | `Domain.Types.Consultation.ConsultationOutcome` |
| Code values (API) | kebab-case | `supply`, `supply-and-refer`, `refer` |

**Do not invent a new name.** Identify the existing domain enumeration first and name the code system to match it exactly. All existing domain enumerations can be found here:

[ChoosePharmacy.Domain.Types](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_git/ChoosePharmacy.Api?path=/src/ChoosePharmacy.Domain.Types)

---

## URI Pattern

All code system URIs follow this pattern:

```
https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/{kebab-case-enum-name}
```

**Examples:**
```
https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/consultation-outcome
https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/referral-destination
https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/ineligibility-reason
```

---

## Namespace

Domain enumerations are grouped by their domain area:

| Domain Area | Namespace |
|---|---|
| Consultation | `NhsWales.ChoosePharmacy.Domain.Types.Consultation` |
| Allergies | `NhsWales.ChoosePharmacy.Domain.Types.Allergies` |

---

## Template

Each code system in a user story is documented as follows:

```markdown
### CodeSystemName

**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/code-system-name`

| Code | Display |
|------|---------|
| `code-value` | Human-readable label |
| `code-value` | Human-readable label |
```

---

## Existing Code Systems (EMS)

| Code System | URI | Domain Enum |
|---|---|---|
| `ConsultationUndertaken` | `.../consultation-undertaken` | `Domain.Types.Consultation.ConsultationUndertaken` |
| `ReferredBy` | `.../referred-by` | `Domain.Types.Consultation.ReferredBy` |
| `IneligibilityReason` | `.../ineligibility-reason` | `Domain.Types.Consultation.IneligibilityReason` |
| `EmergencySupplyReason` | `.../emergency-supply-reason` | `Domain.Types.Consultation.EmergencySupplyReason` |
| `ConsultationOutcome` | `.../consultation-outcome` | `Domain.Types.Consultation.ConsultationOutcome` |
| `ReferralDestination` | `.../referral-destination` | `Domain.Types.Consultation.ReferralDestination` |
| `ReferralTimescale` | `.../referral-timescale` | `Domain.Types.Consultation.ReferralTimescale` |
