# UI: CAS Supply Medication Outcome Selection

## Story Type: UI | Dependent on: #613956

---

## Description

### Story Card
**As a** pharmacy user  
**I want** to supply medication from the appropriate formulary based on the CAS condition assessment  
**So that** I can complete the consultation with the correct medicines and document any backup antibiotic supply decision

### Context
This story is part of the Clinical Conditions Management (CCM) journey when the outcome is to **Supply** medication under the Common Ailments Service (CAS). 

The page displays medication options in one or two sections depending on the CAS condition:
- **STTT (Sore Throat) or UTI (Urinary Tract Infection):** Two sections—formulary medicines and antibiotics
- **Any other CAS condition:** Single section—formulary medicines only

The user selects medicine(s), specifies quantities and dosages, and optionally indicates if antibiotics are a backup supply. This data feeds into the consultation record and prescription generation.

**Related Journey Step**: CCM Consultation → Outcome Decision → Supply Medication → Complete Consultation

### Captions
**Journey step:** Clinical Conditions Management  
**Page caption:** Outcome: Supply

### Out of Scope
- Formulary maintenance or medication catalogue updates
- Pricing, cost analysis, or drug interaction checking
- Prescription printing or ePrescription transmission
- Editing selections after submission (would require separate edit journey)
- Historical medication recommendations or previous supply records
- Bulk/repeat dispensing workflows
- Pharmacokinetic or dosage calculations beyond user entry

### Related/Dependent Stories
- **#613956**: API to retrieve condition-specific medications and formulary data — *blocks this story*
- **#603150**: CCM Outcome Selection (Limited Options) — *upstream*
- Related Feature: Clinical Conditions Management (CAS Supply Pathway)

---

## Design Reference

**Design System:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Component Guidance:** [COMPONENTS.md](../COMPONENTS.md)

**Error Messaging Standard:** [standards/UI-ERROR-MESSAGING.md](../standards/UI-ERROR-MESSAGING.md)

### Components Used

- **Table** — Display medicines with pack size, quantity, and dosage fields
  [Figma: Table](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-79)

- **Spinners (Number Input)** — Quantity supplied (dose units)
  [Figma: Number Input](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

- **Text Input** — Dosage field
  [Figma: Text Input](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

- **Radios** — "Is this a backup supply of antibiotics?" (Yes/No)
  [Figma: Radios](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6058-4625)

- **Textarea** — Advice to patient (optional)
  [Figma: Textarea](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6218-4824)

- **Buttons** — Previous (secondary), Save and continue (primary)
  [Figma: Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475)

- **Action Link** — Cancel and exit
  [Figma: Action Link](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9493)

- **Error Summary** — Validation error display
  [Figma: Error Summary](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4585)

---

## Acceptance Criteria

**Total Scenarios: 11**

### Page Structure & Content

**Scenario 1: Display medication supply page with formulary section only (non-STTT/UTI conditions)**

```gherkin
Given the user has selected a CAS condition that is NOT STTT or UTI
When the page loads
Then the page displays:
  - Journey caption: "Clinical Conditions Management" (small text, above heading)
  - Page heading: "Outcome: Supply" (prominent)
  - Instruction text: "Supply medication under Common Ailments Service"
  - Single section heading: "Select medicine(s) from the formulary below"
  - Table with columns: Medicine, Pack size, Quantity supplied (dose units), Dosage
  - Multiple medicine rows populated from API #613956 (each row shows medicine details and input fields)
  - Previous button (secondary)
  - Save and continue button (primary)
  - Cancel and exit link
And the patient banner displays available patient information above the main content
And NO antibiotics section is displayed
And NO "Is this a backup supply?" question is displayed
```

**Scenario 2: Display medication supply page with both formulary and antibiotics sections (STTT or UTI)**

```gherkin
Given the user has selected a CAS condition that IS STTT or UTI
When the page loads
Then the page displays:
  - Journey caption: "Clinical Conditions Management" (small text, above heading)
  - Page heading: "Outcome: Supply" (prominent)
  - Instruction text: "Supply medication under Common Ailments Service"
  - First section heading: "Select medicine(s) from the formulary below"
  - Formulary medicines table: Medicine, Pack size, Quantity supplied (dose units), Dosage (rows populated from API #613956)
  - Second section heading: "Select antibiotics from the formulary below"
  - Antibiotics table: Medicine, Pack size, Quantity supplied (dose units), Dosage (rows populated from API #613956)
  - Question: "Is this a backup supply of antibiotics?" with Yes/No radio buttons and supporting text
  - Section: "Advice to patient (optional)" with textarea for free text entry
  - Previous button (secondary)
  - Save and continue button (primary)
  - Cancel and exit link
And both medicine lists are populated from API #613956
And the patient banner displays available patient information above the main content
```

### Default State

**Scenario 3: Initial page state with no selections**

```gherkin
Given the user navigates to the medication supply page
When the page loads for the first time
Then all quantity fields display: "0"
And all dosage fields are empty
And no medicine rows are pre-selected
And the "Is this a backup supply?" radio buttons show no selection
And the "Advice to patient" textarea is empty
And the user is required to make selections before proceeding
```

**Scenario 4: Retained selections when user returns to page**

```gherkin
Given the user has previously selected medicines and entered quantities
When the user navigates away and returns to the page
Then all previously selected medicines retain their quantities
And all previously entered dosages are retained
And previously selected "Is this a backup supply?" option remains selected
And previously entered advice text is retained
```

### User Interaction

**Scenario 5: User selects and updates medicine quantity**

```gherkin
Given the user is on the medication supply page
When the user modifies the quantity field for a medicine to "5"
Then the quantity spinner updates to "5"
And the updated quantity is displayed in the table row
And the selection persists when navigating within the page
```

**Scenario 6: User enters dosage information**

```gherkin
Given the user is on the medication supply page
And a medicine quantity has been selected
When the user enters "15mg twice daily" in the dosage field
Then the dosage text is displayed in the field
And the dosage entry is associated with the correct medicine row
And the entry persists
```

**Scenario 7: User selects backup antibiotic supply option**

```gherkin
Given the page displays the "Is this a backup supply of antibiotics?" question
When the user selects "Yes"
Then the "Yes" radio button becomes selected
And visual feedback shows the selected state
And the selection persists
```

**Scenario 8: User enters optional advice text**

```gherkin
Given the "Advice to patient (optional)" textarea is displayed
When the user enters: "Take with food. Do not take with dairy products."
Then the text is displayed in the textarea
And the text persists when navigating within the page
```

### Navigation

**Scenario 9: User saves and continues with valid medication selections**

```gherkin
Given the user is on the medication supply page
And at least one medicine has been selected with quantity > 0
When the user clicks "Save and continue"
Then the medication selections are validated
And the page data is saved
And the user is navigated to the consultation completion or next step page
And the selected medicines, quantities, dosages, and advice are passed forward
And the backup antibiotic flag (if applicable) is included in the submission
```

**Scenario 10: User returns to previous page**

```gherkin
Given the user is on the medication supply page
And medicine selections and advice have been entered
When the user clicks the "Previous" button
Then the user is navigated to the previous page in the consultation
And all entered information is retained
And the user can return to this page with data intact
```

**Scenario 11: User cancels consultation**

```gherkin
Given the user is on the medication supply page
When the user clicks "Cancel and exit"
Then the user is navigated to the patient overview page
And no medication data is saved
And the consultation is abandoned
```

### Validation

**Scenario 12: Validation when only formulary section shown and no medicines selected**

```gherkin
Given the page displays only the formulary section (non-STTT/UTI condition)
When the user clicks "Save and continue" without selecting any medicines
Then an error summary displays at the top of the page
And focus moves to the error summary
And the error message states: "Select at least one medicine"
And the error links to the formulary medicines table
And the table displays validation styling (red border/icon)
And the user remains on the medication supply page
And no data is persisted
And when the user selects a medicine with quantity > 0 and resubmits, the validation error is removed
```

**Scenario 13: Validation when both sections shown and no medicines selected**

```gherkin
Given the page displays both formulary and antibiotics sections (STTT/UTI condition)
When the user clicks "Save and continue" without selecting any medicines from either section
Then an error summary displays at the top of the page
And the error message states: "Select at least one medicine or antibiotic"
And the error links to the medicines section
And both the formulary and antibiotics tables display validation styling
And the user remains on the medication supply page
And no data is persisted
And when the user selects at least one medicine (formulary OR antibiotic) and resubmits, the validation error is removed
```

**Scenario 14: Validation allows multiple formulary with zero or one antibiotic selection**

```gherkin
Given the page displays both formulary and antibiotics sections
When the user selects multiple formulary medicines and one antibiotic with quantities
And the user clicks "Save and continue"
Then the form validates successfully
And the data is saved with all selected medicines
And the user proceeds to the next step
```

### Accessibility

**Scenario 15: Keyboard navigation and screen reader support**

```gherkin
Given the page is rendered
When a user navigates using Tab key
Then focus order follows: Previous button → First medicine quantity field → Subsequent quantity fields → Dosage fields → Antibiotics section (if present) → "Is this a backup supply?" radio group → Advice textarea → Save and continue button → Cancel and exit link
And each table cell contains descriptive labels or headers identifying: Medicine name, Pack size, Quantity, Dosage
And the radio group "Is this a backup supply of antibiotics?" is announced as a fieldset with legend
And each radio option announces: "[Yes/No], radio button, [selected/not selected]"
And error messages use role="alert" with aria-live="polite"
And all form inputs have associated labels (even if hidden from sighted users)
And required selection indicators are announced by screen readers
And colour contrast is at least 4.5:1 (text vs. background)
And focus indicators are visible with minimum 3:1 contrast
```

---

## Definition of Done

- [ ] All 15 acceptance criteria met and validated by QA
- [ ] API #613956 completed and integrated
- [ ] Code peer reviewed and approved
- [ ] Unit tests written (80%+ coverage)
- [ ] Accessibility testing completed (WCAG 2.1 AA)
- [ ] UI matches Figma design
- [ ] Browser compatibility verified (Chrome, Firefox, Safari, Edge)
- [ ] Mobile responsiveness tested (portrait/landscape at 375px, 768px, 1024px)
- [ ] Medication data persists correctly across navigation
- [ ] Conditional sections (STTT/UTI vs. other) render correctly
- [ ] Validation errors display and clear as expected
- [ ] Documentation updated (consultation pathway, data model)
- [ ] Ready for UAT

---

## Technical Notes

- Medicine list is populated by API #613956 based on CAS condition selection
- Conditional rendering: If condition in [STTT, UTI], display both sections; otherwise show formulary only
- Quantity validation: 0 (no selection) vs. > 0 (selected)
- Backup antibiotic flag: Only applicable when antibiotics section is shown; not required but should be captured if selected
- Data structure: Each selected medicine should include {medicineId, quantity, dosage}
