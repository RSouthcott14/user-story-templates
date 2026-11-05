# UI: CCM - Review Consultation Summary and Complete Consultation (CAS & IPS)

**Story Type:** UI  
**Tags:** AI Generated, Review (BR)

---

## Description

### Story Card

**As a** pharmacy user  
**I want** to review all consultation details and mark the consultation as complete  
**So that** I can verify the entire consultation pathway was completed correctly and close the consultation session

### Context

This story defines the final step in Clinical Conditions Management (CCM) consultations for both Common Ailments Service (CAS) and Independent Pharmacy Services (IPS) pathways. After users complete outcome-specific pages (Referral, Advice, or Supply), they arrive at a Review Consultation Summary page to:

1. **View a complete summary** of all consultation data entered
2. **Verify all required information** was captured
3. **Mark the consultation as complete** by clicking a button
4. **Return to patient overview** or consultation history

The summary page displays:
- Journey context and patient information
- Conditional sections based on pathway and outcome type:
  - **CCM Referral pathway:** Referral details (destination, timescale, information, advice)
  - **CCM Advice pathway:** Advice details only
  - **CAS Supply pathway:** Medications supplied (formulary + antibiotics if applicable), dosages, backup supply status, advice
  - **IPS pathway:** Service-specific details (to be defined by IPS story)
- Action buttons: Previous (to review/edit), Complete consultation, Cancel and exit

### CAS Supply Pathway Context

For CAS consultations with Supply outcome:
- Review all formulary medications with quantities and dosages
- Review antibiotic selection (if STTT/UTI condition) with backup supply status
- Display advice given to patient
- Validate that all entered data matches consultation model

### IPS Pathway Context

For IPS consultations:
- Display service-specific information captured during consultation
- (Details to be aligned with IPS workflow and story requirements)

### Related Stories

| Story ID | Title | Type | Details |
|----------|-------|------|---------|
| 603150 | UI: CCM - Outcome Selection (Limited Options) | Related | Entry point—selects outcome path (Referral, Advice, or Supply) |
| 603161 | UI: CCM - Record Referral Outcome | Related | Referral pathway branch |
| 603162 | UI: CCM - Record Advice Outcome | Related | Advice pathway branch |
| 618128 | UI: CCM: CAS Supply Medication Quantity and Dosage Entry | Related | CAS Supply pathway branch |
| 603156 | UI: CCM - Sensitive Health Information | Related | Optional step in some pathways |

### Dependent Stories

- **IPS service detail pages:** Stories that capture IPS pathway data must complete before this story
- **Consultation model/state management:** Backend API contract for storing and retrieving complete consultation summaries

---

## Design Reference

| Component | DHCW Design System V2 | Notes |
|-----------|----------------------|-------|
| Page heading (h2) | [Headings - h2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | "Consultation summary" or "Review consultation" |
| Summary sections | [Card/Container](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | Grouped sections for each data category |
| Patient information | Display component | Read-only patient context display |
| Data fields | Read-only text/list display | No input fields—summary only |
| Secondary button | [Button - Secondary](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | "Previous" |
| Primary button | [Button - Primary](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | "Complete consultation" |
| Action link | [Link - Action](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | "Cancel and exit" |
| Error summary | [Error Summary](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | For error states (e.g., API failure, missing data) |

---

## Acceptance Criteria

**Total Scenarios: 35**

### Page Structure & Content

**Scenario 1: Review Consultation Summary page displays for CCM Referral pathway**

```gherkin
Given a consultation with "Refer patient" outcome has been completed
And all referral data has been saved (destination, timescale, information, advice)
When the user navigates to the Review Consultation Summary page
Then the page displays:
  - High level caption: "Clinical Conditions Management"
  - Page heading: "Consultation summary"
  - Patient information section (read-only): Name, NHS number, Date of birth, Age, Adverse reactions
  - Consultation outcome section displaying: "Outcome: Refer patient"
  - Referral details section with:
    * Referred to: [selected destination]
    * Timescale for referral: [selected timescale]
    * Referral information: [entered text]
    * Advice to patient: [entered text or "Not provided"]
  - Secondary button labeled "Previous"
  - Primary button labeled "Complete consultation"
  - Action link labeled "Cancel and exit"
And all data is displayed in read-only format (no edit controls on this page)
And the form layout uses maximum section width with equal margins
```

**Scenario 2: Review Consultation Summary page displays for CCM Advice pathway**

```gherkin
Given a consultation with "Advice only" outcome has been completed
And advice text has been saved
When the user navigates to the Review Consultation Summary page
Then the page displays:
  - High level caption: "Clinical Conditions Management"
  - Page heading: "Consultation summary"
  - Patient information section (read-only)
  - Consultation outcome section displaying: "Outcome: Advice"
  - Advice details section with:
    * Advice given to patient: [entered text or "Not provided"]
  - Previous, Complete consultation, and Cancel and exit buttons
And all data is displayed in read-only format
```

**Scenario 3: Review Consultation Summary page displays for CAS Supply pathway (Formulary only, Non-STTT/UTI)**

```gherkin
Given a CAS consultation for a Non-STTT/UTI condition with Supply outcome
And formulary medications have been entered (quantities and dosages)
When the user navigates to the Review Consultation Summary page
Then the page displays:
  - High level caption: "Clinical Conditions Management" and "Common Ailments Service"
  - Page heading: "Consultation summary"
  - Patient information section (read-only)
  - CAS Condition section displaying: [selected condition name]
  - Medications supplied section displaying:
    * Read-only table with columns: Medicine, Pack size, Quantity supplied, Dosage
    * Each formulary medication entered with its quantity and dosage
  - Advice to patient section: [entered advice or "Not provided"]
  - Previous, Complete consultation, and Cancel and exit buttons
And all data is displayed in read-only format
```

**Scenario 4: Review Consultation Summary page displays for CAS Supply pathway (Formulary + Antibiotic, STTT/UTI)**

```gherkin
Given a CAS consultation for a STTT/UTI condition with Supply outcome
And formulary medications and one antibiotic have been entered with quantities, dosages, and backup supply answer
When the user navigates to the Review Consultation Summary page
Then the page displays:
  - High level caption and headings as in Scenario 3
  - Medications supplied section with:
    * Formulary medications table (if any)
    * Antibiotics section with:
      - Selected antibiotic name, quantity, dosage
      - Backup supply status: "Yes" or "No"
  - Advice to patient section
  - Previous, Complete consultation, and Cancel and exit buttons
And all data is displayed in read-only format
```

**Scenario 5: Review Consultation Summary page displays for IPS pathway**

```gherkin
Given an IPS consultation has been completed
When the user navigates to the Review Consultation Summary page
Then the page displays:
  - High level caption: "Clinical Conditions Management"
  - Page heading: "Consultation summary"
  - Patient information section (read-only): Name, NHS number, Date of birth, Gender, Adverse reactions
  - Consultation details section with:
    * GPhC number
    * Date and time of consultation
    * Consultation undertaken as: "Face to face consultation at pharmacy"
    * Referred by: [source]
  - Symptoms and history section with:
    * Presenting symptoms: [entered symptoms]
    * Relevant medical, social and/or family history: [entered details or empty if optional]
  - Service and template section with:
    * Service: "Independent Prescribers Service (IPS)"
    * Template: [selected template name]
  - General clinical assessment section with:
    * Clinical assessment notes: [entered notes or "Not provided"]
    * Examination notes: [entered notes or "Not provided"]
  - Confirm working diagnosis section with:
    * Working diagnosis selected: [diagnosis name]
    * Service specification: [specification details]
  - Consultation outcome section displaying: "Supply & Refer"
  - Medication(s) section with:
    * Read-only table: Medicine, Pack size, Quantity supplied, Dosage
    * Print prescription button (enabled)
    * Advice given to patient: [entered advice or "No advice recorded"]
  - Referral information section with:
    * Referred to: [destination]
    * Timescale for referral: [timescale]
    * Notes: [referral notes]
  - Sensitive information section: Yes/No
  - Send consultation summary section: Yes/No
  - Previous button (Secondary, left aligned)
  - Complete consultation button (Primary, right aligned)
  - Cancel and exit link (Action link)
And all data is displayed in read-only format (no edit controls on this page)
And the form layout uses maximum section width with equal margins
```

### Default State

**Scenario 6: All consultation data is pre-populated on initial page load**

```gherkin
Given the user navigates to the Review Consultation Summary page
When the page loads for the first time
Then all previously entered consultation data is displayed:
  - Referral details (if referral outcome), OR
  - Advice text (if advice outcome), OR
  - Medication supplies (if CAS supply outcome), OR
  - IPS service details (if IPS outcome)
And patient information is displayed in read-only format
And the Complete consultation button is enabled and clickable
And the page is in read-only mode (no input fields or edit controls)
```

**Scenario 7: Summary data persists when user navigates back and returns**

```gherkin
Given all consultation data has been entered and displayed on the Summary page
When the user clicks Previous to return to the previous outcome page
And makes changes or reviews the data
And navigates forward again to the Review Consultation Summary page
Then all updated data is displayed with the changes reflected
And the summary is accurate to the current consultation model state
And the Complete consultation button remains enabled
```

### User Interaction

**Scenario 8: User reviews referral destination and timescale on summary**

```gherkin
Given the summary displays referral details
When viewing the summary for a Referral pathway consultation
Then the referral destination (e.g., "GP", "Dentist", "A&E") is clearly displayed
And the timescale (e.g., "Within 24 hours") is clearly displayed
And both values are readable and match the consultation model
And no edit controls appear inline
```

**Scenario 9: User reviews antibiotic and backup supply status in CAS summary**

```gherkin
Given the summary displays CAS Supply details with antibiotic (STTT/UTI pathway)
When viewing the summary
Then the selected antibiotic name, quantity, and dosage are displayed
And the backup supply status ("Yes" or "No") is clearly indicated
And only one antibiotic is displayed (mutual exclusivity confirmed)
And the display matches the consultation model exactly
```

**Scenario 10: User reviews advice text on summary**

```gherkin
Given the summary displays advice sections
When viewing advice text
Then the advice is displayed in full
And if advice exceeds one line, scrolling is available
And empty advice fields display as "Not provided" or similar placeholder
And the advice text is read-only (no textarea field)
```

**Scenario 11: User clicks Previous button to return to outcome page**

```gherkin
Given the user is on the Review Consultation Summary page
When the user clicks the "Previous" button
Then the user is navigated back to the last outcome page:
  - Referral Outcome page (if referral pathway), OR
  - Advice Outcome page (if advice pathway), OR
  - CAS Supply page (if CAS pathway), OR
  - IPS outcome page (if IPS pathway)
And all previously entered data remains in the consultation model
And when the user navigates forward again to Summary
Then all data is re-displayed and unchanged
```

**Scenario 12: User clicks Cancel and exit without completing consultation**

```gherkin
Given the user is on the Review Consultation Summary page
When the user clicks the "Cancel and exit" link
Then the user is navigated to the Patient overview page
And the consultation is NOT marked as complete
And no data is saved in consultation completion state
And the user can re-enter the consultation later to resume or complete
```

### Navigation & State Management

**Scenario 13: User completes consultation by clicking Complete consultation button**

```gherkin
Given the user is on the Review Consultation Summary page
And all required consultation data is displayed
When the user clicks the "Complete consultation" button
Then the form is submitted
And the consultation is marked as "Complete" in the consultation model
And all consultation data is finalized and locked (no further edits permitted)
And the user is navigated to a post-completion page:
  - Patient overview page, OR
  - Consultation confirmation page, OR
  - (Destination TBD in workflow specification)
And a success message or confirmation is displayed to the user
And the completion timestamp is recorded
```

**Scenario 14: Complete consultation validates all mandatory fields are present**

```gherkin
Given the user is on the Review Consultation Summary page
When the user clicks "Complete consultation"
And one or more mandatory consultation fields are empty or missing
Then the completion request is rejected
And an error summary is displayed at the top of the page with role="alert" and aria-live="polite"
And inline error messages indicate which fields are missing:
  - For Referral: "Referral destination is missing", "Timescale is missing", "Referral information is required"
  - For Advice: "Advice to patient is required" (if mandatory)
  - For CAS Supply: "No medications were supplied", "Dosage is missing for [medication name]"
And the user remains on the Summary page
And the consultation is not marked as complete
And no data is persisted
```

**Scenario 15: Complete consultation for CAS Supply validates antibiotic backup supply selection**

```gherkin
Given the summary displays a CAS Supply outcome with antibiotic selected
When the user clicks "Complete consultation"
And the backup supply question has not been answered
Then an error displays: "Backup supply status not provided for [antibiotic name]"
And the error summary appears at top of page with role="alert"
And inline error highlights the antibiotics section
And the user must return to Supply page to select backup supply option
And the consultation is not marked as complete
```

**Scenario 14: User reaches Summary page without Clinical Assessment Notes via IPS pathway**

```gherkin
Given the user is identified as an Independent Prescriber
And the user has navigated to the Review Consultation Summary page via IPS pathway
And the General clinical assessment section is displayed
And the Clinical assessment notes field has not been completed
And the Examination notes field has not been completed
When the consultation summary is displayed
Then the page shall render without validation errors
And the Clinical assessment notes field shall display: "Not provided"
And the Examination notes field shall display: "Not provided"
And the consultation shall remain editable
And the user shall be able to view all entered data in read-only format
And no error message shall be displayed at this stage
And the Complete consultation button shall be enabled
And the user can print the prescription from the Medication(s) section without completing Clinical Assessment Notes
And no data is persisted
```

**Scenario 15: User attempts to finalise consultation without Clinical Assessment Notes via IPS pathway**

```gherkin
Given the user is identified as an Independent Prescriber
And the user is on the Review Consultation Summary page
And the General clinical assessment section is displayed
And both Clinical assessment notes and Examination notes fields are empty or show "Not provided"
When the user clicks the "Complete consultation" button
Then the completion request is rejected
And a validation summary is displayed at the top of the page
And the validation summary displays:
  - "There is a problem" as the summary heading
  - Error message in red: "Enter 'Clinical assessment notes'"
And focus is moved to the validation summary with role="alert" and aria-live="polite"
And the error message is presented as a link to navigate to the General clinical assessment section
And the General clinical assessment section is visually identified with a red left border
And the Clinical assessment notes and Examination notes fields display inline warning messages:
  - "You may print a prescription before completing the 'General clinical assessment'. But you must add clinical assessment notes before selecting 'Complete consultation'"
And the consultation is not marked as complete
And no data is persisted
```

**Scenario 16: User completes Clinical Assessment Notes following Summary validation via IPS pathway**

```gherkin
Given the user is identified as an Independent Prescriber
And validation has been triggered on the Summary page due to missing Clinical Assessment Notes
And the error summary displays "There is a problem" with the message "Enter 'Clinical assessment notes'"
And the error message links to the General clinical assessment section
When the user clicks the error link or navigates back using Previous button
And enters Clinical assessment notes in the Clinical assessment notes field
And navigates forward again to the Review Consultation Summary page
And clicks "Complete consultation"
Then the validation error summary is removed
And the red left border on the General clinical assessment section is removed
And the inline warning messages are cleared
And the consultation shall be allowed to be finalised
And the consultation is marked as "Complete"
And a completion timestamp is recorded
And the user is navigated to the post-completion page
```

**Scenario 17: Independent Prescriber can print prescription before Clinical Assessment Notes are completed**

```gherkin
Given the user is identified as an Independent Prescriber
And the consultation contains medication data in the Medication(s) section
And the General clinical assessment section shows Clinical assessment notes as "No notes recorded"
And the Examination notes field shows "No notes recorded"
And validation has not been triggered (user has not attempted finalization)
When the user is viewing the Review Consultation Summary page
And the Print prescription button is displayed in the Medication(s) section
And the user clicks the "Print prescription" button
Then prescription generation and printing shall be permitted
And the deferred Clinical Assessment validation shall not prevent prescription generation or printing
And the user can print the prescription without completing Clinical Assessment Notes
And the inline information message in the General clinical assessment section reminds:
  - "You may print a prescription before completing the 'General clinical assessment'. But you must add clinical assessment notes before selecting 'Complete consultation'"
And the user can then navigate back using Previous button to complete Clinical Assessment Notes before finalizing
And no data is persisted until final consultation completion
```

**Scenario 18: Inline warning message guides Independent Prescriber on Clinical Assessment deferral**

```gherkin
Given the user is identified as an Independent Prescriber
And the user is on the Review Consultation Summary page
And the General clinical assessment section is displayed
And both Clinical assessment notes and Examination notes fields show "No notes recorded" or are empty
When the user views the General clinical assessment section
Then an inline information message is displayed with a left border indicator:
  - "You may print a prescription before completing the 'General clinical assessment'. But you must add clinical assessment notes before selecting 'Complete consultation'"
And the message is visible and readable
And the message appears above or adjacent to the Clinical assessment notes field
And when the user clicks the Print prescription button
Then the message confirms that prescription printing is permitted despite incomplete Clinical Assessment
And when the user attempts to click Complete consultation
Then the message context is linked to the validation error that appears
And the message provides clear guidance on the deferral workflow
```

### Validation & Error Handling

**Scenario 18: Summary data matches consultation model state exactly**

```gherkin
Given a consultation with multiple data entries has been completed
When viewing the Review Consultation Summary
Then all displayed data matches exactly what was entered:
  - Referral: destination, timescale, information text, advice text
  - Advice: advice text verbatim
  - CAS Supply: medication names, quantities, dosages, antibiotic backup status, advice
And no values are truncated, reformatted, or altered on display
And special characters and line breaks in text are preserved
And numeric values (quantities, dosages) display with correct precision
```

**Scenario 19: Summary correctly displays conditional sections based on pathway**

```gherkin
Given a consultation with Referral outcome
When viewing the summary
Then Referral sections are displayed
And CAS Supply sections are NOT displayed
And IPS sections are NOT displayed

Given a CAS Supply consultation for Non-STTT/UTI condition
When viewing the summary
Then Formulary medications section is displayed
And Antibiotics section is NOT displayed

Given a CAS Supply consultation for STTT/UTI condition
When viewing the summary
Then Formulary medications section (if entries exist) is displayed
And Antibiotics section is displayed with selected antibiotic and backup status
```

**Scenario 20: Patient information is current and accurate**

```gherkin
Given the Review Consultation Summary page loads
When patient information is displayed (Name, NHS number, DOB, Age, Adverse reactions)
Then all data is current and retrieved from the patient record
And if patient demographics have changed during consultation session
Then the Summary displays the current values
And data is read-only (no edit capability on Summary page)
And no data is persisted
```

**Scenario 21: Consultation data is persisted before completion button click**

```gherkin
Given a user has entered all consultation data and reached the Summary page
When viewing the Summary
Then all data has been previously saved to the consultation model
And all sections contain the most recent data entered
And if user navigates away and closes browser or loses connection
And later re-opens the same patient consultation
Then all entered data is still present and recoverable
```

**Scenario 22: Completed consultation status is recorded with timestamp**

```gherkin
Given a user has successfully clicked "Complete consultation"
And the consultation has been marked as complete
When the consultation is retrieved later
Then the consultation status shows: "Completed"
And a completion timestamp is recorded in ISO 8601 format
And the consultation can be viewed in consultation history/audit log
And the consultation cannot be edited further (completion lock in place)
And no data is persisted after completion
```

**Scenario 23: API failure retrieving summary data**

```gherkin
Given the Review Consultation Summary page is loading
When the system attempts to retrieve consultation data from the API
And the API call fails (500 error, timeout, network connectivity issue)
Then an error message is displayed: "Unable to load consultation summary. Please try again."
And a Retry button or Previous button is provided to the user
And the page does not allow completion until data is successfully loaded
And the user can attempt to retry the data load
```

**Scenario 24: Missing consultation data when summary page loads**

```gherkin
Given the Review Consultation Summary page is loading
When one or more required consultation fields are missing or null in the model
Then the Summary page loads but displays:
  - Missing sections or fields as empty or "Not provided"
  - Validation errors appear when user attempts to complete
  - Clear messaging about which fields are incomplete
And user is directed back to outcome pages to complete missing data
And the Complete consultation button is disabled until all mandatory fields are populated
```

**Scenario 25: Concurrent consultation edits (conflict resolution)**

```gherkin
Given a user is viewing the Review Consultation Summary page
And another user or session edits the same consultation simultaneously
When the local Summary becomes out-of-sync with server data
Then one of the following occurs:
  - Page displays refresh notification: "Consultation has been updated. Please refresh to see the latest data."
  - Automatic refresh retrieves updated data
  - User is prevented from completing until refresh occurs
And data conflicts are resolved (e.g., last-write-wins or user prompted to reload)
And no data is persisted during conflict resolution
```

### Accessibility

**Scenario 26: Summary page meets WCAG 2.1 AA accessibility compliance**

```gherkin
Given the Review Consultation Summary page is rendered
When a user navigates using keyboard and screen reader
Then all content sections are properly structured with headings
And summary data is organized in logical reading order
And the page heading is announced as: "Consultation summary" (heading level 2)
And patient information section is announced as a logical group
And each consultation outcome section is clearly delimited
And buttons are announced with purpose: "Previous, button", "Complete consultation, button", "Cancel and exit, link"
And the tab order is logical: Previous → Complete consultation → Cancel and exit
And all interactive elements are focusable via Tab key
And focus indicators are visible and meet 3:1 contrast ratio
```

**Scenario 27: Read-only data fields are announced correctly to screen readers**

```gherkin
Given summary data is displayed in read-only format
When a screen reader user navigates the page
Then read-only fields are not treated as interactive
And data labels and values are clearly associated
And text content is readable in continuous flow
And tables (if used for medication summaries) are marked up with proper header rows and data associations
And complex data structures (e.g., medication details) have clear labels
And aria-labels or aria-describedby attributes are used where needed
```

**Scenario 28: Error messages and validation alerts are announced to screen readers**

```gherkin
Given the user clicks "Complete consultation" and validation fails
When error summary displays
Then error messages are announced with role="alert" and aria-live="polite"
And each error message is descriptive and actionable
And focus is moved to error summary for immediate announcement
And inline error messages near affected sections are marked up for screen readers
And error links (if clickable to navigate to errors) are properly announced as links
And no data is persisted when errors occur
```

**Scenario 29: Page supports zoom and responsive layout**

```gherkin
Given the Review Consultation Summary page is displayed
When user zooms to 200% magnification
Then all content remains readable and navigable
And horizontal scrolling is minimal (single column layout preferred)
And buttons remain accessible and clickable at 200% zoom
And summary sections maintain clear visual hierarchy
And no content is hidden or truncated unexpectedly
And text is not forced to single word per line
```

**Scenario 30: High contrast color scheme for readability**

```gherkin
Given the Review Consultation Summary page is rendered
When viewing patient information and summary data
Then all text has color contrast ratio of at least 4.5:1 against background
And buttons are clearly distinguished by color, shape, or icon
And error messages use color + icon + text (not color alone for meaning)
And patient information sections have visual separation (borders, spacing, or background)
And links are identifiable (underline or other indicator beyond color alone)
And read-only fields have distinct visual appearance from input fields
And focus indicators have sufficient contrast (3:1 minimum)
```

---

## Definition of Done

- [ ] All 28 acceptance criteria scenarios pass (manual or automated testing)
- [ ] Page structure matches DHCW Design System V2 components exactly
- [ ] All consultation data is displayed in read-only format with appropriate styling
- [ ] Patient information section clearly displays name, NHS number, DOB, age, adverse reactions
- [ ] Referral pathway: All referral fields displayed (destination, timescale, information, advice)
- [ ] Advice pathway: Advice text displayed with "Not provided" placeholder if empty
- [ ] CAS Supply pathway: Formulary and antibiotic medications displayed with quantities, dosages, backup supply status
- [ ] Conditional sections display correctly based on pathway type and condition type
- [ ] Previous button styled as Secondary button and navigates back to outcome page
- [ ] Complete consultation button styled as Primary button
- [ ] Cancel and exit displays as Action Link
- [ ] No edit controls or input fields appear on Summary page (read-only throughout)
- [ ] Summary data persists when user navigates back and returns
- [ ] All entered data matches consultation model exactly
- [ ] Complete consultation button is only enabled when all mandatory data is present
- [ ] Completion validation runs when button clicked and displays errors if validation fails
- [ ] Consultation is marked as "Complete" in model only after successful completion click
- [ ] Timestamp recorded for consultation completion
- [ ] User navigates to appropriate page after successful completion (destination per workflow spec)
- [ ] Cancel and exit navigates to Patient overview without marking consultation complete
- [ ] Error summary displays at top of page with role="alert" and aria-live="polite" when errors occur
- [ ] Inline error messages appear next to affected fields/sections
- [ ] API failure handling displays user-friendly error message with retry option
- [ ] Missing data handling prevents completion and directs user back to outcome pages
- [ ] Concurrent edit conflicts are detected and handled appropriately
- [ ] WCAG 2.1 AA accessibility compliance verified
- [ ] Color contrast ratio 4.5:1 or higher for all text elements
- [ ] Tab order follows defined sequence: Previous → Complete consultation → Cancel and exit
- [ ] Screen reader announces sections, labels, and error messages correctly
- [ ] Page functional when zoomed to 200%
- [ ] Keyboard navigation works without mouse

---

## Notes

**Navigation Flow:**
- **Previous:** Returns to the last outcome-specific page (Referral, Advice, or CAS Supply) preserving all data
- **Complete consultation:** Marks consultation as complete, finalizes all data, and navigates forward (destination TBD in workflow)
- **Cancel and exit:** Returns to Patient overview without marking consultation complete; user can re-enter to complete later

**Data Display:**
- All summary data is read-only; no in-line editing on this page
- Users must click Previous to return to outcome pages for edits
- Summary page is a "gateway to completion"—ensures review before finalizing

**Pathway Variations:**
- **CCM Referral:** 3 outcome options (Refer patient, Advice only, or possibly others)—each flows through Outcome Selection to their own outcome page to Summary
- **CAS Supply:** Similar flow but with condition-based section visibility (Formulary only vs. Formulary + Antibiotics for STTT/UTI)
- **IPS:** Service-specific details (structure TBD by IPS story)

**Completion Semantics:**
- Clicking "Complete consultation" finalizes the consultation in the system
- Consultation status changes to "Completed" and cannot be edited further
- A timestamp is recorded for audit/compliance purposes
- Completed consultations appear in history/reporting

**Error Handling Strategy:**
- Validation errors on completion are shown in error summary with inline messages
- User must return to outcome page to fix issues and re-submit
- No partial completion states—either fully complete or incomplete with ability to resume

**Cross-Browser & Responsive Considerations:**
- Summary page should work on desktop, tablet, and mobile views
- Read-only display allows for flexible layout options
- Patient information and summary sections may stack on mobile without affecting readability
