# UI: DMR Part 2 Consultation Details

## Story Type: UI
## Tags: `AI Generated` | `Review (BR)` | `DMR`

---

## Description

### Story Card
**As a** pharmacy user  
**I want** to record consultation details for Part 2 of the Discharge Medicines Review  
**So that** I can complete the DMR process and determine the next steps (medication discrepancies or summary to GP)

### Context
This story is Part 2 of the Discharge Medicines Review (DMR) journey. After completing Part 1 (patient registration and eligibility), the user progresses to Part 2 Consultation Details where they:

1. **View Part 1 Summary** (expandable section showing: eligibility criteria, medicines being taken after discharge, discrepancies identified, number of medicines on DAL, number patient is taking, number of discrepancies identified)

2. **View Digital DAL** (optional — only displayed if a digital DAL is available from Part 1)
   - Version 1: Digital DAL exists → Show "View DAL" button opening in a new tab
   - Version 2: Digital DAL unavailable → Show callout message stating "A digital DAL is not available for this DMR. Part 1 details may have been entered from a physical copy of the patient's DAL"

3. **Answer question:** "Can part 2 of the DMR be completed?"
   - Options: Yes / No
   - If No is selected: Show text area for "Give details"
   
4. **GPhC Number** (read-only display with Change link)

5. **Consultation Details** including:
   - Start date and time of consultation (date/time picker fields)
   - How was part 2 of the intervention provided? (radio button options: With patient at pharmacy (without carer), With patient at pharmacy (with carer), With patient by telephone, With carer at pharmacy (without patient), Other)
   - If "Other" selected: Show text field for details

**Navigation Logic:**
- **If YES to "Can part 2 be completed?"** → Continue button navigates to Part 1 Medication Discrepancies page
- **If NO to "Can part 2 be completed?"** → Continue button navigates to Send Consultation Summary to GP page

### Out of Scope
- Validation of GPhC number format or against registries
- Real-time digital DAL retrieval or integration with external systems (DAL exists or doesn't — handled by Part 1)
- Editing or updating Part 1 summary data from this page (view-only)
- Historical consultation records or draft recovery
- Pharmacy-level configuration of available intervention types
- Offline mode or caching of consultation data
- Automatic time defaults or current-time population

### Related Stories
- **Part 1 Registration & Eligibility** (upstream) — Collects patient details and Part 1 summary data
- **Part 1 Medication Discrepancies** (downstream) — Displays if YES selected; navigated to after Continue
- **Send Consultation Summary to GP** (downstream) — Displays if NO selected; navigated to after Continue

---

## Design Reference

**Design System:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Component Guidance:** [COMPONENTS.md](../COMPONENTS.md)

**Error Messaging Standard:** [standards/UI-ERROR-MESSAGING.md](../standards/UI-ERROR-MESSAGING.md)

### Components Used

- **Tabs** — Consultation tabs (Consultation, Overview, Patient details, Adverse reactions, History, Pharmacy services, WGPR, Notes)
  [Figma: Tabs](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

- **Expandable Section / Details/Summary** — Part 1 summary (collapsible)
  [Figma: Disclosure](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4589)

- **Button (Secondary)** — View DAL button
  [Figma: Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475)

- **Callout / Alert** — Digital DAL unavailable message
  [Figma: Callout](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

- **Radio Buttons** — Can part 2 be completed? (Yes/No) & Intervention method
  [Figma: Radio Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4007)

- **Text Input (Textarea)** — Give details field (shown when No is selected)
  [Figma: Textarea](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6218-4824)

- **Text Input (Standard)** — Start date/time fields, Other details field
  [Figma: Text Input](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

- **Date/Time Picker** — Consultation date and time selection
  [Figma: Date Picker](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

- **Action Link** — Change GPhC number, Previous, Cancel and exit
  [Figma: Action Link](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9493)

- **Buttons** — Previous (secondary), Save and continue (primary)
  [Figma: Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475)

- **Info Section / Inset Text** — GPhC number display
  [Figma: Inset Text](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4589)

---

## Acceptance Criteria

**Total Scenarios: 11**

### Page Structure & Content

**Scenario 1: Display Part 2 Consultation Details page with digital DAL available (Version 1)**

```gherkin
Given the user is on the DMR Part 2 Consultation Details page
And a digital DAL was used in Part 1
When the page loads
Then the following elements are displayed:
  - Page heading: "Consultation details - part 2"
  - Consultation tabs (Consultation, Overview, Patient details, Adverse reactions, History, Pharmacy services, WGPR, Notes)
  - Collapsible "Part 1 summary" section (initially collapsed)
  - Button labeled "View DAL (opens in a new tab)"
  - Section heading: "Can part 2 of the DMR be completed?"
  - Radio options: Yes and No
  - Section heading: "GPhC number"
  - GPhC number displayed with "Change" link
  - Section heading: "Consultation details"
  - Start date and time fields with date/time picker
  - Section heading: "How was part 2 of the intervention provided?"
  - Five radio button options (With patient at pharmacy (without carer), With patient at pharmacy (with carer), With patient by telephone, With carer at pharmacy (without patient), Other)
  - Previous button (secondary)
  - Save and continue button (primary)
  - Cancel and exit link
```

**Scenario 2: Display Part 2 Consultation Details page with digital DAL unavailable (Version 2)**

```gherkin
Given the user is on the DMR Part 2 Consultation Details page
And a digital DAL is not available from Part 1
When the page loads
Then the following elements are displayed:
  - Page heading: "Consultation details - part 2"
  - Consultation tabs (Consultation, Overview, Patient details, Adverse reactions, History, Pharmacy services, WGPR, Notes)
  - Collapsible "Part 1 summary" section (initially collapsed)
  - Callout message stating: "A digital DAL is not available for this DMR. Part 1 details may have been entered from a physical copy of the patient's DAL"
  - Section heading: "Can part 2 of the DMR be completed?"
  - Radio options: Yes and No
  - Section heading: "GPhC number"
  - GPhC number displayed with "Change" link
  - Section heading: "Consultation details"
  - Start date and time fields with date/time picker
  - Section heading: "How was part 2 of the intervention provided?"
  - Five radio button options
  - Previous button (secondary)
  - Save and continue button (primary)
  - Cancel and exit link
And the "View DAL (opens in a new tab)" button is NOT displayed
```

**Scenario 3: Display Part 1 summary content when expanded**

```gherkin
Given the user is on the DMR Part 2 Consultation Details page
When the user clicks on the "Part 1 summary" section to expand it
Then the following data is displayed:
  - Eligibility criteria
  - Medicines being taken after discharge (list)
  - Discrepancies identified (details of each)
  - Number of medicines on DAL
  - Number of medicines patient is taking
  - Number of discrepancies identified
And the section remains expanded until the user collapses it
And the displayed data matches the data recorded in Part 1
```

### Default State

**Scenario 4: Initial page load with no selections**

```gherkin
Given the user navigates to the DMR Part 2 Consultation Details page
When the page loads for the first time
Then no radio buttons are pre-selected
And the "Give details" text area is hidden
And the GPhC number is displayed as read-only
And the Start date and time fields are empty
And the "How was part 2 of the intervention provided?" radio buttons are unselected
And the Save and continue button is enabled
And focus is set to the page heading
```

### User Interaction

**Scenario 5: User clicks View DAL button (Version 1 only)**

```gherkin
Given the user is on the DMR Part 2 Consultation Details page
And a digital DAL is available
When the user clicks the "View DAL (opens in a new tab)" button
Then the digital DAL document/file is opened in a new browser tab
And the user remains on the DMR Part 2 page
And the browser tab title shows the DAL document name
And the user can close the new tab to return to the consultation page
```

**Scenario 6: User selects No for "Can part 2 of the DMR be completed?"**

```gherkin
Given the user is on the DMR Part 2 Consultation Details page
When the user selects the "No" radio button for "Can part 2 of the DMR be completed?"
Then the text area labeled "Give details" is displayed below the No option
And the text area is focused or made visible
And a placeholder or hint text is shown: "Please explain why part 2 cannot be completed"
And the text area has a character counter (if applicable)
And the selection is retained if the user navigates away and returns
```

**Scenario 7: User selects Yes for "Can part 2 of the DMR be completed?"**

```gherkin
Given the user has previously selected "No" and entered details
When the user selects "Yes" for "Can part 2 of the DMR be completed?"
Then the "Give details" text area is hidden
And any text previously entered in the text area is cleared
And the selection is retained as "Yes" is selected
```

**Scenario 8: User enters start date and time of consultation**

```gherkin
Given the user is on the DMR Part 2 Consultation Details page
When the user enters a date in the Day, Month, Year fields
And the user enters a time in the Time fields (hours, minutes, AM/PM or 24-hour format)
Then the entered date and time are displayed in the fields
And the values are retained if the user navigates away and returns
And the date and time values are stored for submission
```

**Scenario 9: User selects intervention method**

```gherkin
Given the user is on the DMR Part 2 Consultation Details page
When the user selects one of the five radio button options for "How was part 2 of the intervention provided?"
Then the selected option is marked
And if "Other" is selected, a text input field is displayed below
And the selection is retained if the user navigates away and returns
And the selection is stored for submission
```

**Scenario 10: User clicks Other intervention method and enters details**

```gherkin
Given the user has selected "Other" for the intervention method
When the user enters details in the text field that appears
Then the entered text is displayed in the field
And the text is retained if the user navigates away and returns
And the text is stored for submission
And no character limit is enforced (or standard 255-character text field limit applies)
```

### Navigation

**Scenario 11: User clicks Save and continue with Yes selected**

```gherkin
Given the user is on the DMR Part 2 Consultation Details page
And the user has selected "Yes" for "Can part 2 of the DMR be completed?"
And all required fields are completed (date/time, intervention method, GPhC number is displayed)
When the user clicks the "Save and continue" button
Then the consultation details are saved
And the user is navigated to the Part 1 Medication Discrepancies page
And the Part 2 consultation details data is passed to the next page
And the data is persisted in the system
```

**Scenario 11b: User clicks Save and continue with No selected**

```gherkin
Given the user is on the DMR Part 2 Consultation Details page
And the user has selected "No" for "Can part 2 of the DMR be completed?"
And the user has entered details in the "Give details" text area
And all required fields are completed (date/time, intervention method, GPhC number is displayed)
When the user clicks the "Save and continue" button
Then the consultation details are saved
And the user is navigated to the Send Consultation Summary to GP page
And the Part 2 consultation details data and reason for not completing is passed to the next page
And the data is persisted in the system
```

---

## Notes
- Part 1 summary data should be retrieved from the session/previous step and displayed in a read-only format
- Digital DAL availability is determined in Part 1 and passed as a flag to this page
- GPhC number change functionality may navigate to a separate page or modal; exact UX to be confirmed with design
- Date/time validation rules (past dates, future dates, valid time ranges) to be confirmed
- Character limits for "Give details" text area to be confirmed with business
- Form validation and error handling follow [standards/UI-ERROR-MESSAGING.md](../standards/UI-ERROR-MESSAGING.md)
