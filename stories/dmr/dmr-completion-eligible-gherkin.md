# DMR: Completion Page - Eligible Pathway

## Description

### Story Card
**As a pharmacy professional completing an eligible Discharge Medicines Review**
**I want to receive a clear success confirmation with key consultation details**
**So that I have assurance the consultation has been successfully recorded and can access next steps**

### Context
The Completion page is displayed after the pharmacy professional clicks "Complete part 1" on the Consultation Summary page. This page provides confirmation that the eligible DMR Part 1 has been successfully completed, displays the consultation reference number and completion details, and provides options for next actions.

### Out of Scope
- GP communication failure messaging (displayed separately)
- DMR Part 2 initiation
- Consultation history detail views
- Patient-facing components
- Printing functionality implementation

### Related / Dependent Stories
- US 623544: UI: DMR - Consultation Summary (Eligible pathway)
- US 623545: UI: DMR - Consultation Summary (Non-Eligible pathway)
- US 623546: UI: DMR - Completion Page (Non-Eligible pathway)

### Design Reference
Figma: Choose Pharmacy Current (Re)Designs – DMR Completion pages
DHCW Design System V2: Success messaging pattern (green background, checkmark icon)

---

## Acceptance Criteria

### Default State - Success Message

**Scenario 1: Completion page displays success confirmation for eligible pathway**
```gherkin
Given the pharmacy professional has clicked "Complete part 1" on the Consultation Summary
And all validation passes
When the Completion page loads
Then the page displays a green success banner at the top
And the banner contains a white checkmark icon
And the banner displays the heading "Part 1 complete"
And the banner displays supporting text: "The first part of the Discharge Medicines Review has been successfully completed and recorded."
And the banner background uses DHCW Design System V2 success color (green)
```

**Scenario 2: Completion page displays consultation reference and patient details**
```gherkin
Given the Completion page is displayed after successful consultation completion
When the page renders
Then below the success banner, the page displays:
  • "Consultation reference number" with the generated reference (e.g., "DMR-2026-08-12345")
  • "Patient details" with the patient name (e.g., "ANDERSON, Mary (Ms)")
  • "Consultation completed" with date and time (e.g., "14 August 2026 at 13:45")
And all details are displayed in a read-only format
```

**Scenario 3: Completion details are properly formatted and aligned**
```gherkin
Given the Completion page displays the consultation details section
Then the details display in a structured format:
  • Field labels aligned on the left
  • Field values aligned on the right
  • Consistent spacing and padding
  • A light background (gray card) to distinguish from the page background
```

### Page Actions and Navigation

**Scenario 4: Completion page provides navigation options**
```gherkin
Given the Completion page is displayed after successful consultation completion
When the page fully loads
Then the page displays two action buttons:
  • "Return to Patient Overview" button
  • "View Consultation History" link or button
And both actions are clearly labeled and accessible
```

**Scenario 5: Return to Patient Overview navigation works correctly**
```gherkin
Given the pharmacy professional is viewing the Completion page
When they click the "Return to Patient Overview" button
Then the system returns to the Patient Overview page for the same patient
And the consultation is marked as completed in the system
And the user can view the consultation in the consultation history
```

**Scenario 6: View Consultation History navigation works correctly**
```gherkin
Given the pharmacy professional is viewing the Completion page
When they click "View Consultation History"
Then the system navigates to the Consultation History tab/page
And the completed consultation is displayed in the history list
And the consultation shows the completed date, time, and reference number
```

### GP Communication Messaging

**Scenario 7: Completion page displays GP communication success when applicable**
```gherkin
Given the pharmacy professional selected "Send Consultation Summary to GP" = Yes
And the GP communication was successful
When the Completion page displays
Then below the main success banner, an additional message displays:
  • Green checkmark icon
  • Text: "Summary Letter Sent to GP"
And both success messages display (Part 1 Complete AND Summary Letter Sent to GP)
```

**Scenario 8: Completion page displays GP communication failure when applicable**
```gherkin
Given the pharmacy professional selected "Send Consultation Summary to GP" = Yes
And the GP communication failed
When the Completion page displays
Then below the main success banner, an additional message displays:
  • Yellow/orange warning icon
  • Text: "GP consultation summary could not be sent electronically"
  • Supporting text: "If the information is clinically important, please follow your locally agreed contingency process for communicating with GP practices."
And the consultation is still marked as completed
```

**Scenario 9: Completion page does not show GP messaging when GP communication not requested**
```gherkin
Given the pharmacy professional selected "Send Consultation Summary to GP" = No
When the Completion page displays
Then only the standard completion message displays:
  • "Part 1 complete" with supporting text
And no GP communication messaging appears
And no GP-related links or buttons are displayed
```

### Optional Actions

**Scenario 10: Print Consultation option is available where configured**
```gherkin
Given the Completion page is displayed
And printing is enabled for the pharmacy
When the page loads
Then a "Print Consultation" button may display below the completion details
And clicking the button opens the print dialog for the consultation
And the consultation information is formatted for printing
```

**Scenario 11: Get Leaflets action provides patient information resources**
```gherkin
Given the Completion page is displayed
When the "Get leaflets" or "Access Patient Information Leaflets" option displays
Then clicking the action provides access to DMR-related patient information materials
And the materials can be printed or downloaded
And the action record is logged in the audit trail
```

### Page Layout and Styling

**Scenario 12: Completion page layout is clean and easily scannable**
```gherkin
Given the Completion page is displayed
Then the page layout includes:
  • Full-width green success banner at top
  • Centered content below the banner
  • Proper whitespace between sections
  • Clear visual hierarchy with headings and labels
  • Consistent DHCW Design System V2 styling
```

**Scenario 13: Text content is properly formatted and readable**
```gherkin
Given the Completion page displays
Then all text:
  • Uses appropriate font sizes (larger for headings, standard for body text)
  • Has sufficient contrast against backgrounds
  • Is properly left-aligned or right-aligned as applicable
  • Does not have spelling or grammatical errors
  • Displays in the correct language (English/Welsh as applicable)
```

### Data Recording and Persistence

**Scenario 14: Consultation reference number is generated and displayed**
```gherkin
Given the consultation completion is successful
When the Completion page displays
Then a unique consultation reference number is:
  • Generated by the system in format "DMR-YYYY-MM-XXXXX"
  • Stored in the consultation record
  • Displayed on the Completion page
  • Available in the consultation history
  • Included in any printed or exported consultation documents
```

**Scenario 15: Completion timestamp is recorded accurately**
```gherkin
Given the pharmacy professional initiates consultation completion
When the completion process completes successfully
Then the system records:
  • Date of completion (YYYY-MM-DD format)
  • Time of completion (HH:MM format)
  • User ID of the person completing the consultation
  • All consultation data at the time of completion
And this information is displayed on the Completion page
```

### Responsive Design

**Scenario 16: Completion page displays correctly on all screen sizes**
```gherkin
Given the Completion page is displayed
When viewed on mobile (375px), tablet (768px), or desktop (1024px+)
Then the success banner displays clearly and fills the screen width
And the completion details remain readable and properly formatted
And action buttons are easily clickable on all device sizes
And the page does not require horizontal scrolling
```

**Scenario 17: Mobile view maintains proper spacing and readability**
```gherkin
Given the Completion page is displayed on a mobile device
Then the page maintains:
  • Adequate margins from screen edges
  • Proper line spacing for readability
  • Large enough touch targets for buttons (minimum 44px height)
  • Proper color contrast for all text
  • No overlapping elements
```

### Accessibility Considerations

**Scenario 18: Success icon is accessible to screen readers**
```gherkin
Given the Completion page displays the green checkmark icon
When a screen reader reads the page
Then the checkmark icon has proper alt text or aria-label
And the success message is conveyed through text content, not just the icon
```

**Scenario 19: Navigation links are properly labeled and structured**
```gherkin
Given the Completion page displays navigation options
Then each action button or link:
  • Has descriptive text (not "Click here" or "Link")
  • Is focusable via keyboard navigation
  • Displays a focus indicator when focused
  • Can be activated via Enter key
```

### Error Handling

**Scenario 20: Page handles loading errors gracefully**
```gherkin
Given the Completion page is attempting to load
When a data retrieval error occurs
Then an error message displays explaining the issue
And the user is provided with options to:
  • Retry the operation
  • Return to Patient Overview
  • Contact support
And the error is logged in the audit trail
```
