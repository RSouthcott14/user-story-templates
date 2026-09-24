# Copilot Instructions

This repository contains user story templates and standards for **Choose Pharmacy NextGen** — an NHS Wales Digital (DHCW) platform for community pharmacy services across Wales.

---

## API Stories

Use [templates/API-Template.md](../templates/API-Template.md) for all API stories.
Refer to [examples/API-Example.md](../examples/API-Example.md) for a worked example.

### Endpoint Path

Validate every endpoint against [standards/API-PATH-CONVENTIONS.md](../standards/API-PATH-CONVENTIONS.md) before writing the story.

- Base path is `/api/v1/`
- Resource groups are **lowercase, plural, kebab-case**
- Name the **domain concept** — never a technical layer (`valuesets/`, `commands/`, `fhir/` prefixes are not allowed)
- Patient-scoped resources nest under `/api/v1/patients/{patientId}/`
- Consultation submissions: `POST /api/v1/patients/{patientId}/consultations/{serviceType}`
- Reference/lookup data: `GET /api/v1/{domain}/{data}`

If the path provided violates these rules, correct it and state why before proceeding.

### Code Systems

Refer to [standards/API-CODE-SYSTEMS.md](../standards/API-CODE-SYSTEMS.md) when deciding whether a field needs a code system.

- A code system is required when a field has a **finite, predefined set of values** where each value has distinct clinical or operational meaning and drives system behaviour
- The code system name must match the existing domain enumeration name exactly — check [ChoosePharmacy.Domain.Types](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_git/ChoosePharmacy.Api?path=/src/ChoosePharmacy.Domain.Types) before writing a code system
- URI pattern: `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/{kebab-case-enum-name}`
- Do not create a code system for booleans, free text, numerics, dates, or identifiers

### Acceptance Criteria

- Group by **outcome path** — not by technical category
- Use **Gherkin format** (Given/When/Then/And) throughout
- Include **exact error message strings** in all validation scenarios
- End every validation scenario with: `And no data is persisted`
- Include Patient Not Found and Organisation Not Found scenarios where applicable
