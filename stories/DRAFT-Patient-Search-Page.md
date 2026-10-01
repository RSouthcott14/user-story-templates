# UI Story: Patient Search Page

## Story Type: UI | Feature: TBD | Status: DRAFT

---

## Description

### Story Card
**As a** pharmacy user  
**I want** to search for patients by ID, name, or NHS number  
**So that** I can quickly locate and access patient records to initiate consultations or services

### Context
The Patient Search Page is the primary entry point for community pharmacy users initiating patient interactions. Pharmacy staff must quickly and reliably find patients across the system before proceeding with consultations, medication advice, or other clinical services. This page supports three search modalities — Patient ID (local reference), full name, and NHS number (national identifier) — reflecting real pharmacy workflows where staff may receive requests via different channels (face-to-face, phone, referral forms).

**Related Journey Step**: User Login → Patient Search → Patient Summary/Service Selection

### Out of Scope
- Patient registration or creation (separate story)
- Search history or saved searches
- Bulk patient import or upload
- Advanced filtering beyond basic search criteria
- Patient record editing (separate patient detail story)
- Search analytics or usage reporting
- Autocomplete/suggestions during typing (enhancement for future iteration)

### Related/Dependent Stories
- Feature TBD: Patient Management (parent feature)
- US TBD: Display patient summary/details (downstream dependency)
- US TBD: Select patient service type (downstream dependency)

### Design Reference

**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Components used:**
- [Text Input](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6219-549) — Search field for patient ID, name, NHS number
- [Buttons (Primary)](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475) — Search button (primary action)
- [Back Link](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9492) — Navigate back to previous page
- [Table](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-79) — Display search results in structured format
- [Pagination](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9568) — Navigate between result pages if 20+ results
- [Inset Text](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4589) — Guidance text for search field
- [Notification Banners](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6610-4602) — Empty results or error state messaging
- [Error Summary](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4585) — Display validation errors

**Error messaging standard:** [`standards/UI-ERROR-MESSAGING.md`](../standards/UI-ERROR-MESSAGING.md)

---

## Acceptance Criteria

**Total Scenarios: 8**

### Page Structure & Content

**Scenario 1: Search page displays with all search input options**

```gherkin
Given the user navigates to the Patient Search page
When the page loads
Then the following elements are displayed:
  - Page heading: "Find Patient"
  - Subheading: "Search by Patient ID, Full Name, or NHS Number"
  - Text Input field labeled "Search Term"
  - Inset text guidance: "Enter a Patient ID, full name, or NHS number to locate a patient"
  - Primary button labeled "Search"
  - Back link to previous page
  - Explanatory text: "All fields are case-insensitive"
And the layout includes clear visual hierarchy with heading, search field, and button aligned vertically
And the search input field has a subtle border and placeholder text: "e.g., P123456 or Smith, John or 123 456 7890"
```

### Default State

**Scenario 2: Page loads with empty search field and no results**

```gherkin
Given the user navigates to the Patient Search page
When the page loads for the first time
Then the Search Term field is empty and in focus-ready state
And the Search button displays enabled (not greyed out)
And no results table is displayed
And the page does not show error messages
And the heading "Find Patient" is displayed with clear instructions
```

### User Interaction

**Scenario 3: User enters search term and initiates search**

```gherkin
Given the user is on the Patient Search page
And the search field is focused
When the user types "Smith, John"
Then the search term "Smith, John" appears in the text input field
And the Search button remains enabled
And the system does not perform search automatically (requires explicit button click)
```

**Scenario 4: Search returns results and displays in table**

```gherkin
Given the user has entered a search term: "P123456"
When the user clicks the Search button
Then the search executes and results are displayed in a table
And the table contains the following columns:
  - Patient ID
  - Full Name
  - Date of Birth
  - NHS Number
  - Address (summary)
And each result row is clickable and displays a pointer cursor
And the number of results is displayed: "Showing X of Y results"
And results are sorted by relevance (exact ID matches first, then name, then NHS number)
And the search term "P123456" remains visible in the search field (not cleared)
And results persist when user navigates away and returns to page via Back button
```

**Scenario 5: User selects a patient from results**

```gherkin
Given search results are displayed in the table
And the user sees a patient row: "P789012 | Davies, Jane | 05/12/1975 | 456 789 0123 | 42 Elm Street"
When the user clicks on the patient row
Then the user is navigated to the Patient Summary page
And the page title displays: "Patient Summary: Jane Davies"
And patient details are displayed:
  - Full Name: Jane Davies
  - Patient ID: P789012
  - NHS Number: 456 789 0123
  - Date of Birth: 05/12/1975
And the search term is retained in session (user can return to results via Back)
And the selected patient row is retained as "active" if user returns to search results
```

### Navigation

**Scenario 6: User navigates between result pages with pagination**

```gherkin
Given search results exceed 20 patients (30 results total)
When the page displays the first page of results
Then a Pagination component displays at the bottom of results table
And pagination shows: "Page 1 of 2" with Previous/Next buttons
And the Previous button displays disabled on page 1
And each result page displays maximum 20 patients
When the user clicks the Next button
Then the page navigates to page 2
And the new results (patients 21-30) are displayed in the table
And the Previous button becomes enabled
And the Next button becomes disabled (last page)
And pagination shows: "Page 2 of 2"
And the search term remains populated in the search field
And the user can click Previous to return to page 1 with original results retained
```

### Validation

**Scenario 7: User attempts search with empty search field**

```gherkin
Given the user is on the Patient Search page
When the user clicks the Search button without entering a search term
Then an error message displays: "Enter search term"
And the error appears at the top of the page in an Error Summary component
And the error is styled in red with a warning icon
And the error message includes role="alert" for screen reader announcement
And aria-live="polite" for dynamic content
And the user remains on the Patient Search page
And focus returns to the Search Term input field
And no search request is sent to the server
And no data is persisted
```

**Scenario 8: User enters search term less than 2 characters**

```gherkin
Given the user is on the Patient Search page
When the user enters "A" (single character) in the search field
And clicks the Search button
Then an error message displays: "Enter search term between 2 and 100 characters"
And the error appears near the Search Term field and in the Error Summary at top
And the error is styled in red with a warning icon
And the error message includes role="alert" for screen reader announcement
And aria-live="polite" for dynamic content
And the user remains on the Patient Search page
And focus returns to the Search Term input field
And no search request is sent to the server
And no data is persisted
```

### Accessibility

**Scenario 9: WCAG 2.1 AA compliance for keyboard navigation and screen reader users**

```gherkin
Given the page is rendered
When a user navigates using the Tab key
Then focus order is: Skip Link → Back Link → Search Term Input → Search Button
And the Page Heading "Find Patient" is announced by screen reader as <h1>
And the Search Term input field announces: "Search Term, text input, required"
And the Search button announces: "Search, button, primary action"
And all visible text has colour contrast ratio of at least 4.5:1 against background
And the search input field displays visible focus indicator (minimum 3:1 contrast)
And when results display, the table announces via screen reader: "Table with [X] columns and [Y] rows"
And each table header announces: "Patient ID, column header" etc.
And each result row is focusable with keyboard and announces as a link: "[Patient Name], Patient ID [ID], clickable"
And error messages announce immediately with role="alert" and aria-live="polite"
And all interactive elements are keyboard accessible without requiring mouse
And tab order is logical and follows visual left-to-right, top-to-bottom layout
```

---

## Definition of Done

- [ ] All 9 acceptance criteria met and validated by QA
- [ ] Code peer reviewed and approved by backend/frontend leads
- [ ] Unit tests written (minimum 80% coverage)
- [ ] Integration tests for search functionality (Patient ID, name, NHS number variants)
- [ ] Accessibility testing completed per WCAG 2.1 AA standard
- [ ] Screen reader testing completed (NVDA, JAWS, or VoiceOver)
- [ ] Keyboard-only navigation testing completed
- [ ] UI matches DHCW Design System V2 Figma reference
- [ ] Browser compatibility verified: Chrome, Firefox, Safari, Edge (latest 2 versions)
- [ ] Mobile responsiveness tested: 375px (mobile), 768px (tablet), 1024px (desktop)
- [ ] Error message copy matches [`standards/UI-ERROR-MESSAGING.md`](../standards/UI-ERROR-MESSAGING.md) exactly
- [ ] Performance tested: search results load in <2 seconds for 100+ patient records
- [ ] Pagination works correctly with result sets >20 patients
- [ ] Back button retains search term and results (session state)
- [ ] Documentation updated: User guide, accessibility notes, design patterns
- [ ] Ready for UAT in TEST environment

---

## Notes

### Design Considerations
- Search should support partial matches (e.g., "Smith" finds "Smith, John")
- NHS numbers may be entered with or without spaces (format flexibility)
- Patient ID search is case-insensitive
- Results should prioritize exact matches over partial matches
- Empty state messaging when no results found: "No patients found matching '[search term]'. Check spelling and try again."

### Known Issues / Future Enhancements
- Autocomplete/typeahead suggestions (not in scope for MVP, consider for v1.1)
- Advanced filters by organisation/location (future enhancement)
- Saved search history (future enhancement, requires data persistence)
- Search analytics/audit trail (future enabler story)

### Figma Design Reference
- [Patient Search Page Frame](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) — [Insert specific frame link when available]

### Related Standards
- [UI Error Messaging Standard](../standards/UI-ERROR-MESSAGING.md)
- [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)
- [Accessibility Checklist](../guides/ACCESSIBILITY-CHECKLIST.md)

### Testing Strategy
1. **Unit Tests**: Validation logic (empty field, character limit, format)
2. **Integration Tests**: Search API, pagination, session state retention
3. **E2E Tests**: Full user journey (search → select → navigate to summary)
4. **Accessibility Tests**: WCAG 2.1 AA manual + automated scanning (Axe, Lighthouse)
5. **Performance Tests**: Search response time <2s with large datasets

---

## Feature Linkage

**Link this story to parent Feature in Azure DevOps**
- Feature ID: [TBD — Request from Product Owner]
- Link Type: Child (story is a direct feature deliverable)
- Status: Ready for backlog refinement
