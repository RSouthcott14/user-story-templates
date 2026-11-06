# UI: Review Consultation Summary – Complete Consultation (Generic)

**Story Type:** UI  
**Tags:** AI Generated, Review (BR)

---

## Description

### Story Card

**As a** pharmacy user  
**I want** to review all consultation details before completing the consultation  
**So that** I can verify all data is correct and mark the consultation as complete

### Context

This story defines the **Review Consultation Summary** page—the final verification step before consultation completion. It displays a read-only summary of all consultation data collected across the entire workflow, supporting multiple consultation types:

**Supported Consultation Types:**
1. **CCM with Clinical Scoring Tools** – Sore Throat consultations using:
   - Centor scoring (Exudate/swelling on tonsils, Tender/swollen anterior cervical lymph nodes, Temp >38°C, Cough: Yes/No)
   - FeverPAIN scoring (Cough or sore throat, Fever last 24 hours, Onset <3 days, No runny nose, Inflamed tonsils)
2. **CCM No-Template Consultations** – Dry Eye, Dry Eyes Disease, and similar conditions without predefined scoring
3. **CAS Consultations** – Common Ailments Service with Supply & Refer outcomes

### Key Features

All consultation types display:
- **Consultation details** – GPhC number, date/time, consultation type, referred by
- **Symptoms and history** – Presenting symptoms, relevant medical/family history
- **Service, condition and tool** – Service type, condition name, scoring tool used (if applicable)
- **Clinical scoring assessment** – Tool-specific criteria and calculated scores (varies by tool: Centor, FeverPAIN, or None)
- **Point of Care test** – Test type, batch, expiry, result, if applicable
- **Consultation outcome** – Selected outcome (Referral, Advice, Supply & Refer, etc.)
- **Medications** – Formulary medicines, antibiotic selections, dosages, quantity, advice
- **Referral information** – Destination, timescale, notes (if outcome includes referral)
- **Sensitive information** – Whether consultation contains sensitive health data
- **Send consultation summary** – Email to GP preference
- **Action buttons:** Previous (to edit), Complete consultation, Cancel and exit

### Design Coverage

The page structure remains consistent across all consultation types. Conditional sections display based on:
- **Consultation pathway** (CCM vs. CAS)
- **Outcome type** (Referral, Advice, Supply & Refer, etc.)
- **Scoring tool used** (Centor, FeverPAIN, None—each with different field sets)
- **Services captured** (Medications, Point of Care test, Referral, etc.)

### Related Stories

| Story ID | Title | Type | Details |
|----------|-------|------|---------|
| 603150 | UI: CCM - Outcome Selection (Limited Options) | Related | Entry point—selects outcome path (Referral, Advice, or Supply) |
| 603161 | UI: CCM - Record Referral Outcome | Related | Referral pathway branch |
| 603162 | UI: CCM - Record Advice Outcome | Related | Advice pathway branch |
| 618128 | UI: CCM: CAS Supply Medication Quantity and Dosage Entry | Related | CAS Supply pathway branch |
| 603156 | UI: CCM - Sensitive Health Information | Related | Optional step in some pathways |

### Dependent Stories

- All outcome recording stories must complete before Review Consultation Summary is displayed
- Consultation model/state management backend API

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

**Total Scenarios: 23**

### Page Structure & Content

**Scenario 1: Review Consultation Summary page displays all consultation types and scoring tools**

```gherkin
Given a consultation has been completed (CCM or CAS pathway)
When the user navigates to the Review Consultation Summary page
Then the page displays consistently across all consultation types:
  - High level caption: "Clinical Conditions Management" (or service name)
  - Page heading: "Consultation summary"
  - Consultation details section (read-only):
    * GPhC number: [value]
    * Date and time of consultation: [date time]
    * Consultation undertaken as: [type: Face to face, Remote, etc.]
    * Referred by: [value]
  - Symptoms and history section:
    * Presenting symptoms: [value]
    * Relevant medical, social and/or family history (optional): [value or "Not recorded"]
  - Service, condition and tool section:
    * Service: [value: Common Ailments Service, Clinical Conditions Management, Independent Prescribers Service]
    * Condition: [value: Sore Throat, Dry Eye, etc.]
    * Clinical scoring tool: [value: Centor, FeverPAIN, None]
  - Clinical scoring assessment section (conditional, varies by tool):
    * FOR CENTOR: Exudate/swelling on tonsils, Tender cervical lymph nodes, Temp >38°C, Cough (Yes/No), Centor score
    * FOR FEVERPAIN: Cough or sore throat, Fever last 24 hours, Onset <3 days, No runny nose, Inflamed tonsils, FeverPAIN score
    * FOR NO TOOL: "No clinical scoring tool used for this consultation"
  - Point of Care test section (if applicable):
    * Test type, batch, expiry, result: [values or "Not recorded"]
  - Consultation outcome section:
    * Outcome: [value: Refer patient, Advice only, Supply & Refer, etc.]
  - Medication(s) section (if outcome includes supply):
    * Read-only table: Medicine, Pack size, Quantity supplied, Dosage, Backup supply (if applicable)
    * Advice given to patient: [value or "Not provided"]
  - Referral information section (if outcome includes referral):
    * Referred to: [destination]
    * Timescale for referral: [timescale]
    * Notes: [text or "No notes recorded"]
  - Sensitive information section:
    * Does this consultation contain sensitive health information? [Yes/No]
  - Send consultation summary section:
    * Send a summary letter electronically to GP? [Yes/No]
  - Action buttons at bottom:
    * Secondary button: "Previous" (navigate back to edit)
    * Primary button: "Complete consultation" (finalize)
    * Action link: "Cancel and exit" (discard)
And all data is displayed in read-only format (no edit controls on this page)
And the page layout uses maximum section width with equal left/right margins
And all sections are properly labeled and organized sequentially
```

**Scenario 2: Centor scoring tool displays accurate clinical assessment data**

```gherkin
Given a CCM Sore Throat consultation using Centor scoring has been completed
And Centor criteria were recorded: Exudate/swelling on tonsils (Yes/No), Tender anterior cervical lymph nodes (Yes/No), Temperature >38°C (Yes/No), Cough (Yes/No)
And a Centor score was calculated based on the criteria
When the user views the Review Consultation Summary page
Then the Clinical scoring assessment section displays:
  - Label: "Clinical scoring assessment"
  - All Centor criteria fields with recorded Yes/No values
  - Clinical scoring outcome: [text explaining management based on score]
  - Centor score: [calculated score]
  - Change link to navigate back to scoring section if needed
And the data matches exactly what was entered during the Centor assessment page
```

**Scenario 3: FeverPAIN scoring tool displays accurate clinical assessment data**

```gherkin
Given a CCM Sore Throat consultation using FeverPAIN scoring has been completed
And FeverPAIN criteria were recorded: Cough or sore throat, Fever last 24 hours (Yes/No), Onset <3 days (Yes/No), No runny nose (Yes/No), Inflamed tonsils (Yes/No)
And a FeverPAIN score was calculated
When the user views the Review Consultation Summary page
Then the Clinical scoring assessment section displays:
  - All FeverPAIN criteria fields with recorded Yes/No values
  - Current temperature if recorded
  - Clinical scoring outcome: [text explaining management based on score]
  - FeverPAIN score: [calculated score]
And the data matches exactly what was entered during the FeverPAIN assessment page
```

**Scenario 4: No-template consultation displays without clinical scoring section (CCM & IPS)**

```gherkin
Given a consultation without a predefined scoring template:
  - CCM consultation: Dry Eye, Dry Eyes Disease, or similar condition, OR
  - IPS consultation: "Continue consultation without a template" service type
And no clinical scoring tool was used in the consultation
When the user views the Review Consultation Summary page
Then the Clinical scoring assessment section displays:
  - Message: "No clinical scoring tool used for this consultation"
  - For CCM: General clinical assessment notes (if entered): [text or "Not provided"]
  - For IPS: 
    * Clinical assessment notes: [entered notes or "Not provided"]
    * Examination notes: [entered notes or "Not provided"]
And no scoring criteria fields (Centor, FeverPAIN, etc.) are shown
And no scoring outcome or score calculation is displayed
And the General clinical assessment section includes Change link to edit if needed
```

**Scenario 5: CAS Supply pathway shows medications and antibiotic details**

```gherkin
Given a CAS consultation with Supply & Refer outcome has been completed
When the user views the Review Consultation Summary page
Then the Medication(s) and Antibiotic(s) section displays:
  - Read-only table with columns: Medicine, Pack size, Quantity supplied (dose units), Dosage
  - For formulary medications: Each medicine with its pack size, quantity, and dosage
  - For antibiotics (if STTT/UTI): Antibiotic name, pack size, quantity, dosage, backup supply status
  - Advice given to patient: [entered text or "No advice recorded"]
  - Change link to edit medications if needed
And all medication data matches what was entered during the medication entry page
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

---

## Definition of Done

- [ ] All 23 acceptance criteria scenarios pass (manual or automated testing)
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
