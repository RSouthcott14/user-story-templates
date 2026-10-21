# UI: CCM Record Referral Outcome

## Story Type: UI | Dependent on: #603150 (Outcome Selection)
## Tags: `AI Generated` | `Review (BR)` | `CCM` | `Referral` | `UI`

---

## Description

### Story Card
**As a** pharmacy user  
**I want to** record the details of a patient referral during a CCM consultation  
**So that** the referral outcome, timescale, and supporting information are documented in the consultation record

### Context
This story is part of the **Clinical Conditions Management (CCM) consultation pathway** when the outcome is to **Refer** the patient to another service or provider.

After the user selects "Refer patient" as the outcome (via #603150), they are presented with a Referral page containing:
- Radio button group to select **Referred to** (6 options: GP, Dentist, Optometrist, Out of Hours, A&E, Other)
- Radio button group to select **Timescale for referral** (5 options: Within 24 hours, Within 48 hours, Within 7 days, Within 14 days, Within 28 days)
- Mandatory **Referral information** textarea (max 2000 characters) — Why the patient has been referred
- Optional **Advice to patient** textarea (max 2000 characters) — Guidance given to the patient
- Character counters for both textareas with real-time updates
- Navigation buttons (Previous, Continue, Cancel and exit)

The user records the referral details and can optionally add advice before continuing to the next step in the consultation workflow.

**Related Journey Step**: CCM Consultation → Outcome Decision → Record Referral → Sensitive Health Information

### Captions
**Journey step:** Clinical Conditions Management  
**Page caption:** Outcome: Refer

### Out of Scope
- Editing or viewing referral history from previous encounters
- Tracking referral status (e.g., "referral accepted", "referral rejected")
- Automatic pre-population of referral options based on patient conditions
- Bulk referrals or batch processing multiple patients
- Referral form generation, PDF export, or printing
- Integration with external referral systems or NHS e-Referral Service
- Displaying specialist availability or automated appointment booking
- Canceling or modifying referrals after submission

### Related/Dependent Stories
- **#603150**: CCM Outcome Selection (Upstream) — *user selects "Refer patient" to reach this story*
- **#603156**: CCM Sensitive Health Information (Downstream) — *next page after referral submission*
- Related Feature: Clinical Conditions Management (Referral Pathway)

---

## Design Reference

**Design System:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Figma Frame:** [CCM Record Referral Outcome](https://www.figma.com/design/jYWnYXhtYtjjbaYkq5xSZY/Choose-Pharmacy-Current--Re-Designs?node-id=1582-15674)

**Component Guidance:** [COMPONENTS.md](../COMPONENTS.md)

**Error Messaging Standard:** [standards/UI-ERROR-MESSAGING.md](../standards/UI-ERROR-MESSAGING.md)

**Textarea Standard:** [standards/UI-TEXTAREA-SPECIFICATIONS.md](../standards/UI-TEXTAREA-SPECIFICATIONS.md)

### Components Used

- **Radio Button Group** — Referred to (6 options) and Timescale for referral (5 options)
  [Figma: Radio Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-79)

- **Textarea** — Referral information (mandatory, 2000 character limit) and Advice to patient (optional, 2000 character limit)
  [Figma: Textarea](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6218-4824)

- **Character Counter** — Real-time character count with aria-live="polite"
  [Figma: Character Counter](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

- **Error Summary** — Validation error display at top of page
  [Figma: Error Summary](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4585)

- **Buttons** — Previous (secondary), Continue (primary)
  [Figma: Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475)

- **Action Link** — Cancel and exit
  [Figma: Action Link](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9493)

---

## Acceptance Criteria

**Total Scenarios: 12**

### Page Structure & Content

**Scenario 1: Page loads with referral form fields and patient context displayed**

```gherkin
Given the user has selected "Refer patient" on the Outcome Selection page (#603150)
When the page loads
Then the following elements are displayed:
  - High level caption: "Clinical Conditions Management"
  - Page heading: "Record Referral Outcome"
  - Radio Button Group labeled "Referred to" with 6 options: GP, Dentist, Optometrist, Out of Hours, A&E, Other
  - Radio Button Group labeled "Timescale for referral" with 5 options: Within 24 hours, Within 48 hours, Within 7 days, Within 14 days, Within 28 days
  - Textarea labeled "Referral information: Why have you referred this patient?" with character counter showing "You have 2000 characters remaining"
  - Textarea labeled "Advice to patient: Advice given to patient" (optional) with character counter showing "You have 2000 characters remaining"
  - Secondary button labeled "Previous"
  - Primary button labeled "Continue"
  - Action link labeled "Cancel and exit"
And the form layout uses maximum section width with equal margins
```

### Default State

**Scenario 2: No selections or entries are pre-populated on initial page load**

```gherkin
Given the user navigates to the Record Referral Outcome page
When the page loads for the first time
Then no radio button option is selected in the "Referred to" group
And no radio button option is selected in the "Timescale for referral" group
And both Textarea fields are empty
And the "Continue" button is enabled and clickable
And the form is ready for user input
```

### User Interaction

**Scenario 3: User selects referral destination option**

```gherkin
Given the user is on the Record Referral Outcome page
When the user clicks a "Referred to" radio button option (e.g., GP)
Then the selected option is immediately visible (radio button filled)
And any previously selected option in the same group is deselected
And the selection is visually highlighted with feedback
```

**Scenario 4: User selects referral timescale option**

```gherkin
Given the user is on the Record Referral Outcome page
When the user clicks a "Timescale for referral" radio button option (e.g., Within 24 hours)
Then the selected option is immediately visible (radio button filled)
And any previously selected option in the same group is deselected
And the selection is visually highlighted with feedback
```

**Scenario 5: User enters referral information and advice to patient**

```gherkin
Given the user is on the Record Referral Outcome page
When the user enters text in the "Referral information" textarea
Then the text is recorded as the user types
And the character counter updates in real-time showing: "You have X characters remaining"
And the textarea text wraps automatically and scrolls if content exceeds visible area

When the user enters text in the "Advice to patient" textarea (optional)
Then the text is recorded as the user types
And the character counter updates in real-time showing: "You have X characters remaining"
```

**Scenario 6: Form data persists when user navigates to another patient tab and returns**

```gherkin
Given the user has entered referral information and selected options on this page
When the user navigates to another patient tab
And subsequently returns to this consultation within the same patient encounter
Then all previously entered selections and text remain displayed:
  - "Referred to" option still selected
  - "Timescale for referral" option still selected
  - "Referral information" textarea still contains the entered text
  - "Advice to patient" textarea still contains the entered text
And the character counters display the correct remaining character counts
And the consultation model retains all stored referral data
```

### Navigation

**Scenario 7: User completes mandatory fields and clicks Continue - data stored and navigates to next page**

```gherkin
Given the user is on the Record Referral Outcome page
And has completed all mandatory fields:
  - Selected a "Referred to" option
  - Selected a "Timescale for referral" option
  - Entered text in "Referral information" textarea
When the user clicks the Continue button
Then the form is submitted
And all referral data is persisted in the consultation model:
  - Referred to (selected option)
  - Timescale for referral (selected option)
  - Referral information (textarea text)
  - Advice to patient (textarea text, or empty if not filled)
And the user navigates to the next page: "CCM Sensitive Health Information" (#603156)
And the referral data is accessible to the next page
And the navigation completes successfully
```

**Scenario 8: User clicks Previous button - returns to Outcome Selection page with data retained**

```gherkin
Given the user is on the Record Referral Outcome page
And has entered referral information
When the user clicks the Previous button
Then the user is navigated back to the Outcome Selection page (#603150)
And the "Refer patient" outcome selection is still selected on that page
And the consultation model retains the referral data entered on this page
And when the user navigates forward again to this page
Then all previously entered data is displayed
```

**Scenario 9: User clicks Cancel and exit link - exits consultation without saving**

```gherkin
Given the user is on the Record Referral Outcome page
And has entered some referral information (but not yet submitted)
When the user clicks the "Cancel and exit" link
Then the user is navigated to the Patient overview page
And no data entered on this page is persisted in the consultation model
And the consultation is not marked as saved
```

### Validation

**Scenario 10: User attempts to continue without completing mandatory fields - validation errors display with focus management**

```gherkin
Given the user is on the Record Referral Outcome page
And has not completed one or more mandatory fields
When the user clicks the Continue button
Then an error summary is displayed at the top of the page with:
  - role="alert" for immediate screen reader announcement
  - aria-live="polite" for dynamic updates
  - List of all validation errors that occurred:
    * "Select referred to"
    * "Select timescale for referral"
    * "Enter why you have referred this patient"

And inline error messages appear near each failed field:
  - Near "Referred to" radio button group: "Select referred to"
  - Near "Timescale for referral" radio button group: "Select timescale for referral"
  - Near "Referral information" textarea: "Enter why you have referred this patient"

And the error summary is focusable and keyboard accessible
And focus is automatically moved to the error summary at the top of the page
And no navigation occurs
And the user remains on the current page
And no data is persisted in the consultation model
And the error remains visible until the user makes valid selections
```

**Scenario 11: User enters text exceeding 2000-character limit - character limit is enforced**

```gherkin
Given the user is on the Record Referral Outcome page
When the user enters text in either "Referral information" or "Advice to patient" textarea
And the text approaches or exceeds 2000 characters
Then the character counter updates in real-time showing: "You have X characters remaining"

When the user attempts to enter additional characters beyond 2000
Then the user is prevented from entering additional characters
And the character counter displays: "You have 0 characters remaining"
And if character limit is exceeded on submission attempt:
  - An error summary displays at the top of page with role="alert"
  - Inline error message displays near the field
  - Focus moves to the error summary
  - No data is persisted
And the error remains visible until the user corrects the field
```

### Accessibility

**Scenario 12: Page meets WCAG 2.1 AA accessibility compliance standards**

```gherkin
Given the Record Referral Outcome page is rendered
When a user navigates using keyboard and screen reader
Then all interactive elements are focusable via Tab key
And the tab order is logical and follows:
  Previous → "Referred to" radio buttons (6 options) → "Timescale for referral" radio buttons (5 options) → "Referral information" textarea → "Advice to patient" textarea → Continue → "Cancel and exit"

And the page heading is announced as: "Record Referral Outcome" (heading level 2)
And the journey caption is announced as: "Clinical Conditions Management" (page context)
And the radio button group labels are announced as: "Referred to, group of radio buttons" and "Timescale for referral, group of radio buttons"
And each radio option is announced with its label and state: "GP, radio button, not checked" (or "checked")
And the textarea labels are associated with their fields
And character counters are announced with aria-live="polite": "You have X characters remaining"
And error summary is announced with role="alert" when validation errors occur
And the Previous button is announced as: "Previous, button"
And the Continue button is announced as: "Continue, button"
And the Cancel and exit link is announced as: "Cancel and exit, link"

And color contrast ratio is at least 4.5:1 for all text (including buttons, labels, error messages)
And focus indicators are visible on all interactive elements
And all form labels are properly associated with inputs using <label> or aria-labelledby
And the page is functional when zoomed to 200%
And textareas support user resizing and scrolling
And error messages are announced with aria-live for screen reader users
```

---

## Definition of Done

- [ ] All acceptance criteria scenarios pass (manual or automated testing)
- [ ] Page structure matches DHCW Design System V2 components exactly
- [ ] Radio button groups display correct number of options (6 for "Referred to", 5 for "Timescale for referral")
- [ ] Textareas display with character counters showing "You have 2000 characters remaining"
- [ ] Previous and Continue buttons styled as Secondary and Primary buttons respectively
- [ ] Cancel and exit displays as Action Link
- [ ] No selections are pre-populated on initial page load
- [ ] Character counters update in real-time as user types
- [ ] Character limit of 2000 characters is enforced (user cannot enter beyond limit)
- [ ] Selected options and entered text are immediately visible with visual feedback
- [ ] Form data persists when user navigates to another patient tab and returns
- [ ] Form data persists when user clicks Previous and returns to page
- [ ] Validation error message text matches specification exactly:
  - "Select referred to"
  - "Select timescale for referral"
  - "Enter why you have referred this patient"
- [ ] Error summary displays at top of page with role="alert" and aria-live="polite"
- [ ] **Error summary focus management:** Focus automatically moves to error summary when validation error occurs
- [ ] Error summary remains focusable for keyboard users to review all errors
- [ ] Inline error messages display near each failed field
- [ ] No data is persisted when validation error occurs
- [ ] Character limit errors handled correctly with appropriate messaging
- [ ] Continue button submission stores all referral data in consultation model:
  - Referred to option
  - Timescale for referral option
  - Referral information text
  - Advice to patient text (or empty if not filled)
- [ ] Navigation to next page (#603156) completes successfully with referral data passed
- [ ] Previous button returns to Outcome Selection page (#603150) with state retained
- [ ] Cancel and exit navigates to Patient overview page without persisting data
- [ ] WCAG 2.1 AA accessibility compliance verified:
  - All interactive elements keyboard accessible
  - Tab order: Previous → Referred to (6 options) → Timescale (5 options) → Referral info → Advice → Continue → Cancel and exit
  - Screen reader announcements match specified text
  - Color contrast ratio at least 4.5:1
  - Focus indicators visible on all elements
  - Form labels properly associated with inputs
  - Page functional when zoomed to 200%
- [ ] Character counter aria-live="polite" announcements tested with screen reader
- [ ] Error messaging styling consistent with standards/UI-ERROR-MESSAGING.md
- [ ] Component styling verified on mobile, tablet, and desktop viewports
- [ ] Browser compatibility verified (Chrome, Firefox, Safari, Edge)
- [ ] Code review completed and approved by tech lead
- [ ] Story linked to parent feature in Azure DevOps

---

## Notes

### Related Documentation
- Feature reference: **Clinical Conditions Management (CCM) consultation pathway**
- Consultation model: **Persists referral data throughout journey lifecycle**
- Error messaging: Follows standardized UI error messaging patterns from `standards/UI-ERROR-MESSAGING.md`
- Accessibility baseline: **WCAG 2.1 AA** with DHCW Design System V2 compliance

### Design Links
- Figma component frame: **DHCW Design System V2 – Radio Buttons, Textareas, and Buttons**
- Consultation model architecture: Reference ADO feature documentation

### Implementation Notes
- Referral submission is a **required action** — Continue button validation triggers on click, not on blur
- **Data Persistence Timing:** Consultation model state is updated **when user clicks Continue** (after all selections and text entry, before navigation to next page) — NOT immediately on selection or field blur
  - Visual feedback on selection/entry is immediate (radio button fills, character counter updates)
  - Consultation model storage is deferred until Continue action
  - This ensures referral data is only committed when user confirms they want to proceed
- The selected referral information should be **accessible and persisted** throughout the consultation lifecycle for both forward navigation (Continue) and backward navigation (Previous)
- **Error Message Handling:**
  - Error messages follow standardized format from `standards/UI-ERROR-MESSAGING.md`
  - Error summary displays at top of page with `role="alert"` and `aria-live="polite"` for screen reader announcement
  - Inline errors display near each failed field
  - **Focus Management:** Focus automatically moves to the error summary when validation error occurs (not to the failed field)
  - Errors are cleared/hidden immediately when user corrects the invalid field
- **Character Counter Behavior:**
  - Updates in real-time as user types
  - Enforces 2000-character maximum (user cannot type beyond limit)
  - When limit reached, counter displays "You have 0 characters remaining"
  - Textarea remains scrollable if content exceeds visible area
- Both radio button groups must be equally prominent (no visual bias)
- **Consultation State Persistence:** When user is in active consultation, navigates to another patient tab, and returns to the same encounter, the consultation and form state must remain active and visible
- Consultation model must retain referral data even when user navigates backward (Previous button)

### Known Considerations
- Referral data must not be lost if user navigates backward and returns
- Consultation model must support data retention across multiple navigation cycles
- Error messaging must be clear and user-friendly without technical jargon
- Character counter must remain accurate even after page navigation and return

