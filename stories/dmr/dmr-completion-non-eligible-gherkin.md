# DMR: Completion Page - Non-Eligible Pathway

## Description

### Story Card
**As a pharmacy professional completing a non-eligible Discharge Medicines Review**
**I want to receive a clear confirmation that the consultation has been successfully recorded**
**So that I have assurance the consultation is complete and can access next steps based on the outcome**

### Context
The Completion page for the non-eligible DMR pathway is displayed after successful consultation completion. It confirms that the consultation record has been updated, displays the consultation reference number and completion details, and provides outcome-specific messaging (Referral, Advice, or End Consultation).

### Out of Scope
- DMR Part 2 initiation
- Referral tracking or follow-up systems
- Full eligible pathway completion messaging
- Patient-facing components
- Printing functionality implementation

### Related / Dependent Stories
- US 623547: UI: DMR - Consultation Summary (Non-Eligible pathway)
- US 623548: UI: DMR - Completion Page (Eligible pathway)

### Design Reference
Figma: Choose Pharmacy Current (Re)Designs – DMR Completion pages
DHCW Design System V2: Success messaging pattern (green background, checkmark icon)

---

## Acceptance Criteria

### Default State - Success Message

**Scenario 1: Completion page displays success confirmation for non-eligible pathway**
```gherkin
Given the pharmacy professional has clicked "Complete consultation" on the Non-Eligible Summary
And all validation passes
When the Completion page loads
Then the page displays a green success banner at the top
And the banner contains a white checkmark icon
And the banner displays the heading "Consultation complete"
And the banner displays supporting text: "The patient's consultation record has been updated."
And the banner background uses DHCW Design System V2 success color (green)
```

**Scenario 2: Completion page displays consultation reference and patient details**
```gherkin
Given the Completion page is displayed after successful non-eligible consultation completion
When the page renders
Then below the success banner, the page displays:
  • "Consultation reference number" with the generated reference (e.g., "DMR-2026-08-12345")
  • "Patient details" with the patient name (e.g., "ANDERSON, Mary (Ms)")
  • "Consultation completed" with date and time (e.g., "14 August 2026 at 13:45")
And all details are displayed in a read-only format
```

**Scenario 3: Completion details are properly formatted**
```gherkin
Given the Completion page displays the consultation details
Then the details display in a structured format:
  • Field labels on the left with values on the right
  • Consistent spacing between fields
  • Light background (gray card) for visual separation
  • All text clearly readable and properly formatted
```

### Outcome-Specific Messaging

**Scenario 4: Completion page displays Referral outcome message**
```gherkin
Given a non-eligible consultation where the patient was referred
When the Completion page displays
Then below the success banner, an outcome section displays:
  • Heading: "Consultation Outcome"
  • Message: "Outcome: Patient Referred"
  • The referral destination and timescale are displayed (e.g., "Referred to: GP - Urgent")
And the referral details are shown in a read-only format
```

**Scenario 5: Completion page displays Advice outcome message**
```gherkin
Given a non-eligible consultation where advice was provided
When the Completion page displays
Then below the success banner, an outcome section displays:
  • Heading: "Consultation Outcome"
  • Message: "Outcome: Advice Provided"
  • A summary of the advice given may be displayed
And the outcome is clearly identified
```

**Scenario 6: Completion page displays End Consultation outcome message**
```gherkin
Given a non-eligible consultation that ended without referral or advice
When the Completion page displays
Then below the success banner, an outcome section displays:
  • Heading: "Consultation Outcome"
  • Message: "Outcome: Consultation Ended"
  • Confirmation that no referral or advice was provided
And the outcome is clearly identified
```

**Scenario 7: Only one outcome type displays on completion page**
```gherkin
Given the Completion page is rendered after a non-eligible consultation
When the page displays outcome information
Then exactly ONE outcome type is displayed:
  • Either "Patient Referred", OR
  • "Advice Provided", OR
  • "Consultation Ended"
And the outcome matches the recorded consultation outcome
And no conflicting outcome messages are displayed
```

### Page Actions and Navigation

**Scenario 8: Completion page provides navigation options**
```gherkin
Given the Completion page is displayed after successful consultation completion
When the page fully loads
Then the page displays at least two action options:
  • "Return to Patient Overview" button
  • "View Consultation History" link or button
And both actions are clearly labeled and accessible
```

**Scenario 9: Return to Patient Overview navigation**
```gherkin
Given the pharmacy professional is viewing the Completion page
When they click "Return to Patient Overview"
Then the system returns to the Patient Overview page for the same patient
And the consultation is marked as completed
And the user can view the completed consultation in the history
```

**Scenario 10: View Consultation History navigation**
```gherkin
Given the pharmacy professional is viewing the Completion page
When they click "View Consultation History"
Then the system navigates to the Consultation History view
And the completed non-eligible consultation appears in the history list
And the consultation reference number and completion details are visible
```

### Optional Actions

**Scenario 11: Print Consultation option may be available**
```gherkin
Given the Completion page is displayed
And printing is enabled for the pharmacy
When the page loads
Then a "Print Consultation" button may be displayed
And clicking the button opens the print dialog
And the consultation summary is formatted for printing
```

**Scenario 12: Get Leaflets action provides additional resources**
```gherkin
Given the Completion page is displayed
When the "Get leaflets" action displays
Then it provides access to patient information materials
And the user can print or download the materials
And the action is logged in the audit trail
```

### GP Communication Messaging

**Scenario 13: GP communication success displays when applicable**
```gherkin
Given the pharmacy professional selected "Send Consultation Summary to GP" = Yes
And GP communication was successful
When the Completion page displays
Then an additional message displays:
  • Green checkmark icon
  • Text: "Summary Letter Sent to GP"
And both success messages are visible:
  • "Consultation Complete"
  • "Summary Letter Sent to GP"
```

**Scenario 14: GP communication failure displays when applicable**
```gherkin
Given the pharmacy professional selected "Send Consultation Summary to GP" = Yes
And GP communication failed
When the Completion page displays
Then an additional message displays:
  • Warning icon (yellow/orange)
  • Text: "GP consultation summary could not be sent electronically"
  • Supporting text: "If the information is clinically important, please follow your locally agreed contingency process for communicating with GP practices."
And the consultation is still marked as completed
And the failure is recorded in audit history
```

**Scenario 15: No GP messaging when GP communication not requested**
```gherkin
Given the pharmacy professional selected "Send Consultation Summary to GP" = No
When the Completion page displays
Then only the standard success message displays:
  • "Consultation Complete"
And no GP communication messaging appears
And no GP-related actions or links are displayed
```

### Page Layout and Styling

**Scenario 16: Completion page has clean, scannable layout**
```gherkin
Given the Completion page is displayed
Then the page layout includes:
  • Full-width green success banner at the top
  • Centered content below the banner
  • Proper whitespace between sections
  • Clear visual hierarchy
  • Consistent DHCW Design System V2 styling throughout
```

**Scenario 17: Outcome information displays clearly**
```gherkin
Given the Completion page displays outcome information
Then the outcome section:
  • Is clearly labeled with "Consultation Outcome" heading
  • Shows the outcome type prominently (Referred, Advice, or Ended)
  • Displays any supporting details appropriately formatted
  • Is visually distinct from other page sections
```

### Data Recording and Persistence

**Scenario 18: Consultation reference is generated and stored**
```gherkin
Given the consultation completion is successful
When the Completion page displays
Then a unique consultation reference number:
  • Is generated in format "DMR-YYYY-MM-XXXXX"
  • Is displayed on the Completion page
  • Is stored in the consultation record
  • Appears in consultation history
  • Is available for future reference
```

**Scenario 19: Completion timestamp is accurately recorded**
```gherkin
Given the pharmacy professional initiates consultation completion
When the completion process succeeds
Then the system records:
  • Date of completion (YYYY-MM-DD format)
  • Time of completion (HH:MM format)
  • User ID of the completing professional
  • All consultation data at time of completion
And this information displays on the Completion page
```

**Scenario 20: Outcome information is persisted**
```gherkin
Given a non-eligible consultation with referral, advice, or end outcome
When the consultation is completed
Then the outcome information:
  • Is stored in the consultation record
  • Is displayed on the Completion page
  • Is retained in consultation history
  • Is available in audit records
  • Is not modifiable after completion
```

### Responsive Design

**Scenario 21: Completion page displays correctly on all screen sizes**
```gherkin
Given the Completion page is displayed
When viewed on mobile (375px), tablet (768px), or desktop (1024px+)
Then the success banner displays clearly and fills screen width
And completion details remain readable
And outcome information is clearly displayed
And action buttons are easily clickable on all devices
And the page does not require horizontal scrolling
```

**Scenario 22: Mobile view maintains accessibility and usability**
```gherkin
Given the Completion page is displayed on a mobile device
Then the page maintains:
  • Adequate margins from screen edges
  • Proper text line spacing for readability
  • Large touch targets for buttons (minimum 44px height)
  • Proper color contrast for all text
  • No overlapping or hidden elements
```

### Error Handling

**Scenario 23: Page handles data loading errors gracefully**
```gherkin
Given the Completion page is loading
When a data retrieval error occurs
Then an error message displays explaining the issue
And the user is provided with options to:
  • Retry the operation
  • Return to Patient Overview
  • Contact support
And the error is recorded in the audit trail
```

**Scenario 24: Completion page validates all required data is present**
```gherkin
Given the Completion page is being rendered
When validating the consultation data
Then the system verifies:
  • Consultation reference number exists
  • Patient details are available
  • Outcome information is recorded
  • Completion timestamp is set
If validation fails, appropriate error handling occurs
```
