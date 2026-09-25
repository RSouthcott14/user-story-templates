# Copilot Instructions

This repository contains user story templates and standards for **Choose Pharmacy NextGen** — an NHS Wales Digital (DHCW) platform for community pharmacy services across Wales.

## Branch

Always work on the **`main`** branch. Do not create feature branches or suggest branching strategies for this repository.

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

---

## UI Stories

Use [templates/UI-Template.md](../templates/UI-Template.md) for all UI stories.

### Components

- Every UI component must appear in [COMPONENTS.md](../COMPONENTS.md) — do not introduce an unlisted component
- Link each component to the DHCW Design System V2 reference listed beside it in `COMPONENTS.md`
- Design System Figma: `https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2`

### Acceptance Criteria

UI stories follow exactly **6 scenarios** in this order:

1. **Page Structure & Content** — elements displayed when the page loads
2. **Default State** — initial values and selections before the user acts
3. **User Interaction** — what changes when the user acts on the page
4. **Navigation** — flow to the next page and what data is carried forward
5. **Validation** — error handling for invalid or missing input
6. **Accessibility** — WCAG 2.1 AA: keyboard tab order, screen reader announcements, focus management

Rules:
- State the exact tab order in the accessibility scenario: e.g. `Then focus order is: Back → [field] → Continue`
- State the exact screen reader announcement for interactive elements
- Validation scenarios must include the exact error message string
- Always include an **Out of Scope** section listing what is explicitly excluded

---

## BUG Stories

Use [templates/BUG-Template.md](../templates/BUG-Template.md) for all bug reports.

### Required Sections

- **Environment** — specify the exact environment: DEV / TEST / UAT / PROD
- **Steps to Reproduce** — numbered steps starting from the entry point (login, page navigation)
- **Actual Result** — what currently happens; reference evidence (screenshot or video filename)
- **Expected Result** — what should happen instead
- **Impact Analysis** — who is affected and severity: Critical / High / Medium / Low

### Acceptance Criteria

Bug stories use **2–3 scenarios**:

1. **Defect Reproduced** — following the exact steps recreates the problem; mark the broken behaviour with `(DEFECT)` on the `But` line
2. **Defect Fixed** — after the fix, the expected behaviour occurs
3. **No Regression** — related functionality that could have been affected still works correctly

---

## ENABLER Stories

Use [templates/ENABLER-Template.md](../templates/ENABLER-Template.md) for all technical enabler stories.

### Required Sections

- **Scope (In/Out)** — explicitly state what is and is not included
- **Key Considerations** — risks, dependencies, and assumptions that downstream stories rely on

### Acceptance Criteria

Enabler stories follow exactly **4 scenarios** in this order:

1. **Technology Evaluation** — options evaluated, selection made, rationale documented
2. **Implementation** — capability delivered and integrated with existing services
3. **Quality & Testing** — tests pass, performance benchmarks met
4. **Documentation & Training** — team can use the capability without needing to ask

---

## SPIKE Stories

Use [templates/SPIKE-Template.md](../templates/SPIKE-Template.md) for all research and investigation stories.

### Required Sections

- **Key Questions** — the specific questions the spike must answer (these drive the acceptance criteria)
- **Deliverables** — the artefacts produced: report, ADR, proof-of-concept, decision log
- **Estimated Effort** — time-box in hours or story points before the spike begins

### Acceptance Criteria

Spike stories follow exactly **4 scenarios** in this order:

1. **Research & Documentation** — all key questions answered with evidence and references cited
2. **Analysis & Findings** — trade-offs identified, assumptions documented
3. **Risk Assessment** — technical risks identified, mitigation strategies proposed
4. **Recommendation & Decision** — clear recommended option with rationale and next steps defined

---

## ISSUE Stories

Use the ISSUE story format for design decisions, process questions, and discussion items requiring resolution.

### Required Sections

- **Issue/Problem Statement** — the specific question or problem to resolve
- **Current Position** — what is true today (if anything exists already)
- **Options** — at least two distinct options, each with trade-offs
- **Recommendation** — the preferred option and why
- **Outcome/Decision** — leave blank until resolved; fill in after the decision is made

### Acceptance Criteria

Issue stories use exactly **2 scenarios**:

1. **Decision Made** — issue discussed, decision documented with rationale, stakeholders informed
2. **Action Defined** — next steps are clear, owners assigned, timeline defined

---

## Azure DevOps Linking

Every story must link to a **Feature** or **Epic** — orphan stories are not permitted.

| Story Type | Link Type | Notes |
|------------|-----------|-------|
| UI | Parent | Direct feature deliverable |
| BUG | Parent | Part of the feature fix |
| API | Parent | Backend for a feature |
| ENABLER | Parent or Related | Use Related if it spans multiple features |
| SPIKE | Related | Research supporting a feature |
| ISSUE | Related | Strategic decision; typically spans multiple features |

Always include the **Feature ID** (e.g., `577034`) when creating a story so the link can be set during creation. See [DEVOPS-WORK-ITEM-STRUCTURE.md](../DEVOPS-WORK-ITEM-STRUCTURE.md) for full linking guidance.
