# UI: CAS Supply and Refer - Combined Outcome

## Story Type: UI
## Tags: `AI Generated` | `Review (BR)`

---

## Description

### Story Card
**As a** pharmacy professional  
**I want** to supply medication under the Common Ailments Service AND record a patient referral in a single consultation page  
**So that** both the medication supply details and referral information are accurately captured when the outcome requires both supply and referral

### Context
This page forms part of the **Clinical Conditions Management (CCM) pathway** and is displayed when the consultation outcome **"Supply and refer"** has been selected within a CAS consultation. This combines two separate journeys:

**Supply Section (CAS Pathway):**
- Formulary medications retrieved from the selected CAS condition
- Antibiotic medications also retrieved and displayed separately
- Quantity supplied (dose units) as a dropdown with "0" as default
- Dosage field that is disabled until quantity > 0 is selected
- When quantity reverts to 0, dosage is cleared and disabled
- Backup supply question for antibiotics only (displayed when antibiotic quantity > 0)
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
- Formulary maintenance or medication catalogue updates
- Medication interaction checking or clinical validation
- Prescription generation or transmission
- Editing selections after submission
- Bulk or repeat dispensing workflows
- Referral form generation, PDF export, or printing
- Integration with external referral systems or NHS e-Referral Service
- Tracking referral status after submission

### CAS Pathway Validation Rules
1. **Formulary Only (Non-STTT/UTI):** At least one formulary medication MUST be supplied (quantity > 0)
2. **Formulary + Antibiotics:** At least one medication from either category MUST be supplied (quantity > 0) OR One antibiotic ONLY + Multiple formulary medications supplied

### Related Stories
- **#605455**: Retrieve CAS Formulary Medications (API dependency)
- **#603150**: CCM Outcome Selection (upstream selection)
- Related Feature: Clinical Conditions Management (CAS Supply and Referral Pathway)

### Dependent Stories
- Future stories that consume CAS supply and referral consultation data as a combined outcome

---

## Design Reference

**Design System:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Component Guidance:** [COMPONENTS.md](../COMPONENTS.md)

**Error Messaging Standard:** [standards/UI-ERROR-MESSAGING.md](../standards/UI-ERROR-MESSAGING.md)

**Textarea Standard:** [standards/UI-TEXTAREA-SPECIFICATIONS.md](../standards/UI-TEXTAREA-SPECIFICATIONS.md)

### Components Used

- **Table** — Display medications with quantity dropdown and dosage fields
  [Figma: Table](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-79)

- **Select/Dropdown** — Quantity supplied (dose units) with "0" as default
  [Figma: Select](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6242-6391)

- **Text Input** — Dosage field (disabled by default, enabled when quantity > 0)
  [Figma: Text Input](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

- **Radio Button Group** — Referred to (6 options) and Timescale for referral (5 options)
  [Figma: Radio Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-79)

- **Textarea** — Referral information (mandatory), Advice given to patient (optional), both with 2000-character limit
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

## Assumptions

- The consultation outcome selected is **Supply and refer**
- Formulary medications are retrieved from the CAS API (#605455)
- **Quantity supplied is a dropdown** with "0" as the default option
- When quantity = 0, dosage field is **disabled**
- When quantity is changed from 0 to any value > 0, dosage becomes **enabled**
- When quantity is changed back to 0, dosage is **cleared** and **disabled**
- At least one medication must have quantity > 0 before consultation can progress
- **Referral information is mandatory** (why the patient is being referred)
- Advice to patient is optional in both sections
- Consultation state persistence follows standard Choose Pharmacy consultation behaviour
- Backup supply questions are displayed below antibiotic medications only when quantity > 0 is selected
- Backup supply questions are hidden for formulary medications regardless of quantity

---

## Acceptance Criteria

**Total Scenarios: 23**

### Page Structure & Content

**Scenario 1: Display Supply and Refer outcome page with both sections**
```gherkin
Given the user has selected Outcome = Supply and refer within a CAS consultation
When the page loads
Then the page displays:
  - High level caption: "Clinical Conditions Management"
  - Page heading: "Outcome: Supply and refer"
  - Link to Common Ailments Service formulary to learn more about ailments
  - SUPPLY SECTION heading: "Supply medication under Common Ailments Service"
  - Subheading: "Select medicine(s) from the formulary below"
  - Medication table with columns: Medicine, Pack size, Quantity supplied (dose units), Dosage
  - REFERRAL SECTION heading: "Referral"
  - Radio button group: "Referred to" with 6 options (GP, Dentist, Optometrist, Out of Hours, A&E, Other)
  - Radio button group: "Timescale for referral" with 5 options (Within 24 hours, Within 48 hours, Within 7 days, Within 14 days, Within 28 days)
  - Textarea: "Referral information - Why have you referred this patient?" (mandatory, 2000 char limit)
  - Textarea: "Advice to patient - Advice given to patient" (optional, 2000 char limit)
  - Previous button (secondary)
  - Continue button (primary, labeled "Save and continue")
  - Cancel and exit link
```

**Scenario 2: Retrieve and display CAS formulary and antibiotic medications**
```gherkin
Given the user has reached the Supply and Refer page
And a CAS condition has been selected
When the page loads
Then the system shall retrieve formulary medications using API #605455
And the system shall retrieve antibiotic medications for that condition
And both formulary and antibiotic medications shall be displayed in the medication table
And only medications returned by the API shall be displayed
And if the API call fails, an error message is displayed
```

**Scenario 3: Default state for quantity and dosage fields**
```gherkin
Given formulary medications are displayed
When the page is first loaded
Then the quantity supplied dropdown for each medication defaults to "0"
And the dosage field for each medication is disabled (grayed out, non-editable)
And the advice textarea is empty
And the character counter displays: "You have 2000 characters remaining"
```

### Supply Section - Quantity and Dosage Interaction

**Scenario 4: User selects quantity from dropdown - dosage becomes enabled**
```gherkin
Given a medication row is displayed with quantity dropdown set to "0"
And the dosage field is disabled
When the user clicks the quantity dropdown and selects any value greater than 0
Then the quantity is recorded with the selected value
And the dosage field becomes enabled immediately (no longer grayed out)
And the dosage field becomes editable
And focus remains on the quantity field or can be moved to dosage
```

**Scenario 5: User enters dosage when quantity is greater than 0**
```gherkin
Given a medication has quantity > 0 selected
And the dosage field is enabled
When the user enters dosage instructions
Then the dosage text is recorded and retained
And if multiple medications are present, each dosage is retained independently
And changes to one medication's dosage do not affect others
```

**Scenario 6: User reverts quantity from non-zero back to 0 - dosage clears and disables**
```gherkin
Given a medication has quantity > 0 with dosage entered
When the user clicks the quantity dropdown and changes the value back to "0"
Then the quantity is updated to "0"
And the dosage field is immediately disabled (grayed out)
And any previously entered dosage text is cleared
And the dosage field becomes non-editable again
```

**Scenario 7: User interaction with multiple medications (formulary and antibiotics)**
```gherkin
Given multiple medications (both formulary and antibiotic) are displayed
When the user records different quantities and dosages for each medication
Then each medication's quantity and dosage values are retained independently
And enabling/disabling dosage for one medication does not affect others
And data persists when user navigates between sections
And backup supply question state for antibiotics is retained independently from dosage state
```

**Scenario 7b: Backup supply question for antibiotics only**
```gherkin
Given page contains formulary and antibiotic medications
When page loads
Then backup supply question is hidden for all medications

When user enters quantity > 0 in an antibiotic medication
Then backup supply question displays below that antibiotic medication
With radio options: "Yes" and "No"

When user clears antibiotic quantity (sets to 0)
Then backup supply question is hidden for that antibiotic
And selection is cleared

When user enters quantity in formulary medication
Then backup supply question does NOT appear for formulary medications
```

**Scenario 7c: Backup supply answer recorded**
```gherkin
Given backup supply question is displayed for an antibiotic medication
When user selects "Yes" or "No"
Then selection is recorded against that antibiotic medication
And persists on navigation
And is retained when user returns to the page
```

### Supply Section - Advice to Patient

**Scenario 8: Record advice to patient with character tracking**
```gherkin
Given the user is on the Supply and Refer outcome page
When the user enters text in the "Advice to patient" textarea (optional, in Supply section)
Then the text is recorded as the user types
And the character counter updates in real-time showing: "You have X characters remaining"
And the textarea text wraps automatically and scrolls if content exceeds visible area
And when the text reaches 2000 characters
Then the user shall not be permitted to enter additional characters
And the character counter displays "You have 0 characters remaining"
And advice to patient is optional - user may leave blank and still continue subject to other validation requirements
```

### Referral Section - User Interaction

**Scenario 9: User selects referral destination option**
```gherkin
Given the user is on the Supply and Refer outcome page
When the user clicks a "Referred to" radio button option (e.g., GP)
Then the selected option is immediately visible (radio button filled)
And any previously selected option in the same group is deselected
And the selection is visually highlighted with feedback
```

**Scenario 10: User selects referral timescale option**
```gherkin
Given the user is on the Supply and Refer outcome page
When the user clicks a "Timescale for referral" radio button option (e.g., Within 24 hours)
Then the selected option is immediately visible (radio button filled)
And any previously selected option in the same group is deselected
And the selection is visually highlighted with feedback
```

**Scenario 11: User enters referral information and optional advice with character tracking**
```gherkin
Given the user is on the Supply and Refer outcome page
When the user enters text in the "Referral information" textarea (mandatory)
Then the text is recorded as the user types
And the character counter updates in real-time showing: "You have X characters remaining"
And the textarea text wraps automatically and scrolls if content exceeds visible area
And when the text reaches 2000 characters
Then the user shall not be permitted to enter additional characters
And the character counter displays "You have 0 characters remaining"

When the user enters text in the "Advice to patient" textarea (optional, in Referral section)
Then the text is recorded as the user types
And the character counter updates in real-time showing: "You have X characters remaining"
And when the text reaches 2000 characters
Then the user shall not be permitted to enter additional characters
And the character counter displays "You have 0 characters remaining"
And advice to patient is optional - user may leave blank and still continue
```

### Validation - Comprehensive

**Scenario 12: Comprehensive validation for all mandatory fields from both sections**
```gherkin
Given one or more of the following validation conditions apply from the Supply section:
  - No medication has quantity > 0 supplied
  - A medication has quantity > 0 but no dosage entered
  
OR from the Referral section:
  - No "Referred to" option selected
  - No "Timescale for referral" option selected
  - "Referral information" textarea is empty or blank

When the user selects "continue"
Then an error summary box appears at the top of the page
And the error summary displays all applicable errors:
  | Validation Condition | Error Message |
  | --- | --- |
  | No medication supplied (quantity > 0) | Select at least one medication to supply |
  | Medication supplied without dosage | Enter dosage for selected medication |
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

**Scenario 13: Continue consultation with valid data from both sections**
```gherkin
Given all mandatory information has been completed:
  - At least one medication with quantity > 0 supplied
  - Dosage entered for each medication with quantity > 0
  - "Referred to" option selected
  - "Timescale for referral" option selected
  - "Referral information" textarea completed
When the user clicks "Save and continue"
Then the medication details and patient advice from Supply section shall be stored in the consultation model
And the referral details and advice from Referral section shall be stored in the consultation model
And the user shall progress to the next page in the CCM consultation pathway
```

**Scenario 14: Navigate to previous page - return to Outcome Selection**
```gherkin
Given the user is on the Supply and Refer outcome page
And has entered supply and referral information
When the user clicks Previous
Then the user shall be returned to the Outcome Selection page
And the "Supply and refer" outcome selection is still selected on that page
And all medication details entered (quantity, dosage, advice) shall be retained in consultation state
And all referral details entered (destination, timescale, referral info, advice) shall be retained in consultation state
And when the user navigates forward again to this page
Then all previously entered data is displayed
```

**Scenario 15: Cancel and exit consultation**
```gherkin
Given the user is on the Supply and Refer outcome page
When the user clicks Cancel and exit
Then the user is navigated to the patient overview page
And no data is saved
```

### Consultation State Persistence

**Scenario 16: Consultation state persistence within the same patient encounter**
```gherkin
Given the user has an active consultation within the current patient encounter
And medication details and referral information have been entered
When the user navigates away from the consultation and subsequently returns within the same patient encounter
Then the consultation shall remain active
And the user shall be returned to the Supply and Refer outcome page if it was the last consultation page viewed
And any previously selected quantity values shall be displayed
And any previously entered dosage values shall be displayed
And any previously entered advice (supply section) shall be displayed
And any previously selected referral options shall be displayed
And any previously entered referral information and advice shall be displayed
And all character counters display the correct remaining character counts
```

### API Error Handling

**Scenario 17: CAS formulary medications API unavailable**
```gherkin
Given the CAS formulary API (#605455) is unavailable or fails
When the user accesses the Supply and Refer page
Then an appropriate error message shall be displayed: "The medication formulary service is temporarily unavailable. Please try again."
And the medication table is not displayed
And the error shall be logged in accordance with system standards
And the user shall be able to continue filling the Referral section
```

### Default State on Initial Load

**Scenario 18: No selections or entries are pre-populated on initial page load**
```gherkin
Given the user navigates to the Supply and Refer outcome page for the first time
When the page loads
Then no quantity dropdowns have values selected (all show "0")
And all dosage fields are disabled
And no radio button option is selected in the "Referred to" group
And no radio button option is selected in the "Timescale for referral" group
And both textarea fields (Referral information and Advice) are empty
And character counters display: "You have 2000 characters remaining"
And the "Continue" button is enabled and clickable
```

### Data Persistence Across Navigation

**Scenario 19: Supply and Referral data persists when user navigates to another patient tab and returns**
```gherkin
Given the user has entered supply and referral information
When the user navigates to another patient tab
And subsequently returns to this consultation within the same patient encounter
Then all previously entered selections and text remain displayed:
  - Quantity dropdown values are still selected
  - Dosage text is still visible
  - Referral options are still selected
  - Referral information textarea contains the entered text
  - Advice textarea contains the entered text
And the character counters display the correct remaining character counts
And the consultation model retains all stored supply and referral data
```

### Field State Management

**Scenario 20: Dosage field state consistency across page interactions**
```gherkin
Given a medication has quantity > 0 with dosage entered and displayed
When the user performs page interactions (scrolling, clicking other fields)
Then the dosage field remains enabled
And the dosage value is retained
And the field state does not inadvertently change

When a medication has quantity = 0
Then the dosage field remains disabled
And no text can be entered
And if the user switches to another medication and back, the field remains disabled with no data
```

---

## Out of Scope

- Backup supply questions for antibiotic medications
- Medication interaction checking between multiple medications
- Clinical validation of supply or referral appropriateness
- Formulary maintenance or catalogue updates
- Referral form generation or export (PDF, HL7, etc.)
- Integration with external referral tracking systems
- Automatic pre-population of referral based on medication or condition
- Referral status tracking or confirmation receipt
- Medication substitution or therapeutic alternatives
- Formulary restrictions or compliance checking
- Bulk referrals or batch medication supply
- Editing submitted data after submission

---

## Definition of Done

- [ ] All acceptance criteria (23 scenarios) met and validated by QA
- [ ] Code peer reviewed and approved
- [ ] Unit tests written (80%+ coverage)
- [ ] CAS Formulary API (#605455) integration is configured
- [ ] Quantity supplied dropdown displays with "0" as default value
- [ ] Dosage field is disabled when quantity = 0
- [ ] Dosage field becomes enabled when quantity > 0 is selected
- [ ] Dosage field is cleared and disabled when quantity reverts to 0
- [ ] Validation prevents Continue if no medication has quantity > 0
- [ ] Validation prevents Continue if medication with quantity > 0 has no dosage
- [ ] Validation requires referral destination, timescale, and referral information
- [ ] Error summary displays all applicable errors from both sections
- [ ] Error summary follows standards with "There is a problem" heading
- [ ] Error links navigate to associated fields/sections
- [ ] Focus moves to error summary when validation error occurs
- [ ] Character counters display and update in real-time for all textareas
- [ ] Character limit of 2000 characters enforced on all textareas
- [ ] UI matches design mockup (Supply and Refer combined page)
- [ ] "Save and continue" button text matches design
- [ ] Radio button groups display correct number of options (6 for "Referred to", 5 for "Timescale")
- [ ] Browser compatibility verified (Chrome, Firefox, Safari, Edge)
- [ ] Mobile responsiveness tested
- [ ] Tablet responsiveness tested
- [ ] Consultation state persistence verified for both sections
- [ ] Error messaging follows standards/UI-ERROR-MESSAGING.md
- [ ] Textarea follows standards/UI-TEXTAREA-SPECIFICATIONS.md
- [ ] API error handling tested with #605455 failure
- [ ] Tab order verified for keyboard navigation
- [ ] Documentation updated (if applicable)
- [ ] Ready for UAT

---

## Notes

### Supply Section Notes
- **Quantity supplied is a dropdown** (not a text field) with "0" as the default option
- **Dosage field state is conditional**: disabled when quantity = 0, enabled when quantity > 0
- **Clearing dosage on revert**: When quantity changes from > 0 back to 0, the dosage must be cleared (not just disabled)
- Formulary medications are retrieved from CAS API #605455
- No backup supply questions are included in this combined story
- Backup supply questions are displayed below antibiotic medications only when quantity > 0 is selected
- Backup supply questions are hidden for formulary medications regardless of quantity
- Character limit for supply advice is 2000 characters per UI-TEXTAREA-SPECIFICATIONS.md

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
- State persistence must track both supply quantities/dosages AND referral selections throughout consultation lifecycle
- When user navigates Previous or returns to page, both sections' data must be restored
- Error navigation should scroll to and focus relevant section
- Character counters for supply advice and referral information/advice should operate independently
- Page layout must accommodate both sections with clear visual separation

### Quantity Dropdown Specifics
- Dropdown values are populated from CAS formulary API data
- "0" is the default and represents "not supplied"
- Any value > 0 represents a quantity supplied
- User selecting "0" explicitly disables and clears dosage
- The dropdown selection itself triggers the dosage field state change (not blur or other event)

### Design References
- Supply section components: Based on UI-CAS-Supply-Medication-Outcome (quantity as dropdown, conditional dosage)
- Referral section components: Based on DRAFT-CCM-Record-Referral-Outcome
- Combined layout: User-provided design showing both sections on single page
- Design System baseline: DHCW Design System V2

### Implementation Guidance
- **Data Persistence Timing:** Consultation model state is updated **when user clicks "Save and continue"** (after all selections and entries, before navigation to next page) — NOT immediately on selection or field blur
- Visual feedback on selection/entry is immediate (quantity selected, dosage state changes, character counter updates)
- Consultation model storage is deferred until "Save and continue" action
- This ensures data is only committed when user confirms they want to proceed
- Page must render both sections visible from initial load to allow user to understand full scope of required data entry

### Known Considerations
- Both Supply and Referral data must persist if user navigates backward and returns
- Consultation model must retain data across multiple navigation cycles
- Error messaging must be clear and not overwhelming with large error list
- Character counters must remain accurate even after page navigation and return
- Medication table must not interfere with Referral section visibility or interaction
- Section spacing must prevent visual confusion between medications and referral options
- Dropdown state change must immediately affect dosage field without requiring page refresh
