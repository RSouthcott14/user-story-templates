# UI: CCM Supply and Refer - Combined Outcome

## Story Type: UI
## Tags: `AI Generated` | `Review (BR)`

---

## Description

### Story Card
**As a** pharmacy professional  
**I want** to supply medication under the Independent Prescribers Service AND record a patient referral in a single consultation page  
**So that** both the medication supply details and referral information are accurately captured during a Clinical Conditions Management consultation with a combined outcome

### Context
This page forms part of the **Clinical Conditions Management (CCM) pathway** and is displayed when the consultation outcome **"Supply and refer"** has been selected. This combines two separate journeys:

**Supply Section (IPS Pathway):**
- Pharmacy professionals search for and select medications from the **DM+D (Dictionary of Medicines and Devices)** dataset
- Record pack size, quantity supplied, and dosage for medications
- Optional advice to patient

**Referral Section (Refer Pathway):**
- Select a referral destination (GP, Dentist, Optometrist, Out of Hours, A&E, Other)
- Select referral timescale (Within 24 hours, Within 48 hours, Within 7 days, Within 14 days, Within 28 days)
- Mandatory referral information (why the patient is being referred)
- Optional advice to patient

Both sections are presented on a single page to streamline the consultation workflow when users need to both supply medication AND refer the patient to another service.

### Captions
**Journey step:** Clinical Conditions Management  
**Page caption:** Outcome: Supply and refer

### Out of Scope
- Save and continue functionality at page level (submission logic)
- Medication interaction checking or clinical validation
- Prescription generation or transmission
- Editing selections after submission
- Bulk or repeat dispensing workflows
- Referral form generation, PDF export, or printing
- Integration with external referral systems or NHS e-Referral Service
- Automatic pre-population of referral options based on patient conditions
- Tracking referral status after submission

### Related Stories
- **603157**: CCM – Select Outcome (upstream selection)
- **603153**: CCM IPS – Working Diagnosis (upstream decision)
- **603150**: CCM Outcome Selection Limited Options (upstream context)
- **586964**: EMS – Medication Search Component (component dependency)
- **585673**: EMS – Medication Search Implementation (component implementation)
- **592874**: API – GET DM+D Medicine Search (backend dependency)
- **595522**: API – GET DM+D Medicine Packs (backend dependency)

### Dependent Stories
- Future stories that consume IPS and referral consultation data as a combined outcome
- Next step in CCM pathway after this page

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

- **Radio Button Group** — Referred to (6 options) and Timescale for referral (5 options)
  [Figma: Radio Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-79)

- **Textarea** — Referral information (mandatory), Advice given to patient (optional), both with 2000-character limit
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

- The consultation outcome selected is **Supply and refer**
- Medication search is performed using the NHS **DM+D (Dictionary of Medicines and Devices)** service
- Generic medications are searched by default
- **Pack size is a dropdown list** returned from DM+D and is **mandatory**
- **Quantity supplied is mandatory** - accepts positive whole numbers only (no negatives, decimals, characters, or special characters)
- Dosage is captured as free text and is ALWAYS enabled when a medication is added
- Both supply and referral sections must be completed before consultation can progress
- At least one medication must be supplied AND a referral destination and timescale must be selected
- **Referral information is mandatory** (why the patient is being referred)
- Advice to patient is optional in both sections
- Consultation state persistence follows the standard Choose Pharmacy consultation behaviour
- Added medications table only displays after the first medication is selected
- If the user removes the only medication, the table section is hidden

---

## Acceptance Criteria

**Total Scenarios: 21**

### Page Structure & Content

**Scenario 1: Display Supply and Refer outcome page**
```gherkin
Given the user has selected Outcome = Supply and refer within a CCM consultation
When the page loads
Then the page displays:
  - High level caption: "Clinical Conditions Management"
  - Page heading: "Outcome: Supply and refer"
  - SUPPLY SECTION heading: "Supply medication under Independent Prescribers Service"
  - Subheading: "Search for medication"
  - Help text: "Only select a branded medication if there is a clinical need for it to be used (e.g., pharmacokinetic differences between brands)"
  - Medication search area with Generic/Branded toggle
  - Search medicine input field
  - REFERRAL SECTION heading: "Referral"
  - Radio button group: "Referred to" with 6 options (GP, Dentist, Optometrist, Out of Hours, A&E, Other)
  - Radio button group: "Timescale for referral" with 5 options (Within 24 hours, Within 48 hours, Within 7 days, Within 14 days, Within 28 days)
  - Textarea: "Referral information - Why have you referred this patient?" (mandatory, 2000 char limit)
  - Textarea: "Advice to patient - Advice given to patient" (optional, 2000 char limit)
  - Advice to patient (optional) section with textarea
  - Previous button (secondary)
  - Continue button (primary, labeled "Save and continue")
  - Cancel and exit link
```

**Scenario 2: Default search type for medication**
```gherkin
Given the user accesses the Supply and Refer outcome page
When the page loads
Then Generic shall be selected by default in the medication search
And Branded shall be available for selection
```

**Scenario 3: Display medication search controls**
```gherkin
Given the user is on the Supply and Refer outcome page
When the page loads
Then a medication search field shall be displayed
And a Generic radio button shall be displayed and selected
And a Branded radio button shall be displayed and not selected
```

### Medication Search & Selection

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

### Supply Section - Added Medications

**Scenario 7: Display added medication table**
```gherkin
Given a medication has been selected from DM+D
When the medication is added
Then the "Added medication under Independent Prescribers Service" section becomes visible with title
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
Given the user is on the Supply and Refer outcome page
When no medications have been added
Then the "Added medication" section is not displayed

When the user adds a medication to the consultation
Then the "Added medication" section is displayed
And a table is displayed with the columns:
  | Medication | Pack size | Quantity supplied (dose units) | Dosage |

When the user removes the last medication from the consultation
Then the "Added medication" section is hidden
And the medication table is no longer displayed
```

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
When the user enters a whole number in the quantity supplied field
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

### Referral Section - User Interaction

**Scenario 14: User selects referral destination option**
```gherkin
Given the user is on the Supply and Refer outcome page
When the user clicks a "Referred to" radio button option (e.g., GP)
Then the selected option is immediately visible (radio button filled)
And any previously selected option in the same group is deselected
And the selection is visually highlighted with feedback
```

**Scenario 15: User selects referral timescale option**
```gherkin
Given the user is on the Supply and Refer outcome page
When the user clicks a "Timescale for referral" radio button option (e.g., Within 24 hours)
Then the selected option is immediately visible (radio button filled)
And any previously selected option in the same group is deselected
And the selection is visually highlighted with feedback
```

**Scenario 16: User enters referral information and advice with character tracking**
```gherkin
Given the user is on the Supply and Refer outcome page
When the user enters text in the "Referral information" textarea (mandatory)
Then the text is recorded as the user types
And the character counter updates in real-time showing: "You have X characters remaining"
And the textarea text wraps automatically and scrolls if content exceeds visible area
And when the text reaches 2000 characters
Then the user shall not be permitted to enter additional characters
And the character counter displays "You have 0 characters remaining"

When the user enters text in the "Advice to patient" textarea (optional)
Then the text is recorded as the user types
And the character counter updates in real-time showing: "You have X characters remaining"
And the textarea is expandable and scrollable
And when the text reaches 2000 characters
Then the user shall not be permitted to enter additional characters
And the character counter displays "You have 0 characters remaining"
And advice to patient is optional - user may leave blank and still continue subject to other validation requirements
```

### Validation - Comprehensive

**Scenario 17: Comprehensive validation for all mandatory fields from both sections**
```gherkin
Given one or more of the following validation conditions apply from the Supply section:
  - No medication has been supplied
  - A supplied medication has a Quantity of 0
  - A medication has been supplied and no dosage has been entered
  - Pack size not selected for a medication
  
OR from the Referral section:
  - No "Referred to" option selected
  - No "Timescale for referral" option selected
  - "Referral information" textarea is empty or blank

When the user selects "continue"
Then an error summary box appears at the top of the page
And the error summary displays all applicable errors:
  | Validation Condition | Error Message |
  | --- | --- |
  | No medication supplied | Select at least one medication |
  | Medication supplied with quantity 0 | Enter a quantity greater than zero |
  | Medication supplied without dosage | Enter dosage for selected medication |
  | Pack size not selected | Select a pack size |
  | No referral destination selected | Select referred to |
  | No referral timescale selected | Select timescale for referral |
  | Referral information missing | Enter why you have referred this patient |

And the error summary has a red border and alert icon
And the heading displays: "There is a problem"
And each error is displayed as a clickable link
And selecting an error navigates focus to the associated field or section
And focus moves to the error summary when it is displayed
And an inline error is displayed against each affected field or section
And the user remains on the Supply and Refer page
And no data is persisted
And validation is re-evaluated only when the user next selects "continue"
```

### Navigation & State Management

**Scenario 18: Continue consultation with valid data from both sections**
```gherkin
Given all mandatory information has been completed:
  - At least one medication added
  - Pack size selected for each medication
  - Quantity supplied entered for each medication
  - Dosage entered for each medication
  - "Referred to" option selected
  - "Timescale for referral" option selected
  - "Referral information" textarea completed
When the user clicks "Save and continue"
Then the medication details and patient advice from Supply section shall be stored in the consultation model
And the referral details and advice from Referral section shall be stored in the consultation model
And the user shall progress to the next page: "CCM Sensitive Health Information" page
```

**Scenario 19: Navigate to previous page from Limited Outcome Selection**
```gherkin
Given the user is on the Supply and Refer outcome page
And the user navigated here from the Limited Outcome Selection page
When the user clicks Previous
Then the user shall be returned to the Limited Outcome Selection page
And the "Supply and refer" outcome selection is still selected on that page
And all medication details entered shall be retained in consultation state
And all referral details entered shall be retained in consultation state
```

**Scenario 20: Navigate to previous page from Full Outcome Options**
```gherkin
Given the user is on the Supply and Refer outcome page
And the user navigated here from the Full Outcome Options page
When the user clicks Previous
Then the user shall be returned to the Outcome Selection page
And the "Supply and refer" outcome selection is still selected on that page
And all medication details entered shall be retained in consultation state
And all referral details entered shall be retained in consultation state
```

**Scenario 21: Cancel and exit consultation**
```gherkin
Given the user is on the Supply and Refer outcome page
When the user clicks Cancel and exit
Then the user is navigated to the patient overview page
And no data is saved
```

---

## Out of Scope

- Medication interaction checking between multiple supply medications
- Clinical validation of referral appropriateness
- Referral form generation or export (PDF, HL7, etc.)
- Integration with external referral tracking systems
- Automatic pre-population of referral based on condition or medication
- Referral status tracking or confirmation receipt
- Medication substitution or therapeutic alternatives display
- Formulary restrictions or compliance checking
- Bulk referrals or batch medication supply
- Editing submitted Supply and Refer data after submission

---

## Definition of Done

- [ ] All acceptance criteria (21 scenarios) met and validated by QA
- [ ] Code peer reviewed and approved
- [ ] Unit tests written (80%+ coverage)
- [ ] Medication search component (EMS #586964) is available and integrated
- [ ] Integration with DM+D search service is configured
- [ ] Pack size dropdown populated from DM+D data
- [ ] Quantity supplied field validates: whole numbers only (no negatives, decimals, characters, special chars)
- [ ] Dosage field is always enabled (never disabled conditionally)
- [ ] Supply section table visibility: hidden when no medications, visible after first medication added, hidden when all removed
- [ ] Both Supply and Referral sections are visible and distinct on single page
- [ ] Radio button groups display correct number of options (6 for "Referred to", 5 for "Timescale for referral")
- [ ] Character counters display for both "Referral information" and "Advice to patient" textareas
- [ ] Character limit of 2000 characters enforced on all textareas
- [ ] Error summary displays all errors from both sections with correct messages
- [ ] Error summary follows standards with "There is a problem" heading
- [ ] UI matches design mockup (Supply and Refer combined page)
- [ ] "Save and continue" button text matches design
- [ ] Browser compatibility verified (Chrome, Firefox, Safari, Edge)
- [ ] WCAG 2.1 AA accessibility compliance verified
- [ ] Tab order verified (Previous → Medication Search → Search field → Pack size → Quantity → Dosage → Remove links → Advice → Referral options → Referral info → Advice → Continue → Cancel/exit)
- [ ] Screen reader announcements tested
- [ ] Documentation updated (if applicable)
- [ ] Ready for UAT

---

## Notes

### Supply Section Notes
- The medication search component is reused from the EMS journey (stories #586964 and #585673)
- DM+D integration will be handled by a separate backend API
- **Pack size is a mandatory dropdown** populated from DM+D data
- **Quantity supplied field validation** must prevent negative numbers, decimals, letters, and special characters at the field level (not just on submit)
- **Dosage field is always enabled** when a medication is added
- **Table visibility**: The "Added medication" section and table are hidden until the first medication is added, and hidden again if all medications are removed
- Character counter for supply advice textarea must display in real-time as per UI-TEXTAREA-SPECIFICATIONS.md (2000 character limit)

### Referral Section Notes
- Both textareas (Referral information and Advice) must have character counters with aria-live="polite"
- Character limit of 2000 characters must be enforced (user cannot type beyond limit)
- **Referral information is mandatory** (unlike Advice to patient which is optional)
- No radio options are pre-selected on initial page load
- Selected referral options must be retained when user navigates backward and returns

### Combined Page Considerations
- Supply section appears above Referral section on the page
- Both sections are equally important and must be completed for consultation to progress
- Validation errors must list all failures from both sections in a single error summary
- State persistence must track both supply medications AND referral selections throughout consultation lifecycle
- When user navigates Previous or returns to page, both sections' data must be restored
- Error navigation should scroll to and focus relevant section
- Character counters for supply advice and referral information/advice should operate independently
- Page layout must accommodate both sections with clear visual separation

### Design References
- Supply section components: Based on #603159 (IPS Supply Pathway)
- Referral section components: Based on DRAFT-CCM-Record-Referral-Outcome
- Combined layout: User-provided design showing both sections on single page
- Design System baseline: DHCW Design System V2

### Implementation Guidance
- **Data Persistence Timing:** Consultation model state is updated **when user clicks "Save and continue"** (after all selections and entries, before navigation to next page) — NOT immediately on selection or field blur
- Visual feedback on selection/entry is immediate (radio button fills, medication added to table, character counter updates)
- Consultation model storage is deferred until "Save and continue" action
- This ensures data is only committed when user confirms they want to proceed
- Page must render both sections visible from initial load to allow user to understand full scope of required data entry

### Known Considerations
- Both Supply and Referral data must persist if user navigates backward and returns
- Consultation model must retain data across multiple navigation cycles
- Error messaging must be clear and not overwhelming with large error list (can use scrollable error summary if needed)
- Character counters must remain accurate even after page navigation and return
- Medication table must not interfere with Referral section visibility or interaction
- Section spacing must prevent visual confusion between Supply medications and Referral options
