# DMR: Consultation Summary - Eligible Pathway

## Description

### Story Card
**As a pharmacy professional conducting a Discharge Medicines Review**
**I want to review a complete summary of all consultation information captured during the eligible DMR pathway**
**So that I can verify the details are correct and request any amendments before completing the consultation**

### Context
The Consultation Summary page provides the final review stage before consultation completion. It displays a read-only summary of all information captured during the DMR Part 1 journey for eligible patients. This page enables the pharmacy professional to verify information and make amendments where necessary.

### Out of Scope
- GP consultation summary communication outcomes (handled by separate completion page)
- Patient-facing components
- Printing and patient information leaflets
- Data export functionality
- Consultation history view

### Related / Dependent Stories
- US 623536: UI: DMR - Consultation Details Part 1
- US 623537: UI: DMR - Discharge Details
- US 623540: UI: DMR - Medication Details
- US 623541: UI: DMR - Discrepancy Action Selection
- US 623542: UI: DMR - Discrepancy Details

### Design Reference
Figma: Choose Pharmacy Current (Re)Designs – DMR Summary and Completion pages
DHCW Design System V2: Standard section cards with Change links, read-only value display

---

## Acceptance Criteria

### Default State

**Scenario 1: Page displays all eligible pathway consultation sections**
```gherkin
Given the pharmacy professional has completed all consultation steps for an eligible DMR
When they navigate to the Consultation Summary page
Then the page displays the title "Consultation summary - part 1"
And the page displays all applicable sections in the following order:
  • Discharge Advice Letter (DAL) review
  • Consultation details
  • Discharge details
  • Medicine information
And each section displays a "Change" link aligned to the right
And the page displays "Previous" and "Complete part 1" action buttons
And the page displays a "Cancel and exit" link
```

**Scenario 2: Page displays only sections with recorded data**
```gherkin
Given an eligible DMR consultation with complete but variable data
When the Consultation Summary page renders
Then only sections containing recorded information are displayed
And empty sections are not shown on the summary
And the page layout adjusts to remove gaps
```

### Discharge Advice Letter (DAL) Review Section

**Scenario 3: DAL section displays all captured fields**
```gherkin
Given a completed eligible DMR consultation
When the Consultation Summary page loads
Then the Discharge Advice Letter (DAL) review section displays:
  • Field label: "Latest DAL available" with value "Yes" or "No"
  • Field label: "DAL eligible for DMR" with value "Yes" or "No"
And all displayed values are read-only (non-editable)
And the section header has a blue "Change" link
```

**Scenario 4: DAL section displays recorded digital DAL decision**
```gherkin
Given the pharmacy professional selected a digital DAL during consultation
When the Consultation Summary page displays the DAL section
Then the section includes:
  • "Digital DAL available" showing "Yes"
  • "Digital DAL used" showing "Yes" or "No"
And the decision is displayed as recorded during consultation
```

### Consultation Details Section

**Scenario 5: Consultation details displays all recorded information**
```gherkin
Given a completed eligible DMR consultation
When the Consultation Summary page loads
Then the Consultation details section displays:
  • "GPhC" showing the pharmacist's GPhC registration number (e.g., "GPhC102938")
  • "Start date and time of the consultation" (e.g., "14 August 2026 13:22")
  • "Discharge information provided by" (e.g., "Hospital", "Community care", "Other")
  • "How was part one of the consultation provided?" (e.g., "With patient at pharmacy")
And all values are displayed as recorded and are read-only
```

**Scenario 6: GPhC number can be changed via Change link**
```gherkin
Given the Consultation Summary page displays the Consultation details section
When the user clicks the "Change" link in that section
Then the system returns to the Consultation details edit page
And the GPhC number field is pre-populated with the previously entered value
And the user can modify the GPhC number
```

### Discharge Details Section

**Scenario 7: Discharge details displays hospital and location information**
```gherkin
Given a completed eligible DMR consultation
When the Consultation Summary page loads
Then the Discharge details section displays:
  • "Place of discharge" with full hospital details in format:
    "[Hospital Name], [Address], [Town], Wales, United Kingdom"
  • "Ward" (if recorded)
  • "Specialty" (if recorded)
  • "Date of discharge" (e.g., "Self", specific date, or "Other care setting")
And all values are read-only
And the section header has a blue "Change" link
```

**Scenario 8: Conditional discharge location fields display correctly**
```gherkin
Given the pharmacy professional recorded ward and/or specialty information
When the Consultation Summary page displays the Discharge details section
Then:
  • If ward was recorded, it displays in the summary
  • If specialty was recorded, it displays in the summary
  • If other care setting details were recorded, they display
  • If no ward/specialty was recorded, these fields are omitted
```

### Medicine Information Section

**Scenario 9: Medicine information displays reconciliation summary**
```gherkin
Given a completed eligible DMR consultation with medicines reviewed
When the Consultation Summary page loads
Then the Medicine information section displays:
  • "Eligibility criteria" showing the selected criteria (e.g., "Medicines changed during admission")
  • "Medicines being taken after discharge" with a list of medicines (e.g., "Clarithromycin 500mg tablets, Amlodipine 10mg tablets...")
  • "Number of medicines on DAL" (e.g., "4")
  • "Number of medicines patient is taking" (e.g., "4")
  • "Number of discrepancies identified" (e.g., "1")
  • "Discrepancies identified" with details if any exist
  • "Sensitive information" showing "Yes" or "No"
And all values are read-only
```

**Scenario 10: Discrepancies are displayed with full details**
```gherkin
Given the pharmacy professional identified medicines with discrepancies
When the Consultation Summary page displays the Medicine information section
Then for each discrepancy, the system displays:
  • Medicine name (e.g., "Amlodipine 10mg tablets")
  • Nature of discrepancy (e.g., "Continued but at a wrong dose")
  • Next step (e.g., "Refer to GP")
And all discrepancy information is read-only
And the "Change" link allows editing the Medicines Reconciliation page
```

### Information Governance Section

**Scenario 11: Information governance section displays consent and communication decisions**
```gherkin
Given a completed eligible DMR consultation
When the Consultation Summary page loads
Then the Information governance section displays:
  • "GP communication requested" showing "Yes" or "No"
  • If "No" is selected: "Reason consultation summary will not be sent to GP" with recorded reason
  • "Sensitive health information" showing "Yes" or "No"
And all values are read-only
```

### Change Functionality

**Scenario 12: Change link returns to appropriate section with data retained**
```gherkin
Given the Consultation Summary page displays all sections
When the user clicks "Change" on any section
Then the system navigates to the corresponding edit page
And all previously recorded data is pre-populated in the fields
And the user can modify the data
And when the user completes their changes and continues, they return to the Consultation Summary page
And the updated values are displayed in the summary
```

**Scenario 13: Multiple sections can be edited during summary review**
```gherkin
Given the Consultation Summary page is displayed
When the user clicks "Change" on the Consultation details section and edits the GPhC
And then clicks "Change" on the Discharge details section and edits the hospital
And returns to the Consultation Summary page
Then all changes are retained
And the updated values are displayed
And the summary remains in a reviewable state for further changes
```

### Navigation

**Scenario 14: Previous button returns to last consultation page**
```gherkin
Given the Consultation Summary page is displayed
When the user clicks the "Previous" button
Then the system returns to the most recent consultation page (e.g., Medicines Reconciliation page)
And all recorded information is retained
And the user can navigate forward again to the summary
```

**Scenario 15: Cancel and exit saves information and exits consultation**
```gherkin
Given the Consultation Summary page is displayed
When the user clicks "Cancel and exit"
Then the system saves all consultation information according to consultation save rules
And the system marks the consultation as "In Progress"
And the user is returned to the Patient Overview page
And the consultation can be resumed later
```

### Validation

**Scenario 16: Complete button requires all required sections to have data**
```gherkin
Given the Consultation Summary page is displayed with complete data
When the user clicks "Complete part 1"
Then the system validates that all required sections have recorded data
And if validation passes, the consultation completion process begins
And if validation fails, appropriate error messages display
```

**Scenario 17: Summary page prevents accidental data loss**
```gherkin
Given the pharmacy professional has navigated through the summary making changes
When they attempt to navigate away without completing or saving
Then the system displays a confirmation dialog
And prompts "Are you sure you want to exit? Your changes may be lost."
And provides "Continue" and "Stay" options
```

### Page Layout and Styling

**Scenario 18: Summary sections display with consistent styling**
```gherkin
Given the Consultation Summary page is rendered
Then each section displays:
  • A light gray background (DHCW Design System V2 card style)
  • Section heading in bold text
  • Field labels on the left with values on the right
  • Equal padding and margins
  • A blue "Change" link aligned to the right
And the page uses a consistent font and color scheme throughout
```

**Scenario 19: Read-only values display in appropriate format**
```gherkin
Given the Consultation Summary page displays all sections
Then all displayed values:
  • Do not have input field styling
  • Are displayed in plain text format
  • Cannot be directly edited by clicking on the value
  • Can only be edited via the "Change" link
  • Are properly formatted based on data type (dates, lists, etc.)
```

### Responsive Design

**Scenario 20: Summary page displays correctly on different screen sizes**
```gherkin
Given the Consultation Summary page is displayed
When viewed on mobile (375px), tablet (768px), or desktop (1024px+)
Then the sections stack vertically on mobile
And maintain readability on all screen sizes
And the "Change" links remain accessible
And the action buttons remain clickable and properly spaced
And the page does not require horizontal scrolling
```
