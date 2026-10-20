# UI: CCM Supply - IPS Pathway

## Story Type: UI
## Story ID: 603159
## Tags: `AI Generated` | `Review (BR)`

---

## Description

### Story Card
**As a** pharmacy professional  
**I want** to search for, select and record medications to be supplied as part of an IPS consultation  
**So that** the prescribed medication, quantity supplied, dosage instructions and patient advice are accurately recorded in the consultation

### Context
This page forms part of the **Clinical Conditions Management (CCM) IPS (Independent Prescribers Service) pathway** and is displayed when the consultation outcome **"Supply"** has been selected.

Unlike the CAS Supply pathway, medications are not pre-populated from a condition-specific formulary. Instead, the pharmacy professional searches for and selects medications from the **DM+D (Dictionary of Medicines and Devices)** dataset using the medication search component from the EMS journey.

The page supports:
- Searching for medications using DM+D
- Searching by **Generic** or **Branded** medication
- Adding one or more medications to the consultation
- Recording the pack size, quantity supplied and dosage for each medication supplied
- Recording optional advice given to the patient

A minimum of one medication must be supplied before the consultation can progress.

### Captions
**Journey step:** Clinical Conditions Management  
**Page caption:** Outcome: Supply

### Out of Scope
- Save and continue functionality
- Medication interaction checking or clinical validation
- Prescription generation or transmission
- Editing selections after submission
- Bulk or repeat dispensing workflows

### Related Stories
- **603153**: CCM IPS – Working Diagnosis (upstream decision)
- **603157**: CCM – Select Outcome (upstream selection)
- **586964**: EMS – Medication Search Component (component dependency)
- **585673**: EMS – Medication Search Implementation (component implementation)
- **592874**: API – GET DM+D Medicine Search (backend dependency)
- **595522**: API – GET DM+D Medicine Packs (backend dependency)

### Dependent Stories
- Future stories that consume IPS consultation data

---

## Design Reference

**Design System:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Component Guidance:** [COMPONENTS.md](../COMPONENTS.md)

**Error Messaging Standard:** [standards/UI-ERROR-MESSAGING.md](../standards/UI-ERROR-MESSAGING.md)

**Textarea Standard:** [standards/UI-TEXTAREA-SPECIFICATIONS.md](../standards/UI-TEXTAREA-SPECIFICATIONS.md)

### Components Used

- **Medication Search Component** — DM+D search with Generic/Branded toggle
  [EMS Story #586964](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/586964)

- **Table** — Display added medications with Pack size, Quantity supplied, and Dosage columns
  [Figma: Table](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-79)

- **Text Input** — Pack size, Quantity supplied, Dosage fields
  [Figma: Text Input](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

- **Textarea** — Advice given to patient (optional, 2000-character limit)
  [Figma: Textarea](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6218-4824)

- **Buttons** — Previous (secondary), Continue (primary), Remove (destructive)
  [Figma: Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475)

- **Action Link** — Cancel and exit
  [Figma: Action Link](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9493)

- **Error Summary** — Validation error display
  [Figma: Error Summary](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4585)

- **Inset Text** — Help text and instructions
  [Figma: Inset Text](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4589)

---

## Assumptions

- The consultation outcome selected is **Supply**
- Medication search is performed using the NHS **DM+D (Dictionary of Medicines and Devices)** service
- Generic medications are searched by default
- Dosage is captured as free text and is ALWAYS enabled when a medication is added
- Advice given to patient is optional
- Consultation state persistence follows the standard Choose Pharmacy consultation behaviour
- **Pack size is a dropdown list** returned from DM+D and is **mandatory**
- **Quantity supplied is mandatory** - accepts positive whole numbers only (no negatives, decimals, characters, or special characters)
- Added medications table only displays after the first medication is selected
- If the user removes the only medication, the table section is hidden

---

## Acceptance Criteria

**Total Scenarios: 24**

### Page Structure & Content

**Scenario 1: Display IPS Supply page**

```gherkin
Given the user has selected Outcome = Supply within an IPS consultation
When the page loads
Then the page displays:
  - High level caption: "Clinical Conditions Management"
  - Page heading: "Outcome: Supply"
  - The text: "View the Common Ailments Service formulary to learn more about ailments, possible complications, when to refer to GP, treatments, and advice for patients"
  - A hyperlink to "Common Ailments Service formulary" (https://casformulary.wales.nhs.uk/IndexAMG.aspx)
  - Section heading: "Supply medication under Independent Prescribers Service"
  - Heading: "Search for medication"
  - Help text: "Only select a branded medication if there is a clinical need for it to be used (e.g., pharmacokinetic differences between brands)"
  - Medication search area with Generic/Branded toggle
  - Search medicine input field
  - Advice to patient (optional) section with textarea
  - Previous button
  - Continue button (labeled "Save and continue")
  - Cancel and exit link
```

**Scenario 2: Default search type**

```gherkin
Given the user accesses the IPS Supply page
When the page loads
Then Generic shall be selected by default
And Branded shall be available for selection
```

**Scenario 3: Display medication search controls**

```gherkin
Given the user is on the IPS Supply page
When the page loads
Then a medication search field shall be displayed
And a Generic radio button shall be displayed and selected
And a Branded radio button shall be displayed and not selected
```

### Medication Search

**Scenario 4: Search for a generic medication**

```gherkin
Given Generic search is selected
When the user enters search criteria (e.g., "cap")
Then matching generic medications shall be retrieved from DM+D
And matching search suggestions shall be displayed in a dropdown list
```

**Scenario 5: Search for a branded medication**

```gherkin
Given Branded search is selected
When the user enters search criteria
Then matching branded medications shall be retrieved from DM+D
And matching search suggestions shall be displayed in a dropdown list
```

**Scenario 6: Select medication from search suggestions**

```gherkin
Given medication search suggestions are displayed
When the user clicks on a medication in the suggestions
Then the medication shall be added to the Added Medication section
And the search field shall be cleared
And the medication search suggestions shall close
```

**Scenario 7: Display added medication table**

```gherkin
Given a medication has been selected from DM+D
When the medication is added
Then the Added medication section becomes visible
And a table is displayed with columns: Medicine, Pack size, Quantity supplied (dose units), Dosage
And the medication name shall be displayed in the table
And medication details returned from DM+D shall be formatted for display
And a Pack size dropdown field shall be displayed (populated from DM+D data)
And a Quantity supplied (dose units) field shall be displayed and editable
And a Dosage field shall be displayed and editable (always enabled, not conditional)
And a Remove link shall be displayed for the medication
```

**Scenario 8: Table visibility rules**

```gherkin
Given the user is on the IPS Supply page
When no medications have been added
Then the Added medication section is NOT displayed

Given the user has added the first medication to the consultation
When the page refreshes or the user views the section
Then the Added medication section becomes visible
And displays a table with columns: Medication Name | Pack Size | Quantity | Dosage | Action

When the user removes the only medication in the table
Then the Added medication section is hidden
And the table is no longer displayed
```

### Recording Medication Supply

**Scenario 9: Record pack size**

```gherkin
Given a medication has been added
And pack size information is available from DM+D
When the user selects a pack size from the dropdown
Then the pack size shall be recorded against that medication
And the value shall be retained when navigating between fields
```

**Scenario 10: Record quantity supplied**

```gherkin
Given a medication has been added
When the user enters a positive whole number in the quantity supplied field
Then the quantity supplied (dose units) shall be recorded against that medication
And the value shall be persisted
```

**Scenario 11: Quantity field validation**

```gherkin
Given a medication has been added with a quantity field
When the user attempts to enter a negative number, decimal, letter or special character
Then the value is not accepted by the field
And the field maintains its previous value or remains empty
```

**Scenario 12: Record and enter dosage for medication**

```gherkin
Given a medication has been added
When the page loads
Then the Dosage field shall be displayed and enabled for user input
And the Dosage field shall be an expandable and scrollable textarea
When the user enters dosage instructions (e.g., "Apply when needed")
Then the dosage shall be recorded against that medication
And the value shall be retained
And if multiple medications are added, dosage recorded for each medication shall be retained independently
And changes to one medication's dosage shall not affect others
```

**Scenario 13: Remove medication**

```gherkin
Given a medication has been added
When the user clicks the Remove link
Then the selected medication shall be removed from the Added Medication section
And any associated pack size, quantity supplied and dosage information shall also be removed
And the table shall update to show the remaining medications
```

### Advice to Patient

**Scenario 14: Record patient advice**

```gherkin
Given the Advice given to patient textarea is displayed
When the user enters advice
Then the advice shall be recorded against the consultation
And the character count shall be updated as the user types
And the textarea shall be expandable and scrollable
```

**Scenario 15: Advice is optional**

```gherkin
Given the user is completing the IPS Supply page
And at least one medication has been added with quantity and dosage
When the Advice given to patient field is left blank
Then the user shall be permitted to continue subject to all other validation requirements being satisfied
```

**Scenario 16: Advice field maximum length**

```gherkin
Given the user enters advice into the Advice given to patient textarea
When the text reaches 2000 characters
Then the user shall not be permitted to enter additional characters
And a character counter shall display "You have 0 characters remaining"
```

### Navigation

**Scenario 17: Continue consultation with valid data**

```gherkin
Given all mandatory information has been completed:
  - At least one medication added
  - Pack size selected for each medication
  - Quantity supplied entered for each medication
  - Dosage entered for each medication
When the user clicks "Save and continue"
Then the medication details and patient advice shall be saved against the consultation
And the user shall progress to the Sensitive health information page
```

**Scenario 18: Navigate to previous page**

```gherkin
Given the user is on the IPS Supply page
When the user clicks Previous
Then the user shall be returned to the Outcome Selection page
And all medication details entered shall be retained in consultation state
```

**Scenario 19: Cancel and exit consultation**

```gherkin
Given the user is on the IPS Supply page
When the user clicks Cancel and exit
Then the user is navigated to the patient overview page
And no data is saved
```

### Consultation State Persistence

**Scenario 20: Consultation state persistence within the same patient encounter**

```gherkin
Given the user has an active consultation within the current patient encounter
And medication details have been entered
When the user navigates away from the consultation and subsequently returns within the same patient encounter
Then the consultation shall remain active
And the user shall be returned to the IPS Supply page if it was the last consultation page viewed
And any previously entered medication details and patient advice shall be retained
```

### Validation

**Scenario 21: Comprehensive validation**

```gherkin
Given one or more of the following validation conditions apply:
  - No medication has been supplied
  - A supplied medication has a Quantity of 0
  - A medication has been supplied and no dosage has been entered
When the user selects "Save and continue"
Then an error summary box appears at the top of the page
And the error summary has a red border and alert icon
And the heading displays: "There is a problem"
And all validation errors are displayed as a numbered list
And each error is displayed as a clickable link
And selecting an error navigates focus to the associated field or section
And focus moves to the error summary when it is displayed
And an inline error is displayed against each affected field or section
And the user remains on the Supply page
And no data is persisted
And validation is re-evaluated only when the user next selects "Save and continue"
```

### Validation Error Examples

| Validation Condition | Error Message |
| --- | --- |
| No medication supplied | Select at least one medication |
| Medication supplied with quantity 0 | Enter a quantity greater than zero |
| Medication supplied without dosage | Enter dosage for selected medication |
| Pack size not selected | Select a pack size |

**Scenario 22: DM+D search unavailable**

```gherkin
Given the DM+D search service is unavailable
When the user performs a medication search
Then an appropriate error message shall be displayed: "The medication search service is temporarily unavailable. Please try again."
And medication search results shall not be returned
And the error shall be logged in accordance with system standards
And the user shall be able to continue filling other fields on the page
```

---

## Definition of Done

- [ ] All acceptance criteria (22 scenarios) met and validated by QA
- [ ] Code peer reviewed and approved
- [ ] Unit tests written (80%+ coverage)
- [ ] Medication search component (EMS #586964) is available and integrated
- [ ] Integration with DM+D search service is configured
- [ ] Pack size dropdown populated from DM+D data
- [ ] Quantity supplied field validates: whole numbers only (no negatives, decimals, characters, special chars)
- [ ] Dosage field is always enabled (never disabled conditionally)
- [ ] Table visibility: hidden when no medications, visible after first medication added, hidden when all removed
- [ ] UI matches design mockup (EMS Supply design from 618128)
- [ ] "Save and continue" button text matches design
- [ ] Error summary follows standards with "There is a problem" heading
- [ ] Browser compatibility verified (Chrome, Firefox, Safari, Edge)
- [ ] Mobile responsiveness tested
- [ ] Consultation state persistence verified
- [ ] Error messaging follows standards/UI-ERROR-MESSAGING.md
- [ ] Textarea follows standards/UI-TEXTAREA-SPECIFICATIONS.md
- [ ] Documentation updated (if applicable)
- [ ] Ready for UAT

---

## Notes

- The medication search component is reused from the EMS journey (stories #586964 and #585673)
- DM+D integration will be handled by a separate backend API
- **Pack size is a mandatory dropdown** populated from DM+D data (differs from CAS where it may be pre-filled)
- **Quantity supplied field validation** must prevent negative numbers, decimals, letters, and special characters at the field level (not just on submit)
- **Dosage field is always enabled** when a medication is added (differs from CAS where it becomes enabled only after quantity is entered)
- **Table visibility**: The "Added medication" section and table are hidden until the first medication is added, and hidden again if all medications are removed
- Character counter for textarea must display in real-time as per UI-TEXTAREA-SPECIFICATIONS.md (2000 character limit)
- Button label is "Save and continue" (consistent with CAS pathway)
- CAS formulary hyperlink: https://casformulary.wales.nhs.uk/IndexAMG.aspx
- This story differs from the CAS Supply outcome (618128) in key ways:
  - Uses medication search instead of pre-populated formulary
  - Dosage always enabled (vs. conditional in CAS)
  - Pack size is mandatory (vs. optional in CAS)
- Referral section is handled in a separate story (out of scope for this story)
- Based on design mockup from story 618128 (EMS Supply journey)
