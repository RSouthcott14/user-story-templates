# UI: CAS Supply Medication Outcome

## Story Type: UI | Dependent on: #605455 (Retrieve CAS Formulary Medications)

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
- Quantity supplied dropdown for each medication
- Dosage free text field for each medication (required only when quantity > 0)
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

## Acceptance Criteria

**Total Scenarios: 27**

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
  - Quantity supplied (dropdown field)
  - Dosage (text input field)
And the table is populated with data from API #605455
```

### Default State

**Scenario 4: Default medication field values**

```gherkin
Given formulary medications are displayed
When the page is first loaded
Then the Quantity supplied dropdown for each medication shall display: "0"
And the Dosage field for each medication shall be blank
And the Advice given to patient textarea shall be empty
And the character counter displays: "You have 2000 characters remaining"
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

**Scenario 6: Display quantity supplied dropdown options**

```gherkin
Given a formulary medication is displayed
When the Quantity supplied dropdown is activated
Then the dropdown displays available quantity options
And the available values are populated from the formulary configuration for that medication returned by API #605455
And the current value is highlighted/selected
```

**Scenario 7: Quantity supplied options vary by medication**

```gherkin
Given multiple formulary medications are displayed
When quantity supplied dropdown options are loaded
Then each medication may display a different set of quantity supplied options
And the values displayed match those configured for the medication in the formulary (via API #605455)
```

**Scenario 8: Select quantity supplied**

```gherkin
Given a formulary medication is displayed
When the user selects a quantity supplied value from the dropdown
Then the selected quantity supplied value is recorded against that medication
And the value is displayed in the dropdown
And the selection persists
```

**Scenario 9: Record dosage for medication**

```gherkin
Given a formulary medication is displayed
When the user enters a dosage value in the Dosage field
Then the dosage is recorded against that medication
And the value is displayed in the field
And the dosage is accepted as free text (no format validation)
```

**Scenario 10: Record supply details for multiple medications**

```gherkin
Given multiple formulary medications are displayed
When the user records quantity supplied and dosage details for more than one medication
Then the information entered for each medication is retained separately
And when the user navigates within the page or returns to the page, all entered data persists
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

**Scenario 14: Continue with valid supply information**

```gherkin
Given the user has completed the required information on the page
And at least one medication has quantity supplied > 0 with dosage entered
When the user selects Continue
Then the medication supply details are saved against the consultation
And the user is progressed to the next step in the consultation workflow
```

**Scenario 15: Navigate to previous page**

```gherkin
Given the user is on the Supply page
When the user selects Previous
Then the user is returned to the previous consultation page
And any previously saved information is retained
And when the user returns to the Supply page, all entered data is retained
```

**Scenario 16: Cancel and exit consultation**

```gherkin
Given the user is on the Supply page
When the user selects Cancel and exit
Then the user is navigated to the patient overview page
And no data is saved
And the consultation is abandoned
```

### Consultation State Persistence

**Scenario 17: Consultation state persistence within same patient encounter**

```gherkin
Given the user has an active consultation in progress within the current patient encounter
And supply information has been recorded
When the user navigates to another tab within the patient record
And subsequently returns to the consultation within the same patient encounter
Then the consultation remains active
And the user is returned to the Supply page if it was the last consultation page viewed
And any previously entered information is retained
```

### Validation

**Scenario 18: At least one medication must be supplied**

```gherkin
Given the user is on the Supply page
And all displayed medications have a quantity supplied value of "0"
When the user selects Continue
Then the user remains on the current page
And an error summary displays at the top
And the error message displays: "Supply at least one medication"
And the error message has role="alert" with aria-live="polite"
And focus moves to the error summary
And no data is persisted
```

**Scenario 19: Dosage mandatory when medication supplied**

```gherkin
Given a formulary medication has a quantity supplied greater than "0"
And the Dosage field for that medication is blank
When the user selects Continue
Then the user remains on the current page
And an error summary displays
And the error message displays: "Enter dosage for [Medication Name]"
And the corresponding medication row is highlighted with error styling
And focus returns to the Dosage field
And no data is persisted
```

**Scenario 20: Multiple supplied medications require dosage**

```gherkin
Given more than one medication has a quantity supplied greater than "0"
And one or more corresponding Dosage fields are blank
When the user selects Continue
Then validation errors are displayed for each supplied medication without dosage
And error messages display for each: "Enter dosage for [Medication Name]"
And the user remains on the current page
And the user is not permitted to continue until all supplied medications have dosage entered
And no data is persisted
```

**Scenario 21: Advice field character limit enforcement**

```gherkin
Given the user is entering information into the Advice given to patient field
When the entered text reaches 2000 characters
Then the user is not permitted to enter additional characters beyond the maximum limit
And the character counter displays: "You have 0 characters remaining"
And the field stops accepting input
And the 2000-character limit is enforced at input level (hard limit)
```

**Scenario 22: Dosage captured as free text without format validation**

```gherkin
Given the user enters a value into a Dosage field
When the value is entered (e.g., "15mg twice daily", "1-2 tablets", "as directed")
Then the system accepts the value as free text
And no dosage format validation is applied
And the value is recorded and persisted as entered
```

**Scenario 23: API retrieval failure handling**

```gherkin
Given the page loads
When formulary medication information cannot be retrieved from API #605455
Then an appropriate error message displays to the user
And the error message provides guidance (e.g., "Unable to retrieve medications. Please try again or contact support.")
And the medication table is not displayed or is empty
And the error is logged in accordance with system standards
And the user is unable to proceed until the issue is resolved
```

**Scenario 24: Invalid quantity selection prevented**

```gherkin
Given quantity supplied options are configured for a medication
When the user attempts to submit a value that is not available within the configured dropdown list
Then the value is rejected
And the system prevents submission with invalid quantity values
And an appropriate error message may be displayed if the user attempts to circumvent validation
```

**Scenario 25: Clear dosage when quantity supplied changed to zero**

```gherkin
Given a formulary medication has a quantity supplied value greater than "0"
And a dosage has been entered for that medication
When the user changes the quantity supplied value to "0"
Then the dosage value associated with that medication is automatically cleared
And the dosage field is displayed as blank
And when the user returns to the page, the cleared state persists
```

**Scenario 26: Dosage no longer required when quantity supplied is zero**

```gherkin
Given a formulary medication has a quantity supplied value of "0"
When the user selects Continue
Then the medication does not require a dosage value
And dosage validation is not applied to that medication
And the user can proceed without entering dosage for that medication
```

### Accessibility

**Scenario 27: WCAG 2.1 AA compliance for keyboard and screen reader users**

```gherkin
Given the page is rendered
When a user navigates using Tab key
Then focus order follows: Previous button → Medication table cells (qty/dosage fields in row order) → Advice textarea → Character counter → Continue button → Cancel and exit link
And each Select (dropdown) announces: "[Field Name], dropdown, [current value]"
And each Text Input announces: "[Field Name], text input"
And the Textarea announces: "Advice given to patient, optional, textarea"
And the character counter uses: role="status" + aria-live="polite"
And error messages use: role="alert" + aria-live="polite"
And medication table has proper header cells: <th>Medication</th>, <th>Pack size</th>, etc.
And all form inputs have associated labels (not placeholder-only)
And required fields are marked: aria-required="true"
And focus indicators are visible with minimum 3:1 contrast and 2px width
And colour contrast is at least 4.5:1 (text vs. background)
And error text maintains 4.5:1 contrast ratio
```

---

## Definition of Done

- [ ] All 27 acceptance criteria met and validated by QA
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
- Quantity supplied: dropdown values populated by API (varies by medication)
- Dosage: free text field, no validation on format
- Advice textarea: 2000-character hard limit enforced at input level
- When quantity changed from >0 to 0, dosage field auto-clears
- Validation applies: at least 1 qty > 0, and all qty > 0 require dosage
- Character counter displays dynamically: "You have X characters remaining"
- Data structure: {medicationId, quantitySupplied, dosage} for each selected medicine
