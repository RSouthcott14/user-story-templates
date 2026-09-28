# UI User Story: Point of Care Test (POCT) Recording

## Story Type: UI

---

## Description

### Story Card
**As a** pharmacy user  
**I want** to record the completion of a Point of Care Test (POCT) within the Clinical Conditions Management consultation workflow  
**So that** I can document patient consent, test kit details, results, and clinical governance information for STTT (Sore Throat Test and Treat) consultations

---

### Context

This page forms part of the Clinical Conditions Management (CCM) consultation workflow following a clinical assessment (FeverPain or Centor scoring). The POCT recording page enables the pharmacy user to:

1. Confirm patient consent for the test
2. Record POCT details (test type, batch number, expiry date) for traceability and clinical governance
3. Record test result and whether result was given to patient
4. Capture optional clinical notes
5. Navigate to the consultation outcomes page

**Service Context (from APPLICATION-CONTEXT.md):**
- **Service:** Sore Throat Test and Treat (STTT)
- **Workflow Stage:** Post-assessment (after FeverPain or Centor scoring)
- **User:** Pharmacy User
- **Clinical Governance:** Test kit, patient consent documentation, result recording

The information captured supports clinical decision-making, regulatory compliance, and patient safety by ensuring complete documentation of test details and outcomes.

---

### Out of Scope

- POCT test kit inventory management (separate story)
- Backend API for retrieving POCT tests (separate API story)

---

### Related/Dependent Stories

- **594832:** CCM Flow Structure — Defines overall CCM consultation workflow
- **TBD:** Select Clinical Scoring Tool — User selects FeverPain or Centor assessment
- **TBD:** FeverPain/Centor Scoring Assessment — Completes symptom assessment before POCT
- **TBD:** STTT Assessment Results & Recommendations — Displays recommendations based on POCT result
- **TBD:** Consultation Outcomes — Final page recording pharmacist actions and referrals

---

## Acceptance Criteria

### Scenario 1: Page Structure & Content

**Title:** POCT recording page displays all required form sections and elements

```gherkin
Scenario 1: User navigates to POCT recording page
  Given the user has completed a clinical assessment (FeverPain or Centor)
  And the user has selected to proceed with POCT testing
  When the POCT recording page loads
  Then the following page structure is displayed:
    - High level caption "Clinical Conditions Management"
    - Page heading: "Point of Care Test" (H1)
    - Section 1: "Patient consent" (H2)
      - Question: "Do you have the patient's consent?" (mandatory)
      - Radio options: "Yes" / "No" [Radios - DHCW Design System V2]
    - Section 2: "Point of Care Test details" (H2)
      - "Name of Point of Care Test" dropdown (mandatory) [Select - DHCW Design System V2]
      - "Batch number" text input (mandatory) [Text Input - DHCW Design System V2]
      - "Expiry date" with Month/Year fields (mandatory) [Date Input - DHCW Design System V2]
    - Section 3: "Test result" (H2)
      - "Test result" radio options (mandatory) [Radios - DHCW Design System V2]:
        * "Positive (for Step A)"
        * "Negative (for Step A)"
      - "Test result given to patient" radio options (mandatory) [Radios - DHCW Design System V2]:
        * "Yes"
        * "No"
      - Conditional "Reason not given" textarea (mandatory if "No" selected) [Textarea - DHCW Design System V2]
    - Section 4: "POCT notes (optional)" (H2)
      - Notes textarea (optional) [Textarea - DHCW Design System V2]
    - Navigation buttons:
      - "Previous" [Buttons - Secondary - DHCW Design System V2]
      - "Save and continue" [Buttons - Primary - DHCW Design System V2]
      - "Cancel and exit" [Action Link - DHCW Design System V2]
  And the layout uses a single-column vertical flow
  And mandatory field indicators (asterisk "*") appear next to required fields
  And the page displays Inset Text with: "All fields marked with * must be completed"
```

---

### Scenario 2: Default State

**Title:** POCT recording page initial appearance with no data entered

```gherkin
Scenario 2: POCT recording page default/initial state
  Given the user has just navigated to the POCT recording page
  When the page loads for the first time
  Then all form fields display in the default state:
    - Patient consent radios: unchecked, all options enabled
    - POCT name dropdown: displays "Please select" placeholder
    - Batch number field: empty, enabled
    - Expiry date Month field: empty, enabled
    - Expiry date Year field: empty, enabled
    - Test result radios: unchecked, all options enabled
    - Test result given radios: unchecked, all options enabled
    - Reason not given textarea: hidden (not displayed)
    - POCT notes textarea: empty, optional, enabled
  And the "Save and continue" button is enabled but styled as inactive
  And no error messages are displayed
  And no data is pre-populated
```

---

### Scenario 3: User Interaction - Action

**Title:** POCT recording page responds to user input and field population

```gherkin
Scenario 3a: User confirms patient consent
  Given the user is on the POCT recording page
  And no consent option has been selected
  When the user clicks the "Yes" radio button for patient consent
  Then the "Yes" option is selected and visually highlighted
  And the selection is retained when user moves to other fields

Scenario 3b: User selects POCT name from dropdown
  Given the user has confirmed patient consent
  When the user clicks the "Name of Point of Care Test" dropdown
  Then the dropdown opens and displays available POCT options
  And the user can select "Strep A (Rapid Antigen Test)"
  Then the selection is recorded in the dropdown
  And the dropdown closes

Scenario 3c: User enters batch number and expiry date
  Given the POCT name has been selected
  When the user enters batch number "B12345"
  And enters expiry date "12/2025"
  Then the values are displayed in the fields

Scenario 3d: User records test result and result given
  Given the previous form sections are complete
  When the user selects "Positive (for Step A)" for test result
  And selects "Yes" for "Test result given to patient"
  Then both selections are recorded
  And the conditional "Reason not given" textarea remains hidden

Scenario 3e: User indicates result NOT given and enters reason
  Given the test result is recorded as "Negative"
  When the user selects "No" for "Test result given to patient"
  Then the "Reason not given" textarea appears
  And the user can type a reason
  And the reason is retained

Scenario 3f: User adds optional POCT notes
  Given the mandatory fields are complete
  When the user types in the "POCT notes" field
  Then the notes are entered and displayed
```

---

### Scenario 4: Navigation

**Title:** POCT recording page navigation flow and data passage

```gherkin
Scenario 4a: User navigates back to previous assessment page
  Given the user is on the POCT recording page
  And has entered some POCT data
  When the user clicks the "Previous" button
  Then the user is navigated back to the previous page
  And the POCT data is retained in session

Scenario 4b: User completes form and navigates forward
  Given the user has completed all mandatory fields correctly
  When the user clicks the "Save and continue" button
  Then the POCT data is submitted
  And the user is navigated to the next page

Scenario 4c: User exits consultation without saving
  Given the user has not saved the form
  When the user clicks the "Cancel and exit" link
  Then a confirmation dialog appears
  And the unsaved data is discarded
  And the user is navigated to the Consultation Overview page
```

---

### Scenario 5: Validation

**Title:** POCT recording page validation and error handling

```gherkin
Scenario 5a: Error when patient consent not provided
  Given the user is on the POCT recording page
  And has not selected consent
  When the user clicks "Save and continue"
  Then an Error Summary appears at the top [Error Summary - DHCW Design System V2]
  And the message reads: "You must confirm patient consent"
  And the "Patient consent" field is highlighted in error state
  And submission is prevented

Scenario 5b: Error when POCT name not selected
  Given the user has provided consent
  And the POCT name dropdown shows "Please select"
  When the user clicks "Save and continue"
  Then the Error Summary displays: "You must select a Point of Care Test"
  And submission is prevented

Scenario 5c: Batch number validation
  Given the user enters invalid characters in batch number
  When the user clicks "Save and continue"
  Then an in-line error message appears: "Batch number must be alphanumeric"
  And submission is prevented

Scenario 5d: Expiry date validation
  Given the user enters an invalid month "13"
  Then an in-line error appears: "Month must be between 1 and 12"
  And when the user enters expiry date in the past
  Then an error message appears: "Expiry date must be in the future"

Scenario 5e: Test result must be selected
  Given all other fields are complete
  And test result is not selected
  When the user clicks "Save and continue"
  Then the Error Summary displays: "You must select a test result"

Scenario 5f: Conditional validation for reason not given
  Given the user selects "No" for "Test result given to patient"
  And does not enter a reason
  When the user clicks "Save and continue"
  Then an error appears: "You must provide a reason if result was not given"
  And submission is prevented

Scenario 5g: All fields valid - successful submission
  Given all mandatory fields are correctly populated
  When the user clicks "Save and continue"
  Then no validation errors occur
  And the form submits successfully
```

---

### Scenario 6: Accessibility

**Title:** POCT recording page meets WCAG 2.1 AA accessibility standards

```gherkin
Scenario 6a: Keyboard navigation through form
  Given the page is rendered
  When a user navigates using Tab key only
  Then the tab order is logical:
    1. Patient consent radios
    2. POCT name dropdown
    3. Batch number field
    4. Expiry date fields
    5. Test result radios
    6. Result given radios
    7. Reason not given textarea (if visible)
    8. POCT notes textarea
    9. Navigation buttons
  And each element has a visible focus indicator
  And Arrow keys navigate radio options
  And Space bar selects radio/checkbox options
  And Enter key submits form

Scenario 6b: Screen reader accessibility
  Given the page is rendered
  When a screen reader user navigates
  Then the screen reader announces:
    - Page title: "Clinical Conditions Management - Point of Care Test"
    - Headings with level (H1, H2)
    - Form labels for all fields
    - Mandatory field indicators
    - Radio/checkbox groups with grouping semantics
    - Error messages with role="alert" when they appear
    - Form status updates via aria-live

Scenario 6c: Color contrast requirements
  Given the page elements are rendered
  Then all text meets WCAG AA contrast minimums:
    - Normal text: 4.5:1 minimum
    - Large text: 3:1 minimum
  And error messages use color + icon + text (not color alone)
  And disabled controls are not distinguished by color alone

Scenario 6d: Form labels and field associations
  Given the form fields are rendered
  Then each field has:
    - Associated label element (via for/id)
    - Clear, descriptive label text
    - aria-required="true" on mandatory fields
    - aria-invalid="true" on error fields
    - aria-describedby linking to error messages
  And radio/checkbox groups use:
    - Semantic fieldset element
    - Legend element with group description
    - Individual labels for each option

Scenario 6e: Error message accessibility
  Given a validation error occurs
  When the Error Summary appears
  Then it has:
    - role="alert" (announces to screen readers)
    - aria-live="assertive" (interrupts screen reader)
    - Links to error fields via aria-describedby
    - Specific, actionable error text
  And focus can be moved to erroneous fields for correction

Scenario 6f: Responsive and mobile accessibility
  Given the page displays on mobile devices (320px+)
  Then the layout:
    - Uses single-column layout (no horizontal scroll)
    - Maintains 44px minimum touch target size
    - Keeps text size at least 12px at 100% zoom
    - Maintains field labels and associations
    - Properly shows/hides conditional fields

Scenario 6g: Zoom and text scaling support
  Given the user zooms to 200% or increases text size
  Then the page:
    - Remains functional without horizontal scrolling
    - Maintains all form field associations
    - Preserves focus indicators and error states
    - Keeps buttons and links accessible
```

---

## Definition of Done

- [ ] All 6 acceptance criteria scenarios passed
- [ ] Page layout matches design screenshot
- [ ] All DHCW Design System V2 components used correctly
- [ ] Keyboard navigation fully tested (Tab, Arrow, Space, Enter)
- [ ] Screen reader testing passed (NVDA, JAWS, VoiceOver)
- [ ] WCAG 2.1 AA compliance verified
- [ ] Color contrast verified (4.5:1 minimum)
- [ ] Touch targets 44px minimum on mobile
- [ ] Responsive design tested (mobile, tablet, desktop)
- [ ] Form validation tested (all error scenarios)
- [ ] Conditional field visibility working correctly
- [ ] Backend form submission API working
- [ ] Data persistence on navigation tested
- [ ] Unit and integration tests written
- [ ] Peer code review approved
- [ ] Performance verified (<2s load time)
- [ ] Browser compatibility confirmed
- [ ] Documentation updated

---

## Notes

**Clinical Governance:**
- Batch number and expiry date required for test kit traceability and safety recalls
- Patient consent documentation required for legal/ethical compliance
- Test result documentation supports clinical decision-making
- Reason for not giving result documents communication decisions

**POCT Types:**
- Strep A (Rapid Antigen Test)
- COVID-19 (Lateral Flow Device)
- FLU (Rapid Antigen Test)
- Other approved POCTs per service specification

**Components (DHCW Design System V2):**
- Radios
- Select
- Text Input
- Date Input
- Textarea
- Buttons
- Action Link
- Error Summary
- Inset Text

**Design Reference:**
DHCW Design System V2: https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2

✅ **Story Complete**
