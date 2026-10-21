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

**Total Scenarios: 11**

### Page Structure & Content

**Scenario 1: Page loads with referral form fields and patient context displayed**

```
<span style="color: #0066cc">Given</span> the user has selected <span style="color: #cc0000">"Refer patient"</span> on the Outcome Selection page (#603150)
<span style="color: #0066cc">When</span> the page loads
<span style="color: #0066cc">Then</span> the following elements are displayed:
  - High level caption: <span style="color: #cc0000">"Clinical Conditions Management"</span>
  - Page heading: <span style="color: #cc0000">"Record Referral Outcome"</span>
  - Radio Button Group labeled <span style="color: #cc0000">"Referred to"</span> with 6 options: GP, Dentist, Optometrist, Out of Hours, A&E, Other
  - Radio Button Group labeled <span style="color: #cc0000">"Timescale for referral"</span> with 5 options: Within 24 hours, Within 48 hours, Within 7 days, Within 14 days, Within 28 days
  - Textarea labeled <span style="color: #cc0000">"Referral information: Why have you referred this patient?"</span> with character counter showing <span style="color: #cc0000">"You have 2000 characters remaining"</span>
  - Textarea labeled <span style="color: #cc0000">"Advice to patient: Advice given to patient"</span> (optional) with character counter showing <span style="color: #cc0000">"You have 2000 characters remaining"</span>
  - Secondary button labeled <span style="color: #cc0000">"Previous"</span>
  - Primary button labeled <span style="color: #cc0000">"Continue"</span>
  - Action link labeled <span style="color: #cc0000">"Cancel and exit"</span>
<span style="color: #0066cc">And</span> the form layout uses maximum section width with equal margins
```

### Default State

**Scenario 2: No selections or entries are pre-populated on initial page load**

```
<span style="color: #0066cc">Given</span> the user navigates to the Record Referral Outcome page
<span style="color: #0066cc">When</span> the page loads for the first time
<span style="color: #0066cc">Then</span> no radio button option is selected in the <span style="color: #cc0000">"Referred to"</span> group
<span style="color: #0066cc">And</span> no radio button option is selected in the <span style="color: #cc0000">"Timescale for referral"</span> group
<span style="color: #0066cc">And</span> both Textarea fields are empty
<span style="color: #0066cc">And</span> the <span style="color: #cc0000">"Continue"</span> button is enabled and clickable
<span style="color: #0066cc">And</span> the form is ready for user input
```

### User Interaction

**Scenario 3: User selects referral destination option**

```
<span style="color: #0066cc">Given</span> the user is on the Record Referral Outcome page
<span style="color: #0066cc">When</span> the user clicks a <span style="color: #cc0000">"Referred to"</span> radio button option (e.g., GP)
<span style="color: #0066cc">Then</span> the selected option is immediately visible (radio button filled)
<span style="color: #0066cc">And</span> any previously selected option in the same group is deselected
<span style="color: #0066cc">And</span> the selection is visually highlighted with feedback
```

**Scenario 4: User selects referral timescale option**

```
<span style="color: #0066cc">Given</span> the user is on the Record Referral Outcome page
<span style="color: #0066cc">When</span> the user clicks a <span style="color: #cc0000">"Timescale for referral"</span> radio button option (e.g., Within 24 hours)
<span style="color: #0066cc">Then</span> the selected option is immediately visible (radio button filled)
<span style="color: #0066cc">And</span> any previously selected option in the same group is deselected
<span style="color: #0066cc">And</span> the selection is visually highlighted with feedback
```

**Scenario 5: User enters referral information and advice to patient**

```
<span style="color: #0066cc">Given</span> the user is on the Record Referral Outcome page
<span style="color: #0066cc">When</span> the user enters text in the <span style="color: #cc0000">"Referral information"</span> textarea
<span style="color: #0066cc">Then</span> the text is recorded as the user types
<span style="color: #0066cc">And</span> the character counter updates in real-time showing: <span style="color: #cc0000">"You have X characters remaining"</span>
<span style="color: #0066cc">And</span> the textarea text wraps automatically and scrolls if content exceeds visible area

<span style="color: #0066cc">When</span> the user enters text in the <span style="color: #cc0000">"Advice to patient"</span> textarea (optional)
<span style="color: #0066cc">Then</span> the text is recorded as the user types
<span style="color: #0066cc">And</span> the character counter updates in real-time showing: <span style="color: #cc0000">"You have X characters remaining"</span>
```

**Scenario 6: Form data persists when user navigates to another patient tab and returns**

```
<span style="color: #0066cc">Given</span> the user has entered referral information and selected options on this page
<span style="color: #0066cc">When</span> the user navigates to another patient tab
<span style="color: #0066cc">And</span> subsequently returns to this consultation within the same patient encounter
<span style="color: #0066cc">Then</span> all previously entered selections and text remain displayed:
  - <span style="color: #cc0000">"Referred to"</span> option still selected
  - <span style="color: #cc0000">"Timescale for referral"</span> option still selected
  - <span style="color: #cc0000">"Referral information"</span> textarea still contains the entered text
  - <span style="color: #cc0000">"Advice to patient"</span> textarea still contains the entered text
<span style="color: #0066cc">And</span> the character counters display the correct remaining character counts
<span style="color: #0066cc">And</span> the consultation model retains all stored referral data
```

### Navigation

**Scenario 7: User completes mandatory fields and clicks Continue - data stored and navigates to next page**

```
<span style="color: #0066cc">Given</span> the user is on the Record Referral Outcome page
<span style="color: #0066cc">And</span> has completed all mandatory fields:
  - Selected a <span style="color: #cc0000">"Referred to"</span> option
  - Selected a <span style="color: #cc0000">"Timescale for referral"</span> option
  - Entered text in <span style="color: #cc0000">"Referral information"</span> textarea
<span style="color: #0066cc">When</span> the user clicks the Continue button
<span style="color: #0066cc">Then</span> the form is submitted
<span style="color: #0066cc">And</span> all referral data is persisted in the consultation model:
  - Referred to (selected option)
  - Timescale for referral (selected option)
  - Referral information (textarea text)
  - Advice to patient (textarea text, or empty if not filled)
<span style="color: #0066cc">And</span> the user navigates to the next page: <span style="color: #cc0000">"CCM Sensitive Health Information"</span> (#603156)
<span style="color: #0066cc">And</span> the referral data is accessible to the next page
<span style="color: #0066cc">And</span> the navigation completes successfully
```

**Scenario 8: User clicks Previous button - returns to Outcome Selection page with data retained**

```
<span style="color: #0066cc">Given</span> the user is on the Record Referral Outcome page
<span style="color: #0066cc">And</span> has entered referral information
<span style="color: #0066cc">When</span> the user clicks the Previous button
<span style="color: #0066cc">Then</span> the user is navigated back to the Outcome Selection page (#603150)
<span style="color: #0066cc">And</span> the <span style="color: #cc0000">"Refer patient"</span> outcome selection is still selected on that page
<span style="color: #0066cc">And</span> the consultation model retains the referral data entered on this page
<span style="color: #0066cc">And</span> when the user navigates forward again to this page
<span style="color: #0066cc">Then</span> all previously entered data is displayed
```

**Scenario 9: User clicks Cancel and exit link - exits consultation without saving**

```
<span style="color: #0066cc">Given</span> the user is on the Record Referral Outcome page
<span style="color: #0066cc">And</span> has entered some referral information (but not yet submitted)
<span style="color: #0066cc">When</span> the user clicks the <span style="color: #cc0000">"Cancel and exit"</span> link
<span style="color: #0066cc">Then</span> the user is navigated to the Patient overview page
<span style="color: #0066cc">And</span> no data entered on this page is persisted in the consultation model
<span style="color: #0066cc">And</span> the consultation is not marked as saved
```

### Validation

**Scenario 10: User attempts to continue without completing mandatory fields - validation errors display with focus management**

```
<span style="color: #0066cc">Given</span> the user is on the Record Referral Outcome page
<span style="color: #0066cc">And</span> has not completed one or more mandatory fields
<span style="color: #0066cc">When</span> the user clicks the Continue button
<span style="color: #0066cc">Then</span> an error summary is displayed at the top of the page with:
  - role=<span style="color: #cc0000">"alert"</span> for immediate screen reader announcement
  - aria-live=<span style="color: #cc0000">"polite"</span> for dynamic updates
  - List of all validation errors that occurred:
    * <span style="color: #cc0000">"Select referred to"</span>
    * <span style="color: #cc0000">"Select timescale for referral"</span>
    * <span style="color: #cc0000">"Enter why you have referred this patient"</span>

<span style="color: #0066cc">And</span> inline error messages appear near each failed field:
  - Near <span style="color: #cc0000">"Referred to"</span> radio button group: <span style="color: #cc0000">"Select referred to"</span>
  - Near <span style="color: #cc0000">"Timescale for referral"</span> radio button group: <span style="color: #cc0000">"Select timescale for referral"</span>
  - Near <span style="color: #cc0000">"Referral information"</span> textarea: <span style="color: #cc0000">"Enter why you have referred this patient"</span>

<span style="color: #0066cc">And</span> the error summary is focusable and keyboard accessible
<span style="color: #0066cc">And</span> focus is automatically moved to the error summary at the top of the page
<span style="color: #0066cc">And</span> no navigation occurs
<span style="color: #0066cc">And</span> the user remains on the current page
<span style="color: #0066cc">And</span> no data is persisted in the consultation model
<span style="color: #0066cc">And</span> the error remains visible until the user makes valid selections
```

**Scenario 11: User enters text exceeding 2000-character limit - character limit is enforced**

```
<span style="color: #0066cc">Given</span> the user is on the Record Referral Outcome page
<span style="color: #0066cc">When</span> the user enters text in either <span style="color: #cc0000">"Referral information"</span> or <span style="color: #cc0000">"Advice to patient"</span> textarea
<span style="color: #0066cc">And</span> the text approaches or exceeds 2000 characters
<span style="color: #0066cc">Then</span> the character counter updates in real-time showing: <span style="color: #cc0000">"You have X characters remaining"</span>

<span style="color: #0066cc">When</span> the user attempts to enter additional characters beyond 2000
<span style="color: #0066cc">Then</span> the user is prevented from entering additional characters
<span style="color: #0066cc">And</span> the character counter displays: <span style="color: #cc0000">"You have 0 characters remaining"</span>
<span style="color: #0066cc">And</span> if character limit is exceeded on submission attempt:
  - An error summary displays at the top of page with role=<span style="color: #cc0000">"alert"</span>
  - Inline error message displays near the field
  - Focus moves to the error summary
  - No data is persisted
<span style="color: #0066cc">And</span> the error remains visible until the user corrects the field
```

### Accessibility

**Scenario 12: Page meets WCAG 2.1 AA accessibility compliance standards**

```
<span style="color: #0066cc">Given</span> the Record Referral Outcome page is rendered
<span style="color: #0066cc">When</span> a user navigates using keyboard and screen reader
<span style="color: #0066cc">Then</span> all interactive elements are focusable via Tab key
<span style="color: #0066cc">And</span> the tab order is logical and follows:
  Previous → <span style="color: #cc0000">"Referred to"</span> radio buttons (6 options) → <span style="color: #cc0000">"Timescale for referral"</span> radio buttons (5 options) → <span style="color: #cc0000">"Referral information"</span> textarea → <span style="color: #cc0000">"Advice to patient"</span> textarea → Continue → <span style="color: #cc0000">"Cancel and exit"</span>

<span style="color: #0066cc">And</span> the page heading is announced as: <span style="color: #cc0000">"Record Referral Outcome"</span> (heading level 2)
<span style="color: #0066cc">And</span> the journey caption is announced as: <span style="color: #cc0000">"Clinical Conditions Management"</span> (page context)
<span style="color: #0066cc">And</span> the radio button group labels are announced as: <span style="color: #cc0000">"Referred to, group of radio buttons"</span> and <span style="color: #cc0000">"Timescale for referral, group of radio buttons"</span>
<span style="color: #0066cc">And</span> each radio option is announced with its label and state: <span style="color: #cc0000">"GP, radio button, not checked"</span> (or <span style="color: #cc0000">"checked"</span>)
<span style="color: #0066cc">And</span> the textarea labels are associated with their fields
<span style="color: #0066cc">And</span> character counters are announced with aria-live=<span style="color: #cc0000">"polite"</span>: <span style="color: #cc0000">"You have X characters remaining"</span>
<span style="color: #0066cc">And</span> error summary is announced with role=<span style="color: #cc0000">"alert"</span> when validation errors occur
<span style="color: #0066cc">And</span> the Previous button is announced as: <span style="color: #cc0000">"Previous, button"</span>
<span style="color: #0066cc">And</span> the Continue button is announced as: <span style="color: #cc0000">"Continue, button"</span>
<span style="color: #0066cc">And</span> the Cancel and exit link is announced as: <span style="color: #cc0000">"Cancel and exit, link"</span>

<span style="color: #0066cc">And</span> color contrast ratio is at least 4.5:1 for all text (including buttons, labels, error messages)
<span style="color: #0066cc">And</span> focus indicators are visible on all interactive elements
<span style="color: #0066cc">And</span> all form labels are properly associated with inputs using <label> or aria-labelledby
<span style="color: #0066cc">And</span> the page is functional when zoomed to 200%
<span style="color: #0066cc">And</span> textareas support user resizing and scrolling
<span style="color: #0066cc">And</span> error messages are announced with aria-live for screen reader users
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
