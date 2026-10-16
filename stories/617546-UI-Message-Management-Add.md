# UI User Story: System Administration - Add/Edit Message

## Story Type: UI

---

## Description

### Story Card
**As a** Choose Pharmacy system administrator  
**I want** to create and manage system messages with rich formatting and role-based visibility  
**So that** I can communicate important announcements, maintenance notifications, and role-specific alerts to platform users

### Context
System administrators need the ability to compose structured messages with rich text formatting (bold, italic, links, lists, etc.) and control message visibility by user role and date range. Messages can be active during specific time windows and targeted to different pharmacy roles. This form-based interface provides all necessary fields and tools for creating comprehensive system communications.

The Add/Edit Message screen provides:
- **Message metadata** — Title, Priority, Summary, Start/End dates
- **Message visibility** — Role-based targeting (CAS Pharmacist Admin, CAS Pharmacist)
- **Rich text editor** — WYSIWYG content editing with formatting toolbar
- **Date/time pickers** — Set message start and end dates
- **Form validation** — Ensure all required fields are completed
- **Save/Cancel actions** — Save message or discard changes
- **Responsive design** — Works seamlessly on desktop and tablet devices

### Out of Scope
- Message preview mode (viewing formatted message in edit screen)
- Message scheduling with calendar UI (dates entered directly)
- Bulk message creation/batch operations
- Message templates or template library
- Message attachment functionality (files, images, documents)
- Approval workflows or multi-step publishing
- Message versioning/comparison
- Concurrent editing or collaboration features
- Auto-save or draft functionality
- Message localization/translation
- Advanced formatting (custom CSS, embedded media)

### Related/Dependent Stories
- [617546-UI-Message-Management-List](./617546-UI-Message-Management-List.md) — Message list screen (navigates back to this)
- [Message API] — Backend API for creating/updating messages (separate story)
- [Authentication & Authorization] — Ensures only admins can access this form

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Component guidance:** [`COMPONENTS.md`](../COMPONENTS.md)

**Components used in this story:**
- [Header](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9567) — Application header with branding
- [Text Input](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6219-549) — Message Title and Summary fields
- [Select](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6242-6391) — Priority dropdown (Normal, Important, Critical)
- [Date Input](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4583) — Start Date/Time and End Date/Time pickers
- [Textarea](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6218-4824) — Summary field (could use Text Input or Textarea)
- [Checkboxes](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4008) — Visible to Roles checkboxes
- [Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475) — Save (primary) and Cancel (secondary) buttons
- [Error Summary](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4585) — Display validation errors
- [Inset Text](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4589) — Help text and formatting tips
- [Panel](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4590) — Section containers for grouping related fields
- [Back Link](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9492) — Back to message list link
- [Footer](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9566) — Application footer

**Mock-up:** `mock-designs/message-management-add-mock.html`

**Error messaging standard:** [`standards/UI-ERROR-MESSAGING.md`](../standards/UI-ERROR-MESSAGING.md)

---

## Acceptance Criteria

**Total Scenarios: 12**

### Page Structure & Content

**Scenario 1: Add Message form displays all required fields and sections**

```gherkin
Given the user navigates to Add New Message screen
When the page loads
Then the following elements are displayed:
  - Page header: "Add New Message" (or "Edit Message: [Title]" if editing)
  - Back link: "← Back to Messages"
  - Section heading: "New Message" with panel border
  - Message metadata section containing:
    - Label: "Message Title" with required indicator (*)
    - Text input field (placeholder: "Enter a message title")
    - Label: "Priority" with required indicator (*)
    - Select dropdown (default: "Please Select")
    - Label: "Summary" with required indicator (*)
    - Text input or textarea field (placeholder: "Brief summary of the message")
    - Label: "Start Date Time" with required indicator (*)
    - Date/time input field
    - Label: "Expiry Date Time" with required indicator (*)
    - Date/time input field
  - Visible to Roles section containing:
    - Label: "Visible to Roles" with required indicator (*)
    - Checkboxes:
      - ☐ CAS Pharmacist Admin
      - ☐ CAS Pharmacist
    - Help text: "Select the roles that will see this message"
  - Message Content section containing:
    - Label: "Message Content" with required indicator (*)
    - WYSIWYG editor toolbar with formatting options:
      - Format dropdown (Normal, Heading 1, Heading 2, etc.)
      - Bold button (B)
      - Italic button (I)
      - Underline button (U)
      - Bullet list button
      - Numbered list button
      - Link button
      - Link removal button
      - Clear formatting button
      - Horizontal rule button
    - Large text input area for message content (min-height 300px)
  - Action buttons at bottom:
    - "Cancel" button (secondary, gray styling)
    - "Save" button (primary, blue styling)
  - Footer with application information
And all required fields are marked with red asterisk (*)
And form labels use clear, descriptive text from design system
And form is organized into clear sections with visual separation
```

**Scenario 2: Edit Message form pre-populates with existing message data**

```gherkin
Given the user navigates to Edit Message screen for an existing message
When the page loads
Then the page title shows: "Edit Message: [Original Message Title]"
And all form fields are pre-populated with the message's current data:
  - Message Title field contains the current title
  - Priority dropdown shows the current priority (Normal/Important/Critical)
  - Summary field contains the current summary text
  - Start Date/Time shows the current start datetime
  - End Date/Time shows the current end datetime
  - Visible to Roles checkboxes are checked/unchecked based on current selection
  - Message Content WYSIWYG shows the formatted message content
And the "Save" button text remains "Save" (not "Update")
And the Back link navigates back to the Message List without saving
And all form fields behave identically to the Add Message form
```

### Default State

**Scenario 3: Form displays empty/default state for new message**

```gherkin
Given the user navigates to Add New Message screen
When the page loads for the first time
Then all text input fields are empty:
  - Message Title: empty
  - Summary: empty
  - Message Content: empty
And the Priority dropdown shows "Please Select" as placeholder
And date/time fields are empty (or show placeholder like "DD/MM/YYYY HH:MM")
And all Visible to Roles checkboxes are unchecked
And the WYSIWYG editor is empty and ready for input
And the "Save" button is enabled
And all required field indicators (*) are visible
```

### User Interaction

**Scenario 4: User enters message metadata and form captures input**

```gherkin
Given the user is on the Add Message form
And all fields are empty
When the user types "COVID-19 Testing Service" in the Message Title field
Then the text appears immediately in the Title field
When the user selects "Important" from the Priority dropdown
Then the dropdown closes
And "Important" is displayed as the selected value
When the user enters "New COVID testing location has opened" in Summary
Then the text is captured in the Summary field
When the user types "Starting 10 June, we now offer..." in Message Content
Then the text appears in the WYSIWYG editor
And all entered data is retained as the user moves between fields
```

**Scenario 5: User sets start and end dates and times**

```gherkin
Given the user is on the Add Message form
And the date/time fields are empty
When the user clicks the "Start Date Time" field
Then a date/time picker displays showing:
  - Calendar for month/year selection
  - Date day selection
  - Time input (hours and minutes)
When the user selects date: 10 June 2026, time: 09:00
Then the "Start Date Time" field displays: "10 Jun 2026 09:00"
When the user clicks the "Expiry Date Time" field
Then a date/time picker displays
When the user selects date: 15 June 2026, time: 17:00
Then the "Expiry Date Time" field displays: "15 Jun 2026 17:00"
And both dates are retained in the form
```

**Scenario 6: User selects visible roles with checkboxes**

```gherkin
Given the user is on the Add Message form
And all Visible to Roles checkboxes are unchecked
When the user clicks the "CAS Pharmacist Admin" checkbox
Then the checkbox becomes checked (✓)
And the selection is visually highlighted
When the user also clicks the "CAS Pharmacist" checkbox
Then both checkboxes are checked
And the selection is visually clear
When the user unchecks "CAS Pharmacist"
Then only "CAS Pharmacist Admin" remains checked
And the change is reflected immediately
```

**Scenario 7: User formats message content with WYSIWYG editor**

```gherkin
Given the user is on the Add Message form
And the Message Content field has focus
When the user types text into the editor: "This is a test message"
Then the text appears in the editor
When the user selects the text and clicks the Bold button (B)
Then the selected text becomes bold: **This is a test message**
When the user selects text and clicks the Italic button (I)
Then the selected text becomes italic: *This is a test message*
When the user clicks the Underline button (U)
Then the selected text becomes underlined
When the user places cursor at end of text and presses Enter, then clicks Bullet List
Then a bulleted list starts:
  • First bullet point
  • Second bullet point
When the user clicks the Link button and enters URL and text
Then a formatted link appears: [Link text](URL)
When the user selects text and clicks Clear Formatting
Then all formatting (bold, italic, underline) is removed from selection
And the toolbar buttons accurately reflect the formatting of selected text (appear pressed when formatting is applied)
```

**Scenario 8: User saves new message successfully**

```gherkin
Given the user is on the Add Message form
And the following data is entered:
  - Message Title: "Service Update - July 2026"
  - Priority: "Important"
  - Summary: "Important service updates for all pharmacies"
  - Start Date Time: "01 Jul 2026 08:00"
  - Expiry Date Time: "31 Jul 2026 17:00"
  - Visible to Roles: CAS Pharmacist Admin, CAS Pharmacist (both checked)
  - Message Content: "**Important Update** - Please review the following..."
When the user clicks the "Save" button
Then a loading indicator appears temporarily
And the form is disabled (preventing double-submit)
And the backend processes the form submission
And a success message appears: "Message 'Service Update - July 2026' was successfully created"
And the user is redirected to the Message Management List
And the new message appears in the list with correct data
```

**Scenario 9: User saves edited message successfully**

```gherkin
Given the user is editing an existing message
And the user modifies:
  - Message Title from "Old Title" to "Updated Title"
  - Summary text
  - Message Content
When the user clicks the "Save" button
Then a loading indicator appears temporarily
And the form is disabled
And a success message appears: "Message 'Updated Title' was successfully updated"
And the user is redirected to the Message Management List
And the updated message appears with new data
And the edit timestamp is updated on the message record
```

**Scenario 10: User cancels form entry without saving**

```gherkin
Given the user is on the Add Message form
And the following data is entered:
  - Message Title: "Unsaved Message"
  - Priority: "Important"
  - Summary: "This will be discarded"
  - Message Content: "Do not save"
When the user clicks the "Cancel" button
Then a confirmation dialog may appear (depending on UX preference): "You have unsaved changes. Are you sure you want to leave?"
  - "Cancel" button (secondary) - return to form
  - "Leave" or "Discard" button (destructive) - discard changes
When the user confirms leaving (or if no dialog, direct navigation)
Then the user is redirected to the Message Management List
And all entered data is discarded
And no message is created or modified
When the user clicks the Back link instead
Then the same cancel behavior occurs
And no data is persisted
```

### Navigation

**Scenario 11: Navigation flow between Message List and Add/Edit Message**

```gherkin
Given the user is on the Message Management List
When the user clicks "Add new message"
Then the user is navigated to the Add Message form
And the page title shows "Add New Message"
And all form fields are empty
And the Back link says "← Back to Messages"
When the user fills in the form and clicks "Save"
Then the message is created
And the user is redirected to the Message Management List
And the new message appears in the table
When the user clicks an Edit button on a message row
Then the user is navigated to the Edit Message form
And the form is pre-populated with message data
And the page title shows "Edit Message: [Title]"
When the user cancels from the Edit form
Then the user returns to the Message Management List
And all changes are discarded
```

### Validation

**Scenario 12: Form validation prevents submission with missing or invalid data**

```gherkin
Given the user is on the Add Message form
When the user clicks "Save" without filling any fields
Then the form displays validation errors:
  - Error banner at top: "Please fix the following errors before saving"
  - Error message next to "Message Title": "Message title is required"
  - Error message next to "Priority": "Priority must be selected"
  - Error message next to "Summary": "Summary is required"
  - Error message next to "Start Date Time": "Start date is required"
  - Error message next to "Expiry Date Time": "Expiry date is required"
  - Error message next to "Visible to Roles": "At least one role must be selected"
  - Error message next to "Message Content": "Message content is required"
And the form remains on the current page (not submitted)
And focus moves to the first error field
And error messages use red color with error icon

When the user enters Title but leaves other fields empty
And clicks "Save"
Then only the errors for empty required fields are displayed
And the Message Title field does not show an error

When the user enters a Message Title longer than 200 characters
And clicks "Save"
Then an error displays: "Message title must be 200 characters or less"
And focus returns to the Title field
And no data is persisted

When the user enters an Expiry Date before the Start Date
And clicks "Save"
Then an error displays: "Expiry date must be after the start date"
And focus returns to the Expiry Date field
And no data is persisted

When the user does not select any Visible to Roles checkboxes
And clicks "Save"
Then an error displays: "At least one role must be selected"
And focus returns to the Visible to Roles section
And no data is persisted

When the user enters all required fields correctly
And clicks "Save"
Then no validation errors appear
And the message is submitted to the backend
```

### Accessibility

**Scenario 13: WCAG 2.1 AA keyboard navigation and screen reader support**

```gherkin
Given the page is rendered
When a user navigates using only a keyboard
Then all interactive elements are reachable via Tab key:
  - Back link
  - Message Title input
  - Priority dropdown
  - Summary input
  - Start Date/Time input
  - End Date/Time input
  - Visible to Roles checkboxes (all)
  - Message Content editor
  - WYSIWYG toolbar buttons (all)
  - Cancel button
  - Save button
And the tab order is logical from top to bottom:
  1. Back link
  2. Message Title
  3. Priority dropdown
  4. Summary
  5. Start Date/Time
  6. End Date/Time
  7. Visible to Roles checkboxes (in order)
  8. Message Content editor
  9. WYSIWYG toolbar buttons (optional: can tab through or use keyboard shortcuts)
  10. Cancel button
  11. Save button
And each form field has a properly associated label:
  - Label is connected to input via `for` attribute or semantic nesting
  - Label text is descriptive and clear
  - Required indicator (*) is within the label text
And screen reader announces:
  - "Add New Message" or "Edit Message: [Title]" heading level 1
  - "Message Title, required, text input" when field focused
  - "Priority, required, select dropdown, currently showing Please Select" when dropdown focused
  - "Start Date Time, required, date and time input" when date field focused
  - Each checkbox: "CAS Pharmacist Admin, checkbox, unchecked" or "checked"
  - "Message Content, required, text editor" when editor focused
  - "Save button, primary action" when focused
And all error messages have role="alert" with aria-live="polite"
And error messages appear near the associated field
And colour contrast ratios meet WCAG AA standard (4.5:1 for normal text, 3:1 for large text)
  - Form labels (#1a1a1a) against white background: 14:1 ✓
  - Required indicator (*) in red: meets contrast for visibility ✓
  - Error messages in red: meet 4.5:1 minimum ✓
And all buttons have visible focus indicators (outline or background change)
And form sections are organized with semantic heading hierarchy (h2 for sections)
And the WYSIWYG editor toolbar buttons have aria-labels describing their function:
  - "Bold" for B button
  - "Italic" for I button
  - "Create bulleted list" for list button
  - etc.
And keyboard shortcuts are provided for common formatting:
  - Ctrl+B for Bold
  - Ctrl+I for Italic
  - Ctrl+U for Underline
And documentation or help text explains WYSIWYG editor keyboard usage
```

---

## Definition of Done

- [ ] All acceptance criteria met and validated by QA
- [ ] Code peer reviewed and approved
- [ ] Unit tests written (80%+ coverage)
- [ ] Integration tests for message creation/update API endpoints
- [ ] Accessibility testing completed (WCAG 2.1 AA)
- [ ] UI matches DHCW Design System V2 (Figma/mock-up provided)
- [ ] Browser compatibility verified (Chrome, Firefox, Safari, Edge)
- [ ] Responsive design tested (tablet: 768px, desktop: 1200px+)
- [ ] WYSIWYG editor functions correctly (formatting, links, lists)
- [ ] Date/time pickers work and save values correctly
- [ ] Priority dropdown shows correct options and saves selection
- [ ] Visible to Roles checkboxes function correctly (multi-select)
- [ ] Form validation tests all required fields
- [ ] Form validation tests field length constraints
- [ ] Form validation tests date logic (end date after start date)
- [ ] Error messages display with correct styling and placement
- [ ] Success messages display after save
- [ ] New message saves with all entered data
- [ ] Edit message updates existing message correctly
- [ ] Cancel/Back navigates without saving
- [ ] Navigation between Message List and Add/Edit form works
- [ ] WYSIWYG editor preserves formatting on save
- [ ] No console errors or warnings
- [ ] Keyboard navigation tested end-to-end
- [ ] Screen reader testing completed (NVDA/JAWS on Windows, VoiceOver on macOS)
- [ ] Performance: Form loads in under 2 seconds
- [ ] Mock-up artifact (`mock-designs/message-management-add-mock.html`) provided as reference
- [ ] Double-submit prevention implemented (button disabled during submission)

---

## Notes

### Design Reference
- Mock-up provided: `mock-designs/message-management-add-mock.html`
- DHCW Design System V2 Figma: https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2

### Form Specifications
- **Message Title:** Required, string, max 200 characters, no special characters restrictions
- **Priority:** Required, dropdown enum (Normal, Important, Critical)
- **Summary:** Required, string, max 500 characters (can be displayed in list view)
- **Start Date/Time:** Required, datetime, must be in future (validation on save)
- **End Date/Time:** Required, datetime, must be after Start Date/Time
- **Visible to Roles:** Required, multi-select checkboxes (min 1 required)
  - Options: CAS Pharmacist Admin, CAS Pharmacist
- **Message Content:** Required, rich text (WYSIWYG formatted)

### WYSIWYG Editor Formatting Support
Supported formatting options:
- Bold, Italic, Underline, Strikethrough
- Paragraph styles (Normal, Heading 1, Heading 2, Heading 3)
- Unordered (bullet) lists
- Ordered (numbered) lists
- Links (create/edit/remove)
- Horizontal rule
- Clear formatting
- Undo/Redo functionality
- Copy/Paste with formatting preserved

### Date/Time Picker Specifications
- Calendar picker for date selection (MM/DD/YYYY or DD/MM/YYYY based on locale)
- Time picker for hours (00-23) and minutes (00-59)
- Validation: End date/time must be after start date/time
- Dates stored in ISO 8601 format (YYYY-MM-DDTHH:MM:SS)

### Validation Rules Summary
1. All fields are required
2. Message Title: max 200 characters
3. Summary: max 500 characters
4. End Date/Time must be after Start Date/Time
5. At least one role must be selected
6. Message Content cannot be empty

### Error Message Standards
All error messages follow the format from `standards/UI-ERROR-MESSAGING.md`:
- Clear, user-friendly language
- Specific about the problem (not generic "error")
- Positioned near the problematic field
- Styled with error color (red) and icon
- Include any helpful guidance (e.g., "max 200 characters")
- Announced to screen readers with role="alert"

### Future Enhancements (Not in scope)
- Message preview before saving
- Auto-save/draft functionality
- Message scheduling with calendar view
- Message templates library
- File/image attachment support
- Advanced formatting (custom CSS, embedded media)
- Collaborative editing
- Message versioning/history
- Approval workflow
- Multi-language support

### Known Issues / Dependencies
- Requires authentication to ensure only system admins can access
- Backend API must support:
  - Create new message
  - Update existing message
  - Retrieve single message (for edit)
  - Validate message data on submission
  - Handle date/time formatting consistently
- WYSIWYG editor library dependency (e.g., TinyMCE, CKEditor, Draft.js)
- Date/time picker library dependency (e.g., Flatpickr, React Date Picker)
