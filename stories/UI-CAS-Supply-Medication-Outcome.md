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
- Formulary medications (CAS pathway specific) retrieved from the selected CAS condition (via API #605455)
- Quantity supplied (dose units) as free text numeric field for each medication
- Dosage free text field (disabled until quantity > 0 is entered)
- Backup supply question (only displayed if user enters quantity into antibiotic medication)
- Optional advice to patient textarea with 2000-character limit

**CAS Pathway Validation Rules:**
1. **Formulary Only (Non-STTT/UTI):** At least one medication MUST be selected
2. **Formulary + Antibiotics:** At least one from either category MUST be selected OR One antibiotic ONLY + Multiple formulary items

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

- **Text Input (Numeric)** — Quantity supplied field (dose units, free text numeric)
  [Figma: Text Input](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

- **Text Input** — Dosage free text field (enabled/disabled based on quantity input)
  [Figma: Text Input](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

- **Conditional Fieldset** — Backup supply question (displayed only for antibiotic medications when quantity > 0)
  [Figma: Fieldset](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

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
Then the Quantity supplied field for each medication shall be empty
And the Dosage field for each medication shall be disabled (grayed out, non-editable)
And the Advice given to patient textarea shall be empty
And the character counter displays: "You have 2000 characters remaining"
And any backup supply questions are hidden (not displayed)
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

**Scenario 6: Enter quantity supplied (dose units) as free text numeric**

```gherkin
Given a formulary medication is displayed
When the user enters a numeric value into the Quantity supplied field
Then the value is accepted as free text numeric input
And the value is recorded against that medication
And the Dosage field for that medication is automatically enabled (becomes editable, no longer grayed out)
And if the user clears the quantity field (removes all numeric input), the Dosage field is disabled and cleared
And the selection persists
```

**Scenario 7: Dosage field disabled until quantity entered**

```gherkin
Given a formulary medication is displayed
And the Quantity supplied field is empty or contains "0"
When the page is in default state
Then the Dosage field is disabled (grayed out, non-editable, no cursor)
And the user cannot enter text into the Dosage field
And screen reader announces: "Dosage, text input, disabled"

When the user enters a numeric quantity > 0 in the Quantity supplied field
Then the Dosage field is immediately enabled
And the Dosage field becomes editable with normal styling
And focus may move to the Dosage field (or user can tab to it)

When the user removes/clears the Quantity supplied value
Then the Dosage field is disabled again
And any previously entered Dosage value is cleared
And the field returns to disabled state (grayed out)
```

**Scenario 8: Record quantity and dosage for multiple medications**

```gherkin
Given multiple formulary medications are displayed
When the user records quantity and dosage details for more than one medication
Then the information entered for each medication is retained separately
And each medication's dosage field state (enabled/disabled) is independent
And when the user navigates within the page or returns to the page, all entered data and field states persist
```

**Scenario 9: Backup supply question displayed only for antibiotic medications**

```gherkin
Given the page contains both formulary and antibiotic medications
When the page is first loaded
Then the backup supply question is hidden for all medications

When the user enters a quantity > 0 in an antibiotic medication's Quantity supplied field
Then the backup supply question is displayed below that medication's row
And the question text is: "Is backup supply provided?"
And two radio options are displayed: "Yes" and "No"
And no option is pre-selected

When the user clears the Quantity supplied field for that antibiotic (back to empty/0)
Then the backup supply question is hidden again
And any selected radio option is cleared

When the user enters a quantity > 0 in a formulary (non-antibiotic) medication field
Then the backup supply question does NOT appear for that medication
```

**Scenario 10: Backup supply answer recorded**

```gherkin
Given the backup supply question is displayed for an antibiotic medication
When the user selects "Yes" or "No"
Then the selection is recorded against that medication
And the selection persists when navigating within the page
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

### CAS Pathway Validation Rules

**Scenario 18a: Formulary Only pathway validation**

```gherkin
Given the user is on the Supply page
And the CAS pathway contains only Formulary medications (Non-STTT/UTI)
When the user selects Continue
And all medications have quantity supplied = 0 (empty)
Then an error is displayed: "At least one medication must be selected"
And the user remains on the page
And no data is persisted

When the user enters quantity > 0 for at least one medication
And completes required dosage fields
Then validation passes and user can proceed
```

**Scenario 18b: Formulary + Antibiotics pathway validation**

```gherkin
Given the user is on the Supply page
And the CAS pathway contains both Formulary AND Antibiotic medications
When the user selects Continue

Scenario 18b-i: At least one from either category selected
  And at least one medication (formulary or antibiotic) has quantity > 0
  Then validation passes

Scenario 18b-ii: One antibiotic + multiple formulary items
  And one antibiotic has quantity > 0
  And one or more formulary medications have quantity > 0
  Then validation passes

Scenario 18b-iii: Neither category has selection
  And all medications (formulary and antibiotic) have quantity = 0
  Then an error is displayed: "At least one medication must be selected from formulary or antibiotics"
  And the user remains on the page
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

**Scenario 24: Quantity field accepts numeric free text input**

```gherkin
Given a medication's Quantity supplied field is displayed
When the user enters a numeric value (e.g., "5", "10", "2.5")
Then the value is accepted as free text
And no strict numeric validation is applied at input level
And the value is recorded against that medication
```

**Scenario 25: Dosage field cleared and disabled when quantity removed**

```gherkin
Given a medication has quantity > 0 entered
And the Dosage field is enabled and contains a dosage value
When the user clears/removes the Quantity supplied field (back to empty)
Then the Dosage field is automatically:
  - Cleared (any entered value removed)
  - Disabled (grayed out, non-editable)
And when the user returns to the page, the cleared and disabled state persists

When the user re-enters a quantity > 0
Then the Dosage field is re-enabled
And is displayed as blank (previously cleared value not restored)
```

**Scenario 26: Dosage no longer required when quantity is empty**

```gherkin
Given a medication has a Quantity supplied value of empty or "0"
When the user selects Continue
Then the medication does not require a dosage value
And dosage validation is not applied to that medication
And the user can proceed without entering dosage for that medication
And the Dosage field remains disabled
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
- [ ] Quantity field enables/disables Dosage field correctly verified
- [ ] Dosage auto-clear when quantity removed verified
- [ ] Backup supply question display/hide logic verified (antibiotic + qty > 0)
- [ ] CAS pathway validation rules verified (Formulary Only vs. Formulary + Antibiotics)
- [ ] Documentation updated (consultation pathway, data model)
- [ ] Ready for UAT

---

## Technical Notes

- Medication list retrieved from API #605455 based on selected CAS condition (formulary + optional antibiotic medications)
- **Quantity supplied:** Free text numeric input field (dose units) — no dropdown
- **Dosage field:** Enabled only when quantity > 0; auto-cleared and disabled when quantity is empty/removed
- **Backup supply question:** Conditional display — only shown for antibiotic medications when quantity > 0
- **Dosage:** Free text field, no validation on format (e.g., "15mg twice daily", "1-2 tablets")
- **Advice textarea:** 2000-character hard limit enforced at input level
- **Validation rules:**
  - **Formulary Only:** At least 1 medication with qty > 0 required
  - **Formulary + Antibiotics:** At least 1 from either category OR (1 antibiotic + multiple formulary)
  - **Dosage:** Required for all medications with qty > 0
- **Character counter:** Displays dynamically: "You have X characters remaining"
- **Data structure:** {medicationId, quantity, dosage, backupSupply?} for each selected medicine
- **Field dependencies:**
  - Quantity (text input) → enables Dosage field
  - Quantity (antibiotic) + qty > 0 → enables Backup Supply question
  - Dosage and Backup Supply auto-clear when quantity removed
