# UI User Story: User Profile Page

## Story Type: UI

---

## Description

### Story Card
**As a** User (Pharmacist, Pharmacy Technician, Trainee Pharmacist, or Administrator)  
**I want** to view and manage my profile information  
**So that** I can see my verified identity details, update my contact preferences, and view my professional accreditations

### Context
The User Profile page provides a central location for users to review their identity information retrieved from Microsoft Entra ID, update their preferred contact telephone number, and view their NHS service accreditations sourced from the Welsh Regulatory and Training Service (WRTS) feed. Users access this page by clicking their user name displayed in the top right corner of the application header.

### Out of Scope
- Editing identity information (read-only from Entra ID)
- Password/security management (handled by Entra ID)
- Changing professional registration status
- Bulk update of accreditations
- Integration with other profile pages or patient records

### Related/Dependent Stories
- [603501]: Microsoft Entra ID Integration — blocks (requires authentication first)
- [603502]: WRTS Feed Integration — depends on (retrieves accreditation data)
- [609590]: User Profile - Edit Contact Number — relates to (optional future enhancement)

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Template:** [`templates/UI-Template.md`](templates/UI-Template.md)

**Component guidance:** [`COMPONENTS.md`](COMPONENTS.md)

#### Components Used:
- **Header** — [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9567) — Application header with user menu
- **Back Link** — [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9492) — Navigate back to previous page
- **Summary List** — [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-77) — Display identity information (key-value pairs)
- **Panel** — [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4590) — Grouped content sections for contact info and accreditations
- **Text Input** — [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6219-549) — Editable preferred contact number field
- **Buttons** — [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475) — Save and Cancel actions
- **Tag** — [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-78) — Service accreditation status badges
- **Inset Text** — [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4589) — Contextual help text and explanations
- **Notification Banners** — [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6610-4602) — Success confirmation for saved changes
- **Error Summary** — [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4585) — Validation error messages

---

## Acceptance Criteria

### Scenario 1: Page Structure & Content
**Title**: User Profile page displays identity, contact, and accreditation information

```gherkin
Given the user navigates to the User Profile page
When the page loads
Then the following elements are displayed:
  - Header (DHCW Design System V2) at the top with "Choose Pharmacy" logo and user name
  - Back Link (DHCW Design System V2) to return to previous page
  - Page heading: "Your Profile"
  - Summary List (DHCW Design System V2) with identity information:
    - Full Name (read-only, from Entra ID)
    - Email Address (read-only, from Entra ID)
    - GPHC Registration Number (read-only, from Entra ID)
    - GPHC Registration Type (read-only, from Entra ID)
  - Panel (DHCW Design System V2) section titled "Contact Information":
    - Label: "Preferred Contact Telephone Number"
    - Text Input (DHCW Design System V2) field (editable)
    - Current saved number displayed as default value
    - Inset Text (DHCW Design System V2) stating "This number may be used to contact you regarding consultations or service updates"
    - Save Button (DHCW Design System V2 Primary) to persist changes
    - Cancel Button (DHCW Design System V2 Secondary) to discard changes
  - Panel (DHCW Design System V2) section titled "Professional Accreditations":
    - List of NHS services user is accredited to perform (from WRTS feed)
    - Each service displayed as a Tag (DHCW Design System V2) with service name and status
    - Inset Text (DHCW Design System V2) stating "Accreditation information is updated from the Welsh Regulatory and Training Service (WRTS)"
And the layout follows a single-column responsive design
And all text uses standard typography from DHCW Design System V2
```

### Scenario 2: Default State
**Title**: User Profile page displays with read-only identity information and current contact details

```gherkin
Given the user navigates to the User Profile page
When the page loads for the first time
Then the Summary List (DHCW Design System V2) displays:
  - Full Name field showing "[User's Full Name]" (read-only, greyed out)
  - Email Address field showing "[User's Email]" (read-only, greyed out)
  - GPHC Registration Number field showing "[GPHC Number]" (read-only, greyed out)
  - GPHC Registration Type field showing "[e.g., Pharmacist, Technician, Trainee]" (read-only, greyed out)
And the Text Input (DHCW Design System V2) for Preferred Contact Telephone Number shows:
  - The user's currently saved telephone number (or placeholder "Add a contact number" if not set)
  - The input field is enabled and ready for editing
And the Professional Accreditations section displays:
  - All services retrieved from WRTS feed as Tag (DHCW Design System V2) components
  - Each tag displays service name with "Active" or "Inactive" status indicator
  - If no accreditations exist, display message "No accreditations currently recorded"
And the Save Button (DHCW Design System V2) is enabled
And the Cancel Button (DHCW Design System V2) is disabled (greyed out) on initial load
And no error messages are displayed
```

### Scenario 3: User Interaction - Update Contact Number
**Title**: User updates their preferred contact telephone number

```gherkin
Given the user is on the User Profile page
And the Text Input (DHCW Design System V2) for Preferred Contact Telephone Number contains "[Old Phone Number]"
When the user clicks in the telephone number field
And the user clears the existing text
And the user enters a new valid telephone number "[New Phone Number]"
Then the Text Input (DHCW Design System V2) field updates to show "[New Phone Number]"
And the Cancel Button (DHCW Design System V2 Secondary) becomes enabled
And the Save Button (DHCW Design System V2 Primary) becomes active
And visual feedback (underline or highlight) indicates the field has been modified
When the user clicks the Save Button (DHCW Design System V2)
Then the page displays a Notification Banner (DHCW Design System V2 - Success):
  - Message: "Your contact number has been updated successfully"
  - Duration: 5 seconds auto-dismiss or manual close
And the Text Input (DHCW Design System V2) field retains the new telephone number
And the Cancel Button (DHCW Design System V2 Secondary) becomes disabled
And the backend API persists the new contact number to user profile
```

### Scenario 4: Navigation
**Title**: User navigates from and to the User Profile page

```gherkin
Given the user is viewing the User Profile page
When the user clicks the Back Link (DHCW Design System V2) at the top
Then the user is navigated to the previously visited page
And the user's scroll position is maintained (if applicable)
And the page from which they came is refreshed if it contains user-related data

Given the user has clicked on their user name in the Header (DHCW Design System V2) top-right
When the dropdown menu displays "Your Profile" option
Then clicking "Your Profile" navigates directly to the User Profile page
And the URL changes to "/profile" or equivalent routing

Given the user is on the User Profile page
When the user closes their browser or navigates to another part of the application
And the user had unsaved changes to the telephone number
Then a browser confirmation dialog appears: "You have unsaved changes. Do you want to leave?"
And only unsaved changes trigger this warning (read-only fields do not)
```

### Scenario 5: Validation - Contact Number
**Title**: User enters invalid telephone number format

```gherkin
Given the user is on the User Profile page
And the Text Input (DHCW Design System V2) for Preferred Contact Telephone Number is in focus
When the user enters an invalid telephone number such as "[Invalid Format]" (fewer than 10 digits or non-numeric characters)
And the user clicks the Save Button (DHCW Design System V2)
Then an Error Summary (DHCW Design System V2) displays:
  - Error message: "Telephone number must be a valid UK format (11 digits starting with 0 or +44)"
  - Error location: Highlighted above the Text Input field
And the Error Summary (DHCW Design System V2) includes a link to the problematic field
When the user clicks on the error link
Then focus is moved to the Text Input (DHCW Design System V2) field
And the field is highlighted with a red left border (error state)
And the page remains on the User Profile page
And the invalid number is not persisted to the database

Given the user has entered a valid telephone number
When the user clicks the Cancel Button (DHCW Design System V2 Secondary)
Then the Text Input (DHCW Design System V2) reverts to the previously saved value
And the Error Summary (DHCW Design System V2) (if present) is cleared
And no changes are persisted
```

### Scenario 6: Accessibility
**Title**: User Profile page is WCAG 2.1 AA compliant

```gherkin
Given the User Profile page is rendered
When a user navigates using keyboard only (no mouse)
Then all interactive elements are focusable in the following logical order:
  - 1st: Back Link (DHCW Design System V2)
  - 2nd: Text Input for Preferred Contact Telephone Number
  - 3rd: Save Button (DHCW Design System V2)
  - 4th: Cancel Button (DHCW Design System V2)
  - 5th: Any action links or controls in Professional Accreditations section
And the Tab key moves forward through elements
And Shift+Tab moves backward through elements
And focus indicator is visible (at least 2px solid outline in DHCW brand colour)

Given a screen reader user (NVDA/JAWS) accesses the page
When the page loads
Then the screen reader announces:
  - Page title: "Your Profile | Choose Pharmacy"
  - Main heading: "Your Profile" as H1 landmark
  - "Summary List" role for identity information section
  - Each Summary List (DHCW Design System V2) row read as key-value pair
    - Example: "Full Name: [User Name]"
  - "Contact Information" as section heading (H2)
  - "Preferred Contact Telephone Number" as label associated with Text Input (DHCW Design System V2)
  - Input field type announced as "Editable text"
  - "Save Button, Primary" for the save action button
  - "Cancel Button, Secondary" for the cancel action button
  - "Professional Accreditations" as section heading (H2)
  - Each Tag (DHCW Design System V2) announced with service name and status
    - Example: "Common Ailments Service, Active, Tag"

Given the page is displayed at any zoom level (100%, 125%, 150%, 200%)
When text content and interactive elements are rendered
Then:
  - Text remains readable without horizontal scrolling at 200% zoom
  - All buttons and interactive elements remain clickable (min 44x44px touch target)
  - No loss of functionality or information

Given the page is rendered in different colour contrast scenarios
When elements are evaluated for colour contrast
Then:
  - All text and background colour combinations meet 4.5:1 contrast ratio (WCAG AAA for normal text)
  - Read-only fields have clear visual differentiation (greyed background, appropriate contrast)
  - Error text in Error Summary (DHCW Design System V2) meets 3:1 contrast with background
  - Focus indicators meet 3:1 contrast ratio against adjacent colours

Given the page contains images, icons, or visual elements
When alternative text is required
Then:
  - All icons in buttons have aria-label attributes
  - Example: Save icon button aria-label="Save changes to contact number"
  - GPHC Registration Type field has help icon with aria-label="Professional registration category from GPHC"
  - No purely decorative images present

Given the user interacts with form fields
When form validation errors occur
Then:
  - Error messages are associated with form fields via aria-describedby
  - Error message is announced to screen reader: "Telephone number must be a valid UK format"
  - Error is displayed near the field (above or adjacent, not hidden)
  - Error announcements are automatic, not requiring explicit user focus change
```

---

## Definition of Done

- [ ] All acceptance criteria met and validated by QA
- [ ] Code peer reviewed and approved (GitHub code review)
- [ ] Unit tests written (80%+ coverage) for all interactive elements
- [ ] Component integration tests confirm DHCW Design System V2 components render correctly
- [ ] Accessibility testing completed (WCAG 2.1 AA) using axe DevTools, WAVE, or similar
- [ ] Manual accessibility testing with keyboard navigation and screen reader (NVDA/JAWS)
- [ ] UI matches DHCW Design System V2 Figma design
- [ ] Browser compatibility verified (Chrome, Firefox, Safari, Edge - latest 2 versions)
- [ ] Mobile responsiveness tested (iPhone, Android, tablet sizes)
- [ ] API integration tested (Entra ID data retrieval, WRTS feed data loading)
- [ ] Error states tested and validated
- [ ] Page load performance verified (< 2 seconds)
- [ ] Documentation updated in README or component library
- [ ] Ready for UAT with stakeholders

---

## Technical Notes

### Data Sources
- **Identity Information**: Microsoft Entra ID (via Microsoft Graph API)
  - Full Name (displayName)
  - Email Address (userPrincipalName)
  - GPHC Registration Number (extension attributes from Entra ID)
  - GPHC Registration Type (extension attributes from Entra ID)

- **Preferred Contact Number**: Choose Pharmacy User Profile database
  - Persisted in database
  - Updated via API call on Save action

- **Professional Accreditations**: Welsh Regulatory and Training Service (WRTS) feed
  - Scheduled data sync from WRTS
  - Service names and status retrieved from feed
  - Cached in application database for performance

### API Endpoints Required
- `GET /api/users/profile` — Retrieve current user profile with contact number
- `PUT /api/users/profile/contact-number` — Update preferred contact number
- `GET /api/users/accreditations` — Retrieve user's WRTS accreditations

### Error Handling
- Network timeout: Display "Unable to load profile information. Please try again."
- WRTS feed unavailable: Display "Accreditation information temporarily unavailable"
- Permission denied: Redirect to unauthorised page
- Validation errors: Display Error Summary (DHCW Design System V2) with inline field errors

### Future Enhancements (Out of Scope)
- Editing other profile fields (requires DHCW approval and Entra ID policy review)
- Two-factor authentication management
- Connected devices/sessions view
- Profile picture/avatar management
- Communication preferences (notification frequency, channels)

---

## Notes

- **Design Review**: Confirm mockup with DHCW Design System V2 before development
- **Backend Coordination**: Ensure API endpoints support GPHC registration field retrieval from Entra ID
- **WRTS Integration**: Coordinate with WRTS data team on feed schedule and data format
- **Accessibility**: Consider providing keyboard shortcuts (e.g., Alt+P to navigate to Profile)
- **Analytics**: Track profile page views and contact number update frequency
- **Content Review**: Have business stakeholder review help text and error messages

---

**Feature ID:** 609589  
**Feature Name:** User Profiles  
**Page Name:** User Profile  
**User Roles:** Pharmacist, Pharmacy Technician, Trainee Pharmacist, Pharmacy Administrator, Superintendent Pharmacist, System Administrator  
**Design Reference:** DHCW Design System V2  
**Last Updated:** 2026-09-17  
**Status:** Ready for Development
