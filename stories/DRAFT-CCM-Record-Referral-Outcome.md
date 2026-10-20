# CCM - Record Referral Outcome

## Story Type: UI

---

## Description

### Story Card

**As a** pharmacy user  
**I want to** record the details of a patient referral during a CCM consultation  
**So that** the referral outcome, timescale, and supporting information are documented in the consultation record

### Context

This page is part of the Clinical Conditions Management (CCM) consultation pathway, appearing after a user selects "Refer patient" on the **Outcome Selection page** (603150).

The user must record:
1. **Referred to:** Which service/provider the patient is referred to (mandatory)
2. **Timescale for referral:** How urgently the referral should be actioned (mandatory)
3. **Referral information:** Why the patient has been referred (mandatory, max 2000 characters)
4. **Advice to patient:** Guidance or instructions given to the patient (optional, max 2000 characters)

This information is stored in the consultation model and carries forward through the CCM workflow. The page also displays patient context (name, NHS number, DOB, gender, adverse reactions) for reference during referral documentation.

### Out of Scope

- Editing or viewing referral history from previous encounters
- Tracking referral status (e.g., "referral accepted", "referral rejected", "referral completed")
- Automatic pre-population of referral options based on patient conditions
- Bulk referrals or batch processing multiple patients
- Referral form generation, PDF export, or printing
- Integration with external referral systems or NHS e-Referral Service
- Displaying specialist availability or automated appointment booking
- Canceling or modifying referrals after submission

### Related Stories

- **603150: CCM Outcome Selection (Limited Options)** — relates to: This page receives the "Refer patient" selection from the Outcome Selection page; user navigates here after selecting that outcome

### Dependent Stories

- **CCM Sensitive Health Information (Story ID TBD)** — blocks: User cannot proceed to the next step in the CCM workflow until referral outcome is recorded and saved

### Captions

**Journey step:** Clinical Conditions Management  
**Page caption:** Record Referral Outcome

---

## Design Reference

**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Components used:** See [`COMPONENTS.md`](../COMPONENTS.md)

| Component | DHCW Reference | Usage |
|-----------|---|---|
| Page Heading | [Heading](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | "Record Referral Outcome" |
| Summary List | [Summary List](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-77) | Patient context (name, NHS number, DOB, gender, adverse reactions) |
| Radio Button Group | [Radios](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6058-4625) | "Referred to" (6 options: GP, Dentist, Optometrist, Out of Hours, A&E, Other) |
| Radio Button Group | [Radios](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6058-4625) | "Timescale for referral" (5 options: Within 24h, Within 48h, Within 7 days, Within 14 days, Within 28 days) |
| Textarea | [Textarea](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6218-4824) | "Referral information: Why have you referred this patient?" (mandatory, max 2000 chars) |
| Textarea | [Textarea](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6218-4824) | "Advice to patient" (optional, max 2000 chars) |
| Character Counter | (Part of Textarea) | Dynamic counter with aria-live="polite" |
| Error Summary | [Error Summary](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4585) | Displays validation errors at top of page with role="alert" |
| Button (Secondary) | [Button](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475) | "Previous" — returns to Outcome Selection |
| Button (Primary) | [Button](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475) | "Save and continue" — submits referral and navigates to next page |
| Action Link | [Action Link](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9493) | "Cancel and exit" — exits consultation without saving |

**Error messaging standard:** [`standards/UI-ERROR-MESSAGING.md`](../standards/UI-ERROR-MESSAGING.md)

---

## Acceptance Criteria

**Total Scenarios: 6**

### Page Structure & Content

**Scenario 1: Page loads with all referral form fields and patient context displayed**

```gherkin
Given the user has selected "Refer patient" on the Outcome Selection page #603150
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

**Scenario 3: User selects referral options, enters referral information, and form data persists across navigation**

```gherkin
Given the user is on the Record Referral Outcome page
When the user:
  - Selects an option in the "Referred to" radio button group
  - Selects an option in the "Timescale for referral" radio button group
  - Enters text in the "Referral information" textarea
  - Enters text in the "Advice to patient (optional)" textarea

Then each selection is immediately visible on the page with visual feedback (filled radio buttons)
And the character counters update in real-time showing:
  - "You have X characters remaining" for Referral information
  - "You have X characters remaining" for Advice to patient
And the textarea text wraps automatically and is scrollable if content exceeds visible area
And when the user navigates to another patient tab and returns to this page
Then all entered data is displayed and preserved
And the consultation model retains this referral data
```

### Navigation

**Scenario 4: User navigates between pages using Previous, Continue, and Cancel and exit buttons**

```gherkin
Given the user is on the Record Referral Outcome page

When the user clicks "Previous" button after entering referral data
Then the user is navigated back to the Outcome Selection page #603150
And the "Refer patient" outcome selection is still selected on that page
And the consultation model retains the referral data entered on this page
And if the user navigates forward again, all previously entered data is displayed

When the user clicks "Continue" with all mandatory fields completed:
  - A "Referred to" option selected
  - A "Timescale for referral" option selected
  - "Referral information" textarea contains text
Then the form is submitted
And all referral data is persisted in the consultation model
And the user navigates to the next page: "CCM Sensitive Health Information" #603156
And the referral outcome, timescale, information, and advice are accessible to the next page

When the user clicks "Cancel and exit" link after entering data
Then the user is navigated to the Patient overview page
And no data entered on this page is persisted
And no data is saved
```

### Validation

**Scenario 5: User attempts to submit form with missing or invalid data - validation errors are displayed with proper messaging and focus management**

```gherkin
Given the user is on the Record Referral Outcome page
When the user clicks "Continue" without:
  - Selecting a "Referred to" option, OR
  - Selecting a "Timescale for referral" option, OR
  - Entering text in "Referral information" field

Then an error summary is displayed at the top of the page with:
  - List of all validation errors:
    * "Select who the patient is referred to"
    * "Select timescale for referral"
    * "Enter why you have referred this patient"

And inline error messages appear near each failed field:
  - Near "Referred to" radio button group: "Select who the patient is referred to"
  - Near "Timescale for referral" radio button group: "Select timescale for referral"
  - Near "Referral information" textarea: "Enter why you have referred this patient"

And each inline error is styled with:
  - Red text color (DHCW error color)
  - Warning icon or error indicator

And the error summary box is focused (focus set to error summary heading)
And the user remains on the current page (no navigation)
And no data is persisted in the consultation model

When the text reaches more than 2000 characters in "Referral information" or "Advice to patient" textarea
Then the user shall not be permitted to enter additional characters
And a character counter shall display "You have 0 characters remaining"

And the errors remain visible until the user corrects the invalid fields
```

### Accessibility

**Scenario 6: Page is fully accessible and compliant with WCAG 2.1 AA standards**

```gherkin
Given the page is rendered
When a user navigates using keyboard only
Then all interactive elements are keyboard accessible and focusable
And the tab order is logical and follows: 
  Previous button → Referred to radio button group (6 options) → Timescale radio button group (5 options) → Referral information textarea → Advice to patient textarea → Save and continue button → Cancel and exit link

And screen reader announces:
  - Page heading with proper heading level
  - "Clinical Conditions Management" journey step label
  - Each radio button option with role="radio" and aria-checked state
  - "Referred to" with aria-label or associated fieldset
  - "Timescale for referral" with aria-label or associated fieldset
  - Character counter for each textarea with aria-live="polite"
  - Error summary with role="alert" when validation errors occur
  - Button labels and link purposes

And color contrast ratio is at least 4.5:1 for all text
And focus indicators are visible and clear on all focusable elements
And error messages in red have accompanying text (not relying on color alone)
And all form labels are properly associated with input fields using <label> or aria-label
And the page is functional when zoomed to 200%
And the textarea can be resized and scrolled if content exceeds visible area
```

---

## Definition of Done

- [ ] All acceptance criteria met and validated by QA
- [ ] Code peer reviewed and approved
- [ ] Unit tests written (80%+ coverage)
- [ ] Integration tests for form submission and validation
- [ ] Accessibility testing completed (WCAG 2.1 AA — keyboard, screen reader, contrast)
- [ ] UI matches DHCW Design System V2 components exactly
- [ ] Character counter updates in real-time with aria-live announcements
- [ ] Error summary displays with proper focus management
- [ ] Inline errors display near each failed field
- [ ] Form data persists across patient tab navigation
- [ ] "Previous" button returns to Outcome Selection with consultation state retained
- [ ] "Cancel and exit" does not persist any data
- [ ] Browser compatibility verified (Chrome, Firefox, Safari, Edge)
- [ ] Mobile responsiveness tested (if applicable)
- [ ] API integration validates and returns errors in expected format
- [ ] Documentation updated (if applicable)
- [ ] Ready for UAT

---

## Notes

**Design Reference:**
- Figma frame: CCM Record Referral Outcome — Full workflow
- Design link: [Choose Pharmacy Current Re-Designs](https://www.figma.com/design/jYWnYXhtYtjjbaYkq5xSZY/Choose-Pharmacy-Current--Re-Designs?node-id=1582-15674)

**Related Documentation:**
- [API-ERROR-HANDLING.md](../standards/API-ERROR-HANDLING.md) — Server-side validation response structure
- [UI-ERROR-MESSAGING.md](../standards/UI-ERROR-MESSAGING.md) — UI error message display standards
- [COMPONENTS.md](../COMPONENTS.md) — DHCW Design System component reference

**Implementation Notes:**
- Character counter behavior: Updates in real-time as user types using `aria-live="polite"`
- Data persistence: Form data is persisted on "Save and continue" submission only (not on field blur)
- Consultation state: When user navigates to other tabs and returns, form data remains (managed by consultation model)
- Error focus: Programmatic focus move to error summary using `focus()` method; error summary heading should be focusable
- Textarea: Supports user resizing and scrolling; character limit of 2000 enforced at form submission (client-side blocking or server-side validation)
- Referral options: 6 options in "Referred to" (GP, Dentist, Optometrist, Out of Hours, A&E, Other)
- Timescale options: 5 options (Within 24 hours, Within 48 hours, Within 7 days, Within 14 days, Within 28 days)

**Known Considerations:**
- The "Other" option in "Referred to" may require additional context or free-text field in future iterations (not in scope for this story)
- Patient adverse reactions display is for context only; no interaction required
- Next page dependency (Sensitive Health Information) will receive referral data as input once story is created
