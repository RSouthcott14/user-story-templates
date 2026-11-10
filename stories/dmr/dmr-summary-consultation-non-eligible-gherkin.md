# DMR: Consultation Summary - Non-Eligible Pathway

## Description

### Story Card
**As a pharmacy professional conducting a Discharge Medicines Review on a non-eligible patient**
**I want to review a summary of the consultation information and recorded outcome**
**So that I can verify the details are correct before completing the non-eligible consultation**

### Context
When a patient is determined to be ineligible for the full DMR process, the pharmacy professional completes a non-eligible consultation pathway. The Consultation Summary page for this pathway displays all information captured during the consultation, including the referral, advice, or end consultation outcome recorded.

### Out of Scope
- Full eligible pathway summary components
- GP communication triggering and outcomes
- Complex discrepancy management features
- DMR Part 2 initiation

### Related / Dependent Stories
- Non-Eligible Referral Outcome recording
- Non-Eligible Advice Outcome recording
- Non-Eligible End Consultation outcome

### Design Reference
Figma: Choose Pharmacy Current (Re)Designs – DMR Summary pages (Non-Eligible pathway)
DHCW Design System V2: Standard section cards with Change links

---

## Acceptance Criteria

### Default State

**Scenario 1: Non-eligible summary page displays all applicable sections**
```gherkin
Given a pharmacy professional has completed all steps for a non-eligible DMR
When they navigate to the Consultation Summary page
Then the page displays the title "Consultation summary - part 1"
And the page displays sections applicable to the non-eligible pathway:
  • Consultation details
  • Discharge details (if recorded)
  • Outcome information (Referral, Advice, or End Consultation)
  • Information governance
And each section displays a "Change" link aligned to the right
And the page displays "Previous" and "Complete consultation" action buttons
And the page displays a "Cancel and exit" link
```

**Scenario 2: Non-eligible summary displays only relevant outcome section**
```gherkin
Given a non-eligible consultation with recorded outcome
When the Consultation Summary page loads
Then only ONE outcome section displays based on the consultation pathway:
  • "Referral Outcome" if patient was referred, OR
  • "Advice Outcome" if advice was provided, OR
  • "End Consultation Outcome" if consultation ended without referral/advice
And only the applicable section is shown (never multiple outcomes)
And the page layout adjusts accordingly
```

### Consultation Details Section

**Scenario 3: Consultation details displays all recorded information**
```gherkin
Given a completed non-eligible DMR consultation
When the Consultation Summary page loads
Then the Consultation details section displays:
  • "GPhC" with pharmacist registration number
  • "Start date and time of the consultation"
  • "Discharge information provided by" (e.g., "Hospital", "Community care")
  • "How was the consultation provided?"
And all values are displayed as recorded and are read-only
```

### Discharge Details Section (Conditional)

**Scenario 4: Discharge details displays when information was recorded**
```gherkin
Given discharge information was recorded for the non-eligible consultation
When the Consultation Summary page loads
Then the Discharge details section displays:
  • "Place of discharge" with hospital details or other care setting
  • "Ward" (if applicable)
  • "Specialty" (if applicable)
  • "Date of discharge"
And the section can be edited via the "Change" link
```

**Scenario 5: Discharge details section is omitted if no information recorded**
```gherkin
Given a non-eligible consultation where no discharge details were entered
When the Consultation Summary page loads
Then the Discharge details section is not displayed
And no empty sections appear in the summary
And the page layout remains clean without gaps
```

### Referral Outcome Section

**Scenario 6: Referral outcome displays complete referral information**
```gherkin
Given a non-eligible consultation where patient was referred
When the Consultation Summary page loads
Then the Referral Outcome section displays:
  • "Referral Destination" (e.g., "GP", "Hospital Specialist", "Community service")
  • "Referral Timescale" (e.g., "Urgent", "Within 2 weeks", "Routine")
  • "Referral notes" or free text entered by the professional
And all information is read-only
And the section header has a blue "Change" link for editing
```

**Scenario 7: Referral outcome can be amended via Change link**
```gherkin
Given the Consultation Summary page displays the Referral Outcome section
When the user clicks "Change"
Then the system returns to the Referral outcome recording page
And all previously entered referral details are pre-populated
And the user can modify any field
And upon saving, returns to the Consultation Summary with updated values
```

### Advice Outcome Section

**Scenario 8: Advice outcome displays recorded advice details**
```gherkin
Given a non-eligible consultation where advice was provided
When the Consultation Summary page loads
Then the Advice Outcome section displays:
  • "Advice Provided to Patient" with the recorded advice text
  • "Advice Category" or type (if applicable)
And the advice is displayed as entered (read-only)
And the section can be edited via the "Change" link
```

**Scenario 9: Advice section displays free-text advice clearly**
```gherkin
Given the pharmacy professional recorded detailed advice for the patient
When the Consultation Summary page displays the Advice Outcome section
Then the advice text displays:
  • In a clearly readable format (not truncated)
  • With proper line breaks preserved
  • In a distinct visual area to separate from other content
  • Supporting character count information if previously displayed
```

### End Consultation Outcome Section

**Scenario 10: End consultation outcome displays confirmation**
```gherkin
Given a non-eligible consultation that ended without referral or advice
When the Consultation Summary page loads
Then the End Consultation Outcome section displays:
  • Confirmation message: "Consultation ended without referral or advice"
  • Date and time of consultation end (automatic from system)
And this information is read-only
```

### Information Governance Section

**Scenario 11: Information governance section displays for all pathways**
```gherkin
Given any non-eligible consultation (referral, advice, or end)
When the Consultation Summary page loads
Then the Information Governance section displays:
  • "GP communication requested" showing "Yes" or "No"
  • If "No": "Reason consultation summary will not be sent to GP" with recorded reason
  • "Sensitive health information" showing "Yes" or "No"
And all values are read-only
```

### Change Functionality

**Scenario 12: Multiple sections can be edited independently**
```gherkin
Given the Consultation Summary page is displayed with all sections
When the user clicks "Change" on the Outcome section and makes edits
And saves those changes
Then the summary returns with updated outcome information
And the user can then click "Change" on another section without losing the outcome changes
And all changes are retained through the consultation completion process
```

**Scenario 13: Change actions preserve all previously recorded data**
```gherkin
Given the pharmacy professional made multiple edits during the summary review
When they navigate back to the Consultation Summary from an edit page
Then all previously recorded information (in all sections) is retained
And the updated values display in their respective sections
And the consultation remains in a reviewable state
```

### Validation

**Scenario 14: Complete button validates outcome has been recorded**
```gherkin
Given the Consultation Summary page is displayed
When the user clicks "Complete consultation"
Then the system validates that an outcome has been recorded:
  • Either a Referral, Advice, or End Consultation outcome exists
  • And required fields within that outcome are populated
If validation passes, consultation completion proceeds
If validation fails, error message displays and user remains on summary
```

**Scenario 15: Sensitive health information must be recorded**
```gherkin
Given a non-eligible consultation summary
When the system validates before completion
Then the system checks that "Sensitive health information" field has a value
And if not recorded, an error message displays prompting the user to record this information
And the user cannot complete the consultation without providing this answer
```

### Navigation

**Scenario 16: Previous button returns to last consultation page**
```gherkin
Given the Consultation Summary page is displayed
When the user clicks "Previous"
Then the system returns to the most recent consultation page
And all recorded data is retained
And the user can navigate forward again to the summary
```

**Scenario 17: Cancel and exit functionality**
```gherkin
Given the Consultation Summary page is displayed
When the user clicks "Cancel and exit"
Then the system saves all consultation information
And marks the consultation as "In Progress"
And returns the user to the Patient Overview page
And the consultation can be resumed later
```

### Page Layout and Styling

**Scenario 18: Non-eligible summary displays with consistent styling**
```gherkin
Given the Non-Eligible Consultation Summary page
Then all sections display with:
  • Light gray background (DHCW Design System V2 card style)
  • Clear section headings
  • Consistent field label and value formatting
  • Blue "Change" links aligned right
  • Equal padding and margins throughout
```

**Scenario 19: Conditional outcome section displays appropriately**
```gherkin
Given the Consultation Summary displays either Referral, Advice, or End Consultation outcome
Then the displayed outcome section:
  • Is visually distinct from other sections
  • Clearly shows which type of outcome was recorded
  • Displays all relevant fields for that outcome type
  • Does not show fields from other outcome types
```

### Responsive Design

**Scenario 20: Summary page adapts to different screen sizes**
```gherkin
Given the Consultation Summary page is displayed
When viewed on mobile (375px), tablet (768px), or desktop (1024px+)
Then all sections stack vertically on mobile
And the "Change" links remain accessible on all device sizes
And the page maintains readability
And action buttons remain properly spaced and clickable
And no horizontal scrolling is required
```
