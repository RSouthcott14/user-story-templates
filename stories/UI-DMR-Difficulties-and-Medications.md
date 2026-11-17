# UI: DMR Difficulties and Medications

## Story Type: UI
## Tags: `AI Generated` | `Review (BR)` | `DMR`

---

## Description

### Story Card
**As a** pharmacy user  
**I want** to record difficulties and medication discussions for the Discharge Medicines Review  
**So that** I can document patient outcomes and follow-up actions needed

### Context
This story is Part 2 Step 3 of the Discharge Medicines Review (DMR) journey. This page follows either:
- **If discrepancies were identified in Part 1:** Directly after the Medication Discrepancies page
- **If NO discrepancies in Part 1:** Directly after the Consultation Details Part 2 page (skipping the Medication Discrepancies page)

The page displays:

1. **Part 1 Summary** (expandable details component showing: eligibility criteria, medicines being taken after discharge, discrepancies identified, medicine counts, etc.)

2. **Digital DAL** (optional — based on Part 1 data):
   - If digital DAL was used: Show "View DAL (opens in a new tab)" button that opens PDF in new browser window
   - If digital DAL NOT available: Show callout message: "A digital DAL is not available for this DMR. Part 1 details may have been entered from a physical copy of the patient's DAL"

3. **Three Mandatory Questions with Conditional Details:**
   - **Difficulties:** "Are there any issues or difficulties arising from new medication?" (Yes/No, if Yes shows text field with 2000-character limit)
   - **Medication Optimisation:** "Have you discussed medication optimisation with the patient?" (Yes/No, if Yes shows text field with 2000-character limit)
   - **Medication Interactions:** "Have you discussed medication interactions with the patient?" (Yes/No, if Yes shows text field with 2000-character limit)

**Navigation Logic:**
- **Previous Button:** Navigates to Medication Discrepancies page if discrepancies exist, otherwise back to Consultation Details Part 2
- **Continue Button:** All questions must be answered before proceeding. Navigates to Follow-up page with all data persisted

**Validation:** All three questions are mandatory—user cannot proceed to next page without answering all three questions.

### Out of Scope
- Clinical decision support or medication interaction checking
- Automatic DAL retrieval or validation
- Editing or updating Part 1 summary data from this page (view-only)
- Historical consultation records
- Multi-language support or localization
- Medication optimisation database or recommendation engine
- Integration with external pharmacy systems
- Printer-friendly formatting or PDF export

### Related Stories
- **UI: DMR Part 2 Consultation Details** (upstream) — Entry point; determines navigation to this page
- **UI: DMR Part 1 Medication Discrepancies** (upstream, conditional) — Navigated to by Previous button if discrepancies exist
- **UI: DMR Follow-up** (downstream) — Navigated to by Continue button after all questions answered

---

## Design Reference

**Design System:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Component Guidance:** [COMPONENTS.md](../COMPONENTS.md)

**Error Messaging Standard:** [standards/UI-ERROR-MESSAGING.md](../standards/UI-ERROR-MESSAGING.md)

**Textarea Standard:** [standards/UI-TEXTAREA-SPECIFICATIONS.md](../standards/UI-TEXTAREA-SPECIFICATIONS.md)

### Components Used

- **Tabs** — Consultation tabs (Consultation, Overview, Patient details, Adverse reactions, History, Pharmacy services, WGPR, Notes)
  [Figma: Tabs](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

- **Details/Disclosure** — Part 1 summary (collapsible component)
  [Figma: Disclosure](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4589)

- **Button (Secondary)** — View DAL button
  [Figma: Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475)

- **Callout / Alert** — Digital DAL unavailable message
  [Figma: Callout](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

- **Radio Buttons** — Yes/No options for all three questions
  [Figma: Radio Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4007)

- **Textarea** — Details fields (2000-character limit when Yes selected)
  [Figma: Textarea](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6218-4824)

- **Buttons** — Previous (secondary), Save and continue (primary)
  [Figma: Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475)

- **Action Link** — Cancel and exit
  [Figma: Action Link](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9493)

- **Inset Text** — Helper text and instructions
  [Figma: Inset Text](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4589)

---

## Acceptance Criteria

**Total Scenarios: 15**

### Page Structure & Content

**Scenario 1: Display Difficulties and Medications page with digital DAL available**

```gherkin
Given the user navigates to the Difficulties and Medications page
And a digital DAL was used in Part 1
When the page loads
Then the following elements are displayed:
  - Page heading: "Difficulties and medications"
  - Consultation tabs (Consultation, Overview, Patient details, Adverse reactions, History, Pharmacy services, WGPR, Notes)
  - Collapsible "Part 1 summary" section (initially collapsed)
  - Button labeled "View DAL (opens in a new tab)"
  - Section heading: "Difficulties"
  - Question: "Are there any issues or difficulties arising from new medication?"
  - Yes/No radio buttons
  - Section heading: "Medication optimisation"
  - Question: "Have you discussed medication optimisation with the patient?"
  - Yes/No radio buttons
  - Section heading: "Medication interactions"
  - Question: "Have you discussed medication interactions with the patient?"
  - Yes/No radio buttons
  - Previous button (secondary)
  - Save and continue button (primary)
  - Cancel and exit link
  - Patient banner displays available patient information above the main content
```

**Scenario 2: Display Difficulties and Medications page with digital DAL unavailable**

```gherkin
Given the user navigates to the Difficulties and Medications page
And a digital DAL is not available from Part 1
When the page loads
Then the following elements are displayed (excluding View DAL button):
  - Page heading: "Difficulties and medications"
  - Consultation tabs
  - Collapsible "Part 1 summary" section
  - Callout message: "A digital DAL is not available for this DMR. Part 1 details may have been entered from a physical copy of the patient's DAL"
  - All three questions and radio buttons (Difficulties, Medication optimisation, Medication interactions)
  - Previous button (secondary)
  - Save and continue button (primary)
  - Cancel and exit link
And the "View DAL (opens in a new tab)" button is NOT displayed
```

**Scenario 3: Display Part 1 summary content when expanded**

```gherkin
Given the user is on the Difficulties and Medications page
When the user clicks on the "Part 1 summary" section to expand it
Then the following data is displayed:
  - Eligibility criteria
  - Medicines being taken after discharge (list)
  - Discrepancies identified (details of each)
  - Number of medicines on DAL
  - Number of medicines patient is taking
  - Number of discrepancies identified
  - Sensitive information indicator
And the section remains expanded until the user collapses it
And the displayed data matches the data recorded in Part 1
```

### Default State

**Scenario 4: Initial page load with no selections**

```gherkin
Given the user navigates to the Difficulties and Medications page
When the page loads for the first time
Then no radio buttons are pre-selected for any question
And no "Give details" text areas are displayed
And all fields are empty
And the Save and continue button is enabled
And focus is set to the page heading
```

### User Interaction — Difficulties Question

**Scenario 5: User selects Yes for Difficulties question**

```gherkin
Given the user is on the Difficulties and Medications page
When the user selects "Yes" for "Are there any issues or difficulties arising from new medication?"
Then the Yes radio button is selected and visually marked
And a "Give details" text area appears below the radio buttons
And a character counter displays: "You have 2000 characters remaining"
And a placeholder or hint text is shown: "Please describe the difficulties or issues"
And the selection is retained if the user navigates away and returns
```

**Scenario 6: User selects No for Difficulties question**

```gherkin
Given the user is on the Difficulties and Medications page
When the user selects "No" for "Are there any issues or difficulties arising from new medication?"
Then the No radio button is selected and visually marked
And no "Give details" text area is displayed
And the selection is retained if the user navigates away and returns
```

### User Interaction — Medication Optimisation Question

**Scenario 7: User selects Yes for Medication optimisation question**

```gherkin
Given the user is on the Difficulties and Medications page
When the user selects "Yes" for "Have you discussed medication optimisation with the patient?"
Then the Yes radio button is selected and visually marked
And a "Give details" text area appears below the radio buttons
And a character counter displays: "You have 2000 characters remaining"
And a placeholder or hint text is shown: "Please describe the medication optimisation discussion"
And the selection is retained if the user navigates away and returns
```

**Scenario 8: User selects No for Medication optimisation question**

```gherkin
Given the user is on the Difficulties and Medications page
When the user selects "No" for "Have you discussed medication optimisation with the patient?"
Then the No radio button is selected and visually marked
And no "Give details" text area is displayed
And the selection is retained if the user navigates away and returns
```

### User Interaction — Medication Interactions Question

**Scenario 9: User selects Yes for Medication interactions question**

```gherkin
Given the user is on the Difficulties and Medications page
When the user selects "Yes" for "Have you discussed medication interactions with the patient?"
Then the Yes radio button is selected and visually marked
And a "Give details" text area appears below the radio buttons
And a character counter displays: "You have 2000 characters remaining"
And a placeholder or hint text is shown: "Please describe the medication interactions discussion"
And the selection is retained if the user navigates away and returns
```

**Scenario 10: User selects No for Medication interactions question**

```gherkin
Given the user is on the Difficulties and Medications page
When the user selects "No" for "Have you discussed medication interactions with the patient?"
Then the No radio button is selected and visually marked
And no "Give details" text area is displayed
And the selection is retained if the user navigates away and returns
```

### Character Counting & Text Input

**Scenario 11: User enters text with character counter updates**

```gherkin
Given the user has selected "Yes" for any question
And a "Give details" text area is displayed
When the user types text into the text area
Then the entered text is displayed in the field
And the character counter updates in real-time: "You have [2000 - n] characters remaining"
And when 2000 characters have been entered, the counter displays: "You have 0 characters remaining"
And the user cannot enter more than 2000 characters
And the text is retained if the user navigates away and returns
And the text is stored for submission
```

**Scenario 12: User switches Yes to No for any question**

```gherkin
Given the user has selected "Yes" and entered text in a text area
When the user selects "No" for the same question
Then the "Give details" text area is hidden
And any text previously entered in the text area is cleared
And the selection is retained as "No"
```

### View DAL Functionality

**Scenario 13: User clicks View DAL button (when digital DAL available)**

```gherkin
Given the user is on the Difficulties and Medications page
And a digital DAL is available
When the user clicks the "View DAL (opens in a new tab)" button
Then the digital DAL document/file is opened in a new browser window/tab
And the user remains on the Difficulties and Medications page
And the browser tab/window title shows the DAL document name
And the user can close the new tab/window to return to the consultation page
```

### Validation & Navigation

**Scenario 14: User attempts Save and continue without answering all questions**

```gherkin
Given the user is on the Difficulties and Medications page
When the user has NOT selected Yes or No for all three questions
And the user clicks the "Save and continue" button
Then an error message is displayed: "Please answer all three questions before continuing"
And focus moves to the first unanswered question
And no data is submitted
And the user remains on the Difficulties and Medications page
And all previously entered data is retained
```

**Scenario 15: User clicks Save and continue with all questions answered**

```gherkin
Given the user is on the Difficulties and Medications page
And the user has selected Yes/No for all three questions
And where "Yes" is selected, details have been entered (up to 2000 characters)
When the user clicks the "Save and continue" button
Then all question responses and optional details are saved
And the user is navigated to the Follow-up page
And all DMR data is passed to the next page
And the data is persisted in the system
And when Previous button is clicked from this page:
  - If discrepancies existed in Part 1, navigate to Medication Discrepancies page
  - If NO discrepancies in Part 1, navigate back to Consultation Details Part 2
```

---

## Notes
- Part 1 summary data should be retrieved from the session/previous step and displayed in a read-only format
- Digital DAL availability is determined in Part 1 and passed as a flag to this page
- All three questions are **mandatory**—validation must prevent proceeding without answering all three
- Character limit for text areas is a hard constraint—users cannot exceed 2000 characters
- Character counter follows [standards/UI-TEXTAREA-SPECIFICATIONS.md](../standards/UI-TEXTAREA-SPECIFICATIONS.md) format: `"You have X characters remaining"`
- Previous button navigation logic depends on whether Medication Discrepancies page was displayed (which depends on whether discrepancies were found in Part 1)
- Form validation and error handling follow [standards/UI-ERROR-MESSAGING.md](../standards/UI-ERROR-MESSAGING.md)
- If a user edits a response (e.g., changes from Yes to No and back), the text field behavior should be predictable and clear
