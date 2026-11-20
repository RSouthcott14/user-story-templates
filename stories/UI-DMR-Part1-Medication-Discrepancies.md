# UI: DMR Part 1 Medication Discrepancies

## Story Type: UI
## Tags: `AI Generated` | `Review (BR)` | `DMR`

---

## Description

### Story Card
**As a** pharmacy user  
**I want** to review and resolve medication discrepancies identified in Part 1 of the Discharge Medicines Review  
**So that** I can document whether discrepancies have been addressed or provide notes for further action

### Context
This story is Part 2 Step 2 of the Discharge Medicines Review (DMR) journey. This page is **only displayed if discrepancies were identified during Part 1**. If no discrepancies exist in Part 1, the user skips this page and navigates directly to the "Difficulties and Medications" page.

The page displays a table of medicines with identified discrepancies from Part 1, including:
- **Medicine** (name, pack size, and dosage)
- **Nature of discrepancy** (what was identified)
- **Next step** (recommended action, e.g., "Refer to GP")
- **Have the above discrepancies been resolved?** (Yes/No resolution status)

For each medication with an unresolved discrepancy (No selected), users can optionally provide notes (2000-character limit).

**Data Source:** Discrepancies are populated from Part 1 of the DMR journey (Part 1 Medication Discrepancies data).

**Navigation Logic:**
- **If discrepancies exist in Part 1:** Display this page
- **If NO discrepancies in Part 1:** Skip directly to Difficulties and Medications page
- **On Save and Continue:** Navigate to Difficulties and Medications page with discrepancy resolution status persisted

### Design Notes
⚠️ **Missing Column:** The design is missing a dedicated "Dosage" column in the table header. Currently, dosage information is embedded within the Medicine cell alongside name and pack size. A separate **Dosage** column should be added between the Medicine and Nature of discrepancy columns for clarity and consistency with Part 1 summary display. This should be clarified during design review.

### Out of Scope
- Validation of discrepancy resolution against external systems
- Automatic discrepancy closure or clinical decision support
- Historical discrepancy tracking or audit trails
- Editing or updating Part 1 discrepancy details from this page (view-only)
- Medication interaction checking or clinical validation
- Pharmacy-level configuration of nature of discrepancy types
- Multi-medication discrepancy grouping or bulk operations
- Integration with GP referral systems (referral is documented as "Next step" text only)

### Related Stories
- **UI: DMR Part 2 Consultation Details** (upstream) — Preceding page; determines whether this page is displayed
- **UI: DMR Difficulties and Medications** (downstream) — Navigated to after Save and Continue; collects intervention details
- **UI: DMR Part 1 Summary** (upstream reference) — Contains discrepancy data shown on this page

---

## Design Reference

**Design System:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Component Guidance:** [COMPONENTS.md](../COMPONENTS.md)

**Error Messaging Standard:** [standards/UI-ERROR-MESSAGING.md](../standards/UI-ERROR-MESSAGING.md)

**Textarea Standard:** [standards/UI-TEXTAREA-SPECIFICATIONS.md](../standards/UI-TEXTAREA-SPECIFICATIONS.md)

### Components Used

- **Tabs** — Consultation tabs (Consultation, Overview, Patient details, Adverse reactions, History, Pharmacy services, WGPR, Notes)
  [Figma: Tabs](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

- **Table** — Medications with discrepancies (Medicine, Dosage, Nature of discrepancy, Next step, Resolution status)
  [Figma: Table](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-79)

- **Radio Buttons** — Yes/No resolution status for each medication
  [Figma: Radio Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4007)

- **Textarea** — Notes field (optional, shown when No is selected, 2000-character limit)
  [Figma: Textarea](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6218-4824)

- **Buttons** — Previous (secondary), Save and continue (primary)
  [Figma: Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475)

- **Action Link** — Cancel and exit
  [Figma: Action Link](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9493)

- **Inset Text** — Helper text for notes field or discrepancy instructions
  [Figma: Inset Text](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4589)

---

## Acceptance Criteria

**Total Scenarios: 10**

### Page Structure & Content

**Scenario 1: Display Part 1 Medication Discrepancies page when discrepancies exist**

```gherkin
Given discrepancies were identified during Part 1 of the DMR
When the user navigates to this page from Part 2 Consultation Details
Then the following elements are displayed:
  - Page heading: "Part 1 medication discrepancies"
  - Consultation tabs (Consultation, Overview, Patient details, Adverse reactions, History, Pharmacy services, WGPR, Notes)
  - Section heading: "Medicines with an identified discrepancy"
  - Table with the following columns:
    * Medicine (medication name, pack size, dosage)
    * Dosage (NOTE: Currently missing in design; should be separate column)
    * Nature of discrepancy
    * Next step
    * "Have the above discrepancies been resolved?" (Yes/No radio buttons)
  - Previous button (secondary)
  - Save and continue button (primary)
  - Cancel and exit link
  - Patient banner displays available patient information above the main content
```

**Scenario 2: Populate table with discrepancies from Part 1**

```gherkin
Given the user is on the Part 1 Medication Discrepancies page
When the page loads
Then each row in the table displays:
  - Medicine name, pack size, and dosage (from Part 1 data)
  - Nature of the discrepancy (from Part 1 data) — e.g., "Continued but at wrong dosage"
  - Recommended next step (from Part 1 data) — e.g., "Refer to GP"
  - Yes/No radio buttons (initially unselected)
And the table contains one row for each medication with a discrepancy identified in Part 1
And if no discrepancies exist, this page is NOT displayed (user navigates directly to Difficulties and Medications page)
```

**Scenario 3: Display missing Dosage column note**

```gherkin
Given the page is rendered
When the design is reviewed
Then note that:
  - A dedicated "Dosage" column is missing from the table header
  - Dosage information is currently embedded in the Medicine cell alongside name and pack size
  - A separate Dosage column should be added between Medicine and Nature of discrepancy columns
  - This requires clarification during design review with UX/Design team
```

### Default State

**Scenario 4: Initial page load with no selections**

```gherkin
Given the user navigates to the Part 1 Medication Discrepancies page
When the page loads for the first time
Then no radio buttons are pre-selected for any medication
And no "Notes (optional)" text areas are displayed
And all fields are empty
And the Save and continue button is enabled
And focus is set to the page heading
```

### User Interaction

**Scenario 5: User selects Yes for discrepancy resolved**

```gherkin
Given the user is on the Part 1 Medication Discrepancies page
And a medication row is displayed with Yes/No radio buttons
When the user selects "Yes" for a medication's resolution status
Then the Yes radio button is selected and visually marked
And no "Notes (optional)" text area is displayed for that medication
And the selection is retained if the user navigates away and returns
And the selection is stored for submission
```

**Scenario 6: User selects No for discrepancy resolved**

```gherkin
Given the user is on the Part 1 Medication Discrepancies page
And a medication row is displayed with Yes/No radio buttons
When the user selects "No" for a medication's resolution status
Then the No radio button is selected and visually marked
And a "Notes (optional)" text area appears below the radio buttons for that medication
And the text area is focused or made visible
And a placeholder or hint text is shown: "Enter any additional notes (optional)"
And a character counter displays: "You have 2000 characters remaining"
And the selection is retained if the user navigates away and returns
```

**Scenario 7: User enters notes with character counting**

```gherkin
Given the user has selected "No" for a medication
And the "Notes (optional)" text area is displayed
When the user types text into the text area
Then the entered text is displayed in the field
And the character counter updates in real-time: "You have [2000 - n] characters remaining"
And when 2000 characters have been entered, the counter displays: "You have 0 characters remaining"
And the user cannot enter more than 2000 characters
And the text is retained if the user navigates away and returns
And the text is stored for submission
```

**Scenario 8: User switches from No back to Yes**

```gherkin
Given the user has selected "No" and entered notes in the text area
When the user selects "Yes" for the same medication
Then the "Notes (optional)" text area is hidden
And any text previously entered in the text area is cleared
And the selection is retained as "Yes"
And focus moves to the Yes radio button
```

**Scenario 9: Multiple medications with mixed resolutions**

```gherkin
Given the user is on the Part 1 Medication Discrepancies page
And the table displays multiple medications with discrepancies
When the user selects different resolutions for different medications:
  - Medication A: Yes (resolved)
  - Medication B: No (not resolved) with notes
  - Medication C: Yes (resolved)
Then each medication retains its own resolution status independently
And only Medication B displays a "Notes (optional)" text area
And all selections and notes are retained if the user navigates away and returns
```

### Navigation

**Scenario 10: User clicks Save and continue**

```gherkin
Given the user is on the Part 1 Medication Discrepancies page
And the user has selected Yes/No resolution status for all displayed medications
And where "No" is selected, optional notes have been entered (up to 2000 characters)
When the user clicks the "Save and continue" button
Then all medication discrepancy resolutions and notes are saved
And the user is navigated to the Difficulties and Medications page
And the discrepancy resolution data is passed to the next page
And the data is persisted in the system
And if no discrepancies were identified in Part 1:
  - This page is not displayed
  - User is navigated directly to Difficulties and Medications page
```

---

## Notes
- Discrepancy data is sourced from Part 1 of the DMR journey and displayed in read-only format (except for resolution status and notes)
- The character limit for notes is a hard constraint—users cannot exceed 2000 characters
- Character counter follows [standards/UI-TEXTAREA-SPECIFICATIONS.md](../standards/UI-TEXTAREA-SPECIFICATIONS.md) format: `"You have X characters remaining"`
- **Design Gap:** Missing Dosage column in table header should be addressed in design review
- Form validation and error handling follow [standards/UI-ERROR-MESSAGING.md](../standards/UI-ERROR-MESSAGING.md)
- If a user edits a resolution status (e.g., changes from Yes to No and back), the notes field behavior should be predictable and clear
