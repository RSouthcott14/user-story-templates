# ADR History Modal with Accordion Versions

## Story Type: UI

---

## Description

### Story Card
**As a** pharmacy user  
**I want** to view the complete history of adverse reaction (ADR) records for a patient  
**So that** I can understand how a reaction record has evolved and see all previous versions with their details

### Context
The patient's Adverse Reaction record now supports versioning to maintain a complete audit trail of changes. The ADR History Modal currently displays only the latest version of an ADR record. This story extends it to display all historical versions in an accordion format, allowing users to review changes made to the ADR over time without needing separate views or different screens.

The modal shows all versions in reverse chronological order (newest first). The most recent version is expanded by default for immediate visibility, while older versions collapse to save screen space. Users can expand any version to review its complete details.

### Out of Scope
- Exporting or downloading ADR history
- Bulk editing multiple ADR versions
- Filtering or sorting ADR versions by criteria
- Reordering ADR versions
- Adding new ADR versions from within the modal
- Deleting historical ADR versions
- Search or find-within-modal functionality
- Inline editing of any ADR version (editing happens via separate ADR edit workflow)

### Related Stories
- Adverse Reaction Record View/Edit — relates to: This modal displays historical versions of ADR records that are edited via the main ADR edit story

---

## Acceptance Criteria

**Total Scenarios: 7**

### Page Structure & Content

**Scenario 1: Modal displays ADR history with multiple versions in accordion format**

```gherkin
Given the user opens the ADR History modal for a patient adverse reaction
When the modal loads
Then the following elements are displayed:
  - Modal title: "Adverse reaction history: [Substance name]" (e.g., "Adverse reaction history: Bee venom")
  - A list of Expander components, one for each ADR version in reverse chronological order (newest first)
  - Each Expander header displays: The date the ADR was recorded/updated (formatted as a link, e.g., "11 Jun 2025")
  - A Close button at the bottom of the modal
And the modal width and spacing follow DHCW Design System V2 Panel component styling
And all text is left-aligned and readable
```

**Scenario 2: Most recent ADR version expands on modal load by default**

```gherkin
Given the ADR History modal loads with multiple versions
When the modal displays for the first time
Then the topmost Expander (most recent ADR version) is expanded
And the next Expanders below it (older versions) are collapsed
And the expanded version displays all ADR fields:
  - Type of reaction
  - Anaphylaxis observed
  - Date of last reaction
  - Date of first reaction (optional)
  - Symptoms and signs
  - Reaction observed by a clinician
  - Notes
  - Clinical status (if present in the ADR data)
And the expanded version does NOT display:
  - "Last updated by" / "Recorded by" fields
  - "Date recorded" field
```

### Default State

**Scenario 3: Each Expander header shows date as a styled link**

```gherkin
Given the ADR History modal is displayed
When the user views the Expander headers
Then each header displays the ADR version date in the format: "[Day] [Month] [Year]" (e.g., "11 Jun 2025")
And the date is styled as a link (blue, underlined, cursor pointer)
And a +/- icon appears to the left of the date header
And the expanded version displays a minus icon (−)
And the collapsed versions display a plus icon (+)
```

**Scenario 4: Older ADR versions remain collapsed until expanded**

```gherkin
Given the ADR History modal loads with multiple versions
And the most recent version is expanded
When the user views the collapsed versions
Then each collapsed Expander only shows:
  - The date link as the header
  - The +/− icon
And the content of the ADR fields is hidden
And no data is visible within the collapsed sections
```

### User Interaction

**Scenario 5: User can expand a collapsed ADR version**

```gherkin
Given the ADR History modal is displayed
And an older ADR version is collapsed
When the user clicks on the date link or the + icon in the Expander header
Then the Expander opens/expands
And the + icon changes to a − icon
And all ADR fields for that version are displayed:
  - Type of reaction
  - Anaphylaxis observed
  - Date of last reaction
  - Date of first reaction (optional)
  - Symptoms and signs
  - Reaction observed by a clinician
  - Notes
  - Clinical status (if present)
And the expanded content is positioned within the Expander without overflowing
```

**Scenario 6: User can collapse an expanded ADR version**

```gherkin
Given the ADR History modal is displayed
And an ADR version is currently expanded
When the user clicks on the date link or the − icon in the Expander header
Then the Expander closes/collapses
And the − icon changes to a + icon
And all ADR field content is hidden
And the Expander header remains visible showing only the date link
```

### Navigation

**Scenario 7: User closes the modal**

```gherkin
Given the ADR History modal is displayed with expanded and/or collapsed versions
When the user clicks the Close button
Then the modal closes
And the user returns to the patient record or previous screen
And any expanded/collapsed state is not persisted on re-opening
```

### Validation

**Scenario 8: ADR fields display correct data in each version**

```gherkin
Given the ADR History modal is displayed
And an ADR version is expanded
When the user views the expanded version content
Then each field displays the correct value for that specific ADR version
And the values displayed are retrieved from the version's historical data
And different versions can show different values (reflecting changes over time)
And all field labels and values are clearly readable with appropriate contrast
```

### Accessibility

**Scenario 9: Accordion and modal meet WCAG 2.1 AA accessibility standards**

```gherkin
Given the ADR History modal is displayed
When the user navigates using keyboard and screen reader
Then focus order is: Modal title → First Expander header → Subsequent Expander headers → Close button
And each Expander header is keyboard accessible and can be toggled with Enter or Space key
And the screen reader announces: "[Date] Expander, collapsed/expanded state" when focus reaches an Expander
And the Close button has focus indicator and can be activated with Enter or Space
And color contrast ratios meet WCAG 2.1 AA standards (4.5:1 for text, 3:1 for UI components)
And the modal has proper ARIA attributes (role="dialog", aria-labelledby, aria-modal="true")
And each Expander uses proper ARIA attributes (role="region", aria-expanded="true/false")
```

---

## Design Reference

**Design System:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Components Used:**
- **Panel** — Container for the modal content — [Panel Reference](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4590)
- **Expander** — Collapsible section for each ADR version — [Expander Reference](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-142)
- **Action Link** — Date header styled as clickable link — [Action Link Reference](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9493)
- **Button** — Close button (secondary style) — [Buttons Reference](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475)

**Summary List Pattern:** ADR field data displayed as key-value pairs within each expanded Expander — [Summary List Reference](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-77)

---

## Notes for Development

- **Version ordering:** Display ADR versions in reverse chronological order (most recent first)
- **Default expansion:** Most recent version must be expanded on modal load for all users
- **State management:** Expansion/collapse state is not persisted; resets on modal re-open
- **Field removal:** Ensure "Last updated by", "Recorded by", and "Date recorded" fields are removed from display (these details are now redundant with version dating)
- **Data consistency:** Each version should be retrievable and displayed completely from historical data storage
- **Performance:** If ADR has many versions (10+), consider pagination or virtualization to maintain modal performance
