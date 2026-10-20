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
- Dosage is captured as free text
- Advice given to patient is optional
- Consultation state persistence follows the standard Choose Pharmacy consultation behaviour
- Pack size is optional and may be disabled/hidden based on DM+D data availability

---

## Acceptance Criteria

**Total Scenarios: 24**

### Page Structure & Content

**Scenario 1: Display IPS Supply page**

```gherkin
Given the user has selected Outcome = Supply within an IPS consultation
When the page loads
Then the page displays:
  - Journey context: "Clinical Conditions Management"
  - Page heading: "Outcome: Supply"
  - Section heading: "Supply medication under Independent Prescribers Service"
  - Help text: "Only select a branded medication if there is a clinical need for it to be used (e.g., pharmacokinetic differences between brands)"
  - Medication search area with Generic/Branded toggle
  - Search medicine input field
  - Added medication section
  - Advice to patient (optional) section with textarea
  - Previous button
  - Continue button
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
Then a medication search field shall be displayed with placeholder text "Search medicine"
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
And the first matching result shall be highlighted (e.g., "Capsaicin 0.025% cream")
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

**Scenario 7: Display added medication**

```gherkin
Given a medication has been selected from DM+D
When the medication is added
Then the medication name shall be displayed in the Added Medication table
And medication details (quantity, instructions) returned from DM+D shall be formatted for display
And a Pack size field shall be displayed (if available from DM+D)
And a Quantity supplied (dose units) field shall be displayed and editable
And a Dosage field shall be displayed and editable
And a Remove link shall be displayed for the medication
```

**Scenario 8: Prevent duplicate medication selection**

```gherkin
Given a medication has already been added to the consultation
When the user attempts to add the same medication again
Then the medication shall not be added a second time
And an appropriate validation message shall be displayed
And no data is persisted
```

### Recording Medication Supply

**Scenario 9: Record pack size**

```gherkin
Given a medication has been added
And pack size information is available from DM+D
When the user enters a pack size value
Then the pack size shall be recorded against that medication
And the value shall be retained when navigating between fields
```

**Scenario 10: Record quantity supplied**

```gherkin
Given a medication has been added
When the user enters a quantity supplied value
Then the quantity supplied (dose units) shall be recorded against that medication
And the value shall be persisted
And the Dosage field shall become enabled (if previously disabled)
```

**Scenario 11: Record dosage**

```gherkin
Given a medication has been added
When the user enters dosage instructions (e.g., "Apply when needed")
Then the dosage shall be recorded against that medication
And the value shall be retained
```

**Scenario 12: Record multiple medications**

```gherkin
Given multiple medications have been added
When supply details are entered for each medication
Then the quantity supplied and dosage recorded for each medication shall be retained independently
And changes to one medication shall not affect others
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

**Scenario 17: Continue consultation**

```gherkin
Given all mandatory information has been completed
  (At least one medication with Quantity supplied and Dosage recorded)
When the user clicks Continue
Then the medication details and patient advice shall be saved against the consultation
And the user shall progress to the next consultation page
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

**Scenario 21: At least one medication required**

```gherkin
Given no medications have been added
When the user clicks Continue
Then the user shall remain on the current page
And an error message shall be displayed: "At least one medication must be supplied to proceed"
And the error summary shall appear at the top of the page
And focus shall be moved to the error message
And no data is persisted
```

**Scenario 22: Quantity supplied mandatory**

```gherkin
Given a medication has been added
And Quantity supplied has not been entered
When the user clicks Continue
Then the user shall remain on the page
And an error message shall be displayed for that medication: "Quantity supplied is required"
And the error is highlighted in red near the Quantity supplied field
And no data is persisted
```

**Scenario 23: Dosage mandatory**

```gherkin
Given a medication has been added
And Dosage has not been entered
When the user clicks Continue
Then the user shall remain on the page
And an error message shall be displayed for that medication: "Dosage is required"
And the error is highlighted in red near the Dosage field
And no data is persisted
```

**Scenario 24: DM+D search unavailable**

```gherkin
Given the DM+D search service is unavailable
When the user performs a medication search
Then an appropriate error message shall be displayed: "The medication search service is temporarily unavailable. Please try again."
And medication search results shall not be returned
And the error shall be logged in accordance with system standards
And the user shall be able to continue filling other fields on the page
```

### Accessibility

**Scenario 25: WCAG 2.1 AA compliance**

```gherkin
Given the page is rendered
When a user navigates using keyboard only
Then all interactive elements are focusable in this order:
  - Search medicine input → Generic radio → Branded radio → Medication suggestions (when visible)
  - Quantity supplied field → Dosage field → Remove link (per medication row)
  - Advice textarea → Previous button → Continue button → Cancel and exit link
And screen reader announces the page structure: "Outcome Supply"
And the Generic radio is announced as "Generic search selected"
And the Branded radio is announced as "Branded search not selected"
And the Advice textarea is announced with its 2000-character limit
And the Remove link is announced as a destructive action
And all text has a colour contrast ratio of at least 4.5:1
And error messages are announced immediately via aria-live="polite"
And form validation errors are grouped in an Error Summary with role="alert"
```

---

## Definition of Done

- [ ] All acceptance criteria met and validated by QA
- [ ] Code peer reviewed and approved
- [ ] Unit tests written (80%+ coverage)
- [ ] Medication search component (EMS #586964) is available and integrated
- [ ] Integration with DM+D search service is configured
- [ ] Accessibility testing completed (WCAG 2.1 AA)
- [ ] UI matches design mockup
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
- Pack size handling may differ from CAS pathway (optional vs. pre-populated)
- Character counter for textarea must display in real-time as per UI-TEXTAREA-SPECIFICATIONS.md
- This story differs from the CAS Supply outcome (603158) in that it uses medication search rather than a pre-populated formulary
