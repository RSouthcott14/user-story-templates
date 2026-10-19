# UI: CAS Supply Medication Outcome

## Story Type: UI | Dependent on: #605455 (Retrieve CAS Formulary Medications)
## Tags: `AI Generated` | `Review (BR)` | `CAS` | `CCM` | `Medication` | `UI`

---

## Description

### Story Card
**As a** pharmacy user  
**I want** to record supply medication details for a CAS consultation  
**So that** I can complete the consultation with the appropriate medicines and document any patient advice

### Context
This story is part of the Clinical Conditions Management (CCM) journey when the outcome is to **Supply** medication under the Common Ailments Service (CAS). 

After the user selects "Supply" as the outcome, they are presented with a Supply page containing:
- Formulary medications retrieved from the selected CAS condition (via API #605455)
- Quantity supplied (dose units) as free text numeric field for each medication (positive whole numbers only, no 0)
- Dosage free text field (disabled until quantity > 0 is entered)
- Backup supply question (only displayed if user enters quantity into antibiotic medication)
- Optional advice to patient textarea with 2000-character limit

The user records the medication details and can optionally add advice before continuing to the next step in the consultation workflow.

**Related Journey Step**: CCM Consultation → Outcome Decision → Supply Medication → Complete Consultation

### Captions
**Journey step:** Clinical Conditions Management  
**Page caption:** Outcome: Supply

### Out of Scope
- Formulary maintenance or medication catalogue updates
- Medication interaction checking or clinical validation
- Prescription generation or transmission
- Editing selections after submission (would require separate edit journey)
- Quantity supplied configuration (populated by API #605455)
- Historical supply records or previous supply recommendations
- Bulk or repeat dispensing workflows

### Related/Dependent Stories
- **#605455**: Retrieve CAS Formulary Medications for a Selected Condition (API) — *blocks this story*
- **#603150**: CCM Outcome Selection (Upstream)
- Related Feature: Clinical Conditions Management (CAS Supply Pathway)

---

## Design Reference

**Design System:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Component Guidance:** [COMPONENTS.md](../COMPONENTS.md)

**Error Messaging Standard:** [standards/UI-ERROR-MESSAGING.md](../standards/UI-ERROR-MESSAGING.md)

**Textarea Standard:** [standards/UI-TEXTAREA-SPECIFICATIONS.md](../standards/UI-TEXTAREA-SPECIFICATIONS.md)

### Components Used

- **Table** — Display medications with quantity and dosage fields
  [Figma: Table](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-79)

- **Select** — Quantity supplied dropdown
  [Figma: Select](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6242-6391)

- **Text Input** — Dosage free text field
  [Figma: Text Input](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

- **Textarea** — Advice to patient (optional, 2000-character limit)
  [Figma: Textarea](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6218-4824)

- **Buttons** — Previous (secondary), Continue (primary)
  [Figma: Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475)

- **Action Link** — Cancel and exit
  [Figma: Action Link](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9493)

- **Error Summary** — Validation error display
  [Figma: Error Summary](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4585)

- **Inset Text** — Help text and instructions
  [Figma: Inset Text](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4589)

---

## CAS Pathway Validation Rules
1. **Formulary Only (Non-STTT/UTI):** At least one medication MUST be selected
2. **Formulary + Antibiotics:** At least one from either category MUST be selected OR One antibiotic ONLY + Multiple formulary items

---

## Acceptance Criteria

**Total Scenarios: 30**

### Page Structure & Content

**Scenario 1: Display Supply page layout**

```gherkin
Given the user has selected the outcome "Supply"
When the page loads
Then the page displays:
  - Journey caption: "Clinical Conditions Management" (small text, above heading)
  - Page heading: "Outcome: Supply"
  - Section text: "Supply medication under Common Ailments Service"
  - Link to Common Ailments Service formulary
  - Medication table section
  - Section heading: "Advice given to patient (optional)"
  - Advice textarea field with character counter
  - Previous button (secondary)
  - Continue button (primary)
  - Cancel and exit link
And the patient banner displays available patient information above the main content
```

**Scenario 2: Retrieve formulary medications from API**

```gherkin
Given the user has reached the Supply page
And a CAS condition has been selected
When the page loads
Then the system shall retrieve formulary medications using API #605455 - Retrieve CAS Formulary Medications for a Selected Condition
And only medications returned by the API shall be displayed
And if the API call fails, an error message is displayed (see Scenario 23)
```

**Scenario 3: Display formulary medications in table**

```gherkin
Given formulary medications have been successfully returned by the API
When the page loads
Then a row shall be displayed for each medication returned
And each row displays:
  - Medication name (text)
  - Pack size (text)
  - Quantity supplied (free text numeric input field) — dose units
  - Dosage (text input field, initially disabled)
And the table is populated with data from API #605455
```

### Default State

**Scenario 4: Default medication field values**

```gherkin
Given formulary medications are displayed
When the page is first loaded
Then:
  - Quantity supplied field is empty
  - Dosage field is disabled (grayed out)
  - Advice textarea is empty
  - Character counter displays: "You have 2000 characters remaining"
  - Backup supply questions are hidden
```

**Scenario 5: Previously saved supply details retained**

```gherkin
Given supply details have previously been recorded for the consultation
When the user returns to the Supply page
Then the previously recorded quantity supplied values shall be displayed for each medication
And the previously recorded dosage values shall be displayed for each medication
And any previously recorded patient advice shall be displayed in the textarea
And the character counter displays: "You have [remaining] characters remaining"
```

### User Interaction

**Scenario 6: Quantity supplied - positive whole numbers only**

```gherkin
Given a formulary medication is displayed
When the user enters a positive whole number (1, 5, 10, 100)
Then the value is recorded
And Dosage field is enabled

When the user attempts to enter 0, negative, decimals, or non-numeric
Then the field rejects input at field level

When the user clears the quantity field
Then Dosage field is disabled and cleared
```

**Scenario 7: Dosage field disabled until quantity entered**

```gherkin
Given Quantity supplied is empty
When the page loads
Then Dosage field is disabled (non-editable)

When user enters quantity > 0
Then Dosage field is immediately enabled

When user removes quantity
Then Dosage field is disabled and cleared
```

**Scenario 8: Record quantity and dosage for multiple medications**

```gherkin
Given multiple medications are displayed
When user records details for multiple medications
Then information is retained separately
And each medication's dosage field state is independent
And data persists on navigation
```

**Scenario 9: Backup supply question for antibiotics only**

```gherkin
Given page contains formulary and antibiotic medications
When page loads
Then backup supply question is hidden for all

When user enters quantity > 0 in antibiotic
Then backup supply question displays below that medication
With radio options: "Yes" and "No"

When user clears antibiotic quantity
Then backup supply question is hidden
And selection is cleared

When user enters quantity in formulary medication
Then backup supply question does NOT appear
```

**Scenario 10: Backup supply answer recorded**

```gherkin
Given backup supply question is displayed
When user selects "Yes" or "No"
Then selection is recorded
And persists on navigation
```

**Scenario 11: Display advice to patient field**

```gherkin
Given the user is on the Supply page
When the page loads
Then the "Advice given to patient (optional)" textarea is displayed
And the field includes:
  - Label: "Advice given to patient (optional)"
  - Placeholder text: "Enter any relevant advice for the patient"
  - Character counter: "You have 2000 characters remaining"
And the textarea utilizes the maximum available section width
And equal margins are maintained on left and right sides
```

**Scenario 12: Record advice given to patient**

```gherkin
Given the Advice given to patient textarea is displayed
When the user enters text
Then the entered advice is recorded against the consultation
And the character counter updates dynamically: "You have [remaining] characters remaining"
And the value persists when navigating within the page
```

**Scenario 13: Advice field is optional**

```gherkin
Given the user is on the Supply page
When the Advice given to patient field is left blank
Then the user is permitted to continue
And no validation error is displayed
And subject to all other validation rules being met, the user can proceed
```

### Navigation

**Scenario 11: Continue with valid supply information**

```gherkin
Given the user has completed required information
When the user selects Continue
Then the supply details are saved
And the user progresses to the next step
```

**Scenario 12: Navigate to previous page**

```gherkin
Given the user is on the Supply page
When the user selects Previous
Then the user is returned to the previous page
And all entered data is retained
```

**Scenario 13: Cancel and exit consultation**

```gherkin
Given the user is on the Supply page
When the user selects Cancel and exit
Then the user navigates to patient overview
And no data is saved
```

### Consultation State Persistence

**Scenario 14: Consultation state persistence within same encounter**

```gherkin
Given a user has an active consultation
When the user navigates between pages within the same encounter
Then selected filters and data remain applied
And the user sees previously filtered results when returning
```

**Scenario 15: Accessing same patient in new encounter**

```gherkin
Given a user has applied filters/data within a consultation
When the user accesses the same patient in a new encounter
Then previously applied data shall not be retained
And default filters shall apply for the new encounter
```

### Validation

**Scenario 16: At least one medication must be supplied**

```gherkin
Given all medications have quantity = 0
When the user selects Continue
Then an error displays: "Supply at least one medication"
And the user remains on the page
```

**Scenario 17: CAS Pathway Validation - Formulary Only**

```gherkin
Given the CAS pathway is Formulary Only (Non-STTT/UTI)
When validating supplied medications
Then at least one medication MUST be selected
```

**Scenario 18: CAS Pathway Validation - Formulary and Antibiotics (Option 1)**

```gherkin
Given the CAS pathway is Formulary + Antibiotics
When validating supplied medications
Then at least one medication from either category MUST be selected
And the user can proceed with 1+ formulary OR 1+ antibiotics
```

**Scenario 19: CAS Pathway Validation - Formulary and Antibiotics (Option 2)**

```gherkin
Given the CAS pathway is Formulary + Antibiotics
When validating supplied medications
Then One antibiotic ONLY + Multiple formulary items is allowed
```

**Scenario 20: Dosage mandatory when medication supplied**

```gherkin
Given a medication has quantity > 0
When user selects Continue without dosage
Then an error displays: "Enter dosage for [Medication Name]"
And the user remains on the page
```

**Scenario 21: Advice field character limit enforcement**

```gherkin
Given the user is entering information into the Advice field
When the entered text reaches 2000 characters
Then no additional characters can be entered
And the character counter displays: "You have 0 characters remaining"
And the field stops accepting input
```

**Scenario 22: Dosage captured as free text without format validation**

```gherkin
Given the user enters a dosage value
When the value is entered (e.g., "15mg twice daily", "1-2 tablets", "as directed")
Then the system accepts the value as free text
And no dosage format validation is applied
```

**Scenario 23: API retrieval failure handling**

```gherkin
Given the page loads
When formulary medication information cannot be retrieved from API #605455
Then an appropriate error message displays to the user
And the medication table is not displayed
And the user is unable to proceed until the issue is resolved
```

**Scenario 24: Quantity field accepts only positive whole numbers**

```gherkin
Given the Quantity supplied field is displayed
When user enters a positive whole number
Then the value is accepted and recorded

When user attempts 0, negative, decimal, or special characters
Then the field rejects input at field level (no 0, no decimals, no non-numeric)
```

**Scenario 25: Dosage cleared and disabled when quantity removed**

```gherkin
Given a medication has quantity > 0 with dosage entered
When user clears the quantity
Then Dosage is auto-cleared and disabled
And the state persists on return to the page
```

**Scenario 26: Dosage not required when quantity empty**

```gherkin
Given quantity is empty/0
When user selects Continue
Then dosage validation is not applied
And the user can proceed
```

**Scenario 27: Multiple supplied medications require dosage**

```gherkin
Given more than one medication has quantity > 0
When one or more corresponding Dosage fields are blank
Then validation errors display for each medication without dosage
And the user remains on the page
And the user cannot continue until all supplied medications have dosage
```

**Scenario 28: Advice field is optional**

```gherkin
Given the user is on the Supply page
When the Advice field is left blank
Then the user is permitted to continue
And no validation error is displayed
```

**Scenario 29: Record advice given to patient**

```gherkin
Given the Advice textarea is displayed
When the user enters text
Then the advice is recorded against the consultation
And the character counter updates dynamically
And the value persists on navigation
```

### Accessibility

**Scenario 30: WCAG 2.1 AA compliance for keyboard and screen reader users**

```gherkin
Given the page is rendered
When a user navigates using Tab key
Then focus order follows: Previous → Quantity/Dosage fields → Advice → Continue → Cancel
And screen readers announce field states correctly
And focus indicators are visible with 3:1 contrast and 2px width
And color contrast is at least 4.5:1 (text vs. background)
```

---

## Definition of Done

- [ ] All 30 acceptance criteria met and validated by QA
- [ ] API #605455 completed and integrated
- [ ] Code peer reviewed and approved
- [ ] Unit tests written (80%+ coverage)
- [ ] Accessibility testing completed (WCAG 2.1 AA)
- [ ] UI matches Figma design
- [ ] Browser compatibility verified (Chrome, Firefox, Safari, Edge)
- [ ] Mobile responsiveness tested (375px, 768px, 1024px viewports)
- [ ] Medication data persists correctly across navigation
- [ ] Validation errors display and clear as expected
- [ ] Character counter updates dynamically on all keystrokes
- [ ] API error handling verified
- [ ] Dosage auto-clear when quantity changed to 0 verified
- [ ] Documentation updated (consultation pathway, data model)
- [ ] Ready for UAT

---

## Technical Notes

- Medication list retrieved from API #605455 based on selected CAS condition
- Quantity supplied: free text numeric input (positive whole numbers only, no 0, no decimals, no non-numeric)
- Dosage: free text field, no validation on format (disabled until quantity > 0 entered)
- Advice textarea: 2000-character hard limit enforced at input level
- When quantity changed from >0 to 0, dosage field auto-clears and disables
- Backup supply question: only displayed for antibiotic medications when quantity > 0; clears when quantity removed
- CAS pathway validation: two distinct paths (Formulary Only vs. Formulary + Antibiotics)
- Validation applies: at least 1 medication, and all qty > 0 require dosage
- Character counter displays dynamically: "You have X characters remaining"
- Data structure: {medicationId, quantitySupplied, dosage, backupSupplyRequired} for each selected medicine
