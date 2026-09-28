# UI User Story: DM+D API Integration - Medication Search Component

## Story Type: UI

---

## Description

### Story Card
**As a** system  
**I want** the DM+D search component to use the correct API calls to get medication and pack sizes available from the DM+D  
**So that** any medication searched and selected by the user is from the correct source

---

### Context

This story covers the integration of API endpoints into the existing Medicines Selection search component. The component will call two APIs to retrieve medication and pack size data from the DM+D (Dictionary of Medicines and Devices):

1. **GET DM+D Medicine Search (592874)** — Returns available medicines matching search criteria
2. **GET DM+D Medicine Packs (595522)** — Returns pack sizes available for a selected medicine

The component is already built (ENABLER: 586964), so this story focuses on wiring the API calls to replace mock data or static lists with live DM+D data.

**Service Context:** All Choose Pharmacy services (CAS, STTT, UTI, DMR, EMS, CS, IPS) use medication selection

**Technical Governance:**
- API calls must be asynchronous (non-blocking UI)
- Results must be cached where appropriate
- Error handling must gracefully degrade to user messaging
- Performance: API calls must complete within 2 seconds

---

### Out of Scope

- API endpoint implementation (covered in stories 592874, 595522)
- Medication data transformation/mapping logic (backend responsibility)
- Advanced search filters beyond text input (future enhancement)
- Bulk medication import/export functionality
- DM+D data governance or data quality rules

---

### Related/Dependent Stories

- **Depends on:** 
  - 592874: API – GET DM+D Medicine Search
  - 595522: API – GET DM+D Medicine Packs
- **Related to:**
  - 586964: ENABLER: Component - Medicines Selection Table (existing component)

---

## Acceptance Criteria

### Scenario 1: Component Structure & API Integration

**Title:** Medicines Selection search component displays and integrates with DM+D APIs

```gherkin
Scenario 1: Search component loads with API integration active
  Given the Medicines Selection search component is rendered on a page
  And the DM+D API endpoints are available (592874, 595522)
  When the component loads for the first time
  Then the following structure is in place:
    - Search input field labeled "Search for medication" (Text Input - DHCW Design System V2)
    - "Search" button to trigger API call [Buttons - Primary - DHCW Design System V2]
    - Results area (initially empty or placeholder) [Inset Text - DHCW Design System V2]
    - Optional filters section (if applicable) [Radios/Checkboxes - DHCW Design System V2]
    - Loading indicator (initially hidden) [Spinner or Inset Text]
    - Error message area (initially hidden) [Error Summary - DHCW Design System V2]
  And the component is configured to call API endpoint: GET /api/dmd/medicines/search
  And the component is configured to call API endpoint: GET /api/dmd/medicines/{medicineId}/packs
  And API credentials/authentication are properly configured
```

---

### Scenario 2: Default State

**Title:** Component displays in default/initial state with no data

```gherkin
Scenario 2: Medicines Selection component in default state
  Given the component has just rendered
  When the page loads for the first time
  Then the component displays:
    - Search input field: empty, enabled, with placeholder "Search for medication name or code"
    - "Search" button: enabled, ready to accept clicks
    - Results area: displays "Start typing to search for medications" [Inset Text]
    - Loading indicator: hidden (not visible)
    - Error messages: not displayed
    - Pack size dropdown (if visible): empty or disabled until medicine selected
  And no API calls have been made yet
```

---

### Scenario 3: User Interaction & API Calls

**Title:** Component responds to user input and calls correct APIs

```gherkin
Scenario 3a: User searches for medication and component calls GET DM+D Medicine Search API
  Given the user is on a page with the Medicines Selection component
  And the search field is empty
  When the user types "paracetamol" in the search field
  And clicks the "Search" button
  Then the loading indicator appears: "Searching medications..."
  And the component calls: GET /api/dmd/medicines/search?query=paracetamol
  And the component sends request with parameters: 
    - query: "paracetamol"
    - limit: 50 (default)
    - offset: 0
  And the request includes authorization headers (Bearer token)
  And the API response is received within 2 seconds

Scenario 3b: API returns medication results and component displays them
  Given the user has searched for "paracetamol"
  And the API (592874) has returned results:
    - Paracetamol 500mg Tablets
    - Paracetamol 120mg/5ml Oral Suspension
    - Paracetamol 1000mg Effervescent Tablets
  When the results are received
  Then the loading indicator disappears
  And results display in a list/table format [Summary List or Table - DHCW Design System V2]:
    - Each result shows: Medication Name, Strength, Form
    - Each result is clickable or has a "Select" button
  And the user can click on a result to select it

Scenario 3c: User selects medication and component calls GET DM+D Medicine Packs API
  Given the search results are displayed
  And the user clicks on "Paracetamol 500mg Tablets"
  When the selection is made
  Then the loading indicator appears: "Loading available pack sizes..."
  And the component calls: GET /api/dmd/medicines/{medicineId}/packs
  And the request includes: medicineId: from selected result
  And the component sends parameters:
    - medicineId: [UUID or DM+D ID]
    - limit: 100

Scenario 3d: Pack sizes are displayed after selection
  Given the API (595522) has returned pack size results:
    - 100 tablets per pack
    - 500 tablets per pack (bulk)
  When the results are received
  Then the loading indicator disappears
  And pack size dropdown/selection displays available options [Select - DHCW Design System V2]
  And the user can select a pack size
  And the selected medication and pack size are retained in component state
```

---

### Scenario 4: Navigation & Data Flow

**Title:** Component passes data through workflow and maintains state

```gherkin
Scenario 4a: Selected medication data is available to parent form
  Given the user has selected a medication and pack size
  When the component's selection is complete
  Then the parent form/page can access:
    - Selected medicine ID (DM+D ID)
    - Selected medicine name
    - Selected pack size
    - Selected quantity (if applicable)
  And this data can be passed to subsequent API calls or form submissions

Scenario 4b: User can clear selection and search again
  Given a medication has been selected
  When the user clicks "Clear" or "Search Again" button
  Then the previous selection is cleared
  And the search field is reset to empty
  And the results area resets to default state
  And the user can perform a new search
```

---

### Scenario 5: Validation & Error Handling

**Title:** Component handles API errors gracefully

```gherkin
Scenario 5a: API search returns no results
  Given the user searches for "xyzabc123notreal"
  And the API (592874) returns empty results
  When the API response is received
  Then the loading indicator disappears
  And an Inset Text message displays: "No medications found matching 'xyzabc123notreal'. Please try a different search term."
  And the user can try another search

Scenario 5b: API returns an error (500, 503, etc.)
  Given the user performs a search
  And the API (592874) returns error: HTTP 500 Internal Server Error
  When the error response is received
  Then the loading indicator disappears
  And Error Summary displays: "Unable to search medications at this time. Please try again later."
  And the error is logged for support/monitoring
  And the user can retry the search

Scenario 5c: API timeout (>2 seconds)
  Given the user performs a search
  And the API endpoint does not respond within 2 seconds
  When the timeout occurs
  Then the loading indicator disappears
  And Error Summary displays: "Search took too long. Please try again or contact support."
  And the request is cancelled
  And the user can retry

Scenario 5d: Pack size API fails after medication selected
  Given the user has selected a medication
  And the component calls the pack size API (595522)
  And the API returns error: HTTP 403 Forbidden
  When the error response is received
  Then the loading indicator disappears
  And Error Summary displays: "Unable to load pack sizes for this medication. Please select a different medication or contact support."
  And the medication selection is retained
  And the user can try a different medication

Scenario 5e: Missing or invalid API response
  Given the API returns a malformed response (invalid JSON, missing required fields)
  When the component tries to parse the response
  Then the component logs the error
  And Error Summary displays: "Invalid data received from server. Please try again."
  And the search/selection is reset to allow retry

Scenario 5f: Network connectivity error
  Given the user is offline or network connection is lost
  When the component attempts an API call
  Then the API call fails
  And Error Summary displays: "Network error. Please check your connection and try again."
  And the user can retry when connection is restored
```

---

### Scenario 6: Accessibility (WCAG 2.1 AA)

**Title:** Component meets accessibility standards

```gherkin
Scenario 6a: Keyboard navigation and screen reader support
  Given the Medicines Selection component is rendered
  When a keyboard-only user navigates using Tab
  Then tab order is logical: Search input → Search button → Results → Pack size selection
  And each interactive element is keyboard accessible (Tab, Enter, Arrow keys)
  And screen reader announces:
    - "Search for medication, text input"
    - "Search button, primary action"
    - "Results list, X medications found"
    - "Paracetamol 500mg Tablets, button, select this medication"
    - Loading states: "Searching medications, please wait"
    - Errors: with role="alert" announcing error messages immediately

Scenario 6b: Loading and error message announcements
  Given a loading indicator appears
  When the user is using a screen reader
  Then the screen reader announces: "Loading, searching medications, please wait"
  And aria-live="polite" or aria-live="assertive" is set appropriately
  And when an error appears, screen reader announces immediately with role="alert"

Scenario 6c: Color contrast and visual design
  Given page elements are rendered
  Then all text meets WCAG AA contrast: Normal 4.5:1, Large 3:1
  And buttons are not distinguished by color alone
  And loading indicators use visual patterns beyond color
  And error messages use icon + color + text (not color only)

Scenario 6d: Focus management during async operations
  Given the user clicks Search button
  When the API call is in progress
  Then focus is managed appropriately:
    - Loading indicator is announced
    - Results area is marked with aria-live="polite"
    - When results arrive, focus can move to first result or results header
  And when an error occurs, focus moves to Error Summary with role="alert"

Scenario 6e: Mobile and responsive accessibility
  Given the component displays on mobile (320px+) and desktop (1024px+)
  Then:
    - Search input is 44px minimum height (mobile touch target)
    - Search button is 44px minimum (mobile touch target)
    - Results are single-column layout (no horizontal scroll)
    - Pack size dropdown is accessible on all screen sizes
    - Touch-friendly spacing maintained (8px minimum between targets)

Scenario 6f: Form label associations
  Given the component is rendered
  Then all form fields have:
    - Associated label elements (via for/id)
    - Clear, descriptive label text
    - aria-required or aria-label where appropriate
    - Error fields marked with aria-invalid="true"
    - Error messages linked via aria-describedby
```

---

## Definition of Done

- [ ] All 6 acceptance criteria scenarios passed
- [ ] Component calls API 592874 (GET DM+D Medicine Search) when user searches
- [ ] Component calls API 595522 (GET DM+D Medicine Packs) when medicine selected
- [ ] API endpoints are correctly configured in component (URL, auth headers, parameters)
- [ ] Results from APIs display correctly in component UI
- [ ] Loading indicators show during API calls
- [ ] Error handling tested for all error scenarios (timeouts, 4xx/5xx, malformed responses)
- [ ] API response time within 2 seconds SLA verified
- [ ] Results are cached appropriately (if applicable per API design)
- [ ] Selected medication data is accessible to parent form/page
- [ ] Keyboard navigation fully tested (Tab, Enter, Arrow keys)
- [ ] Screen reader testing passed (NVDA, JAWS, VoiceOver)
- [ ] WCAG 2.1 AA compliance verified (axe DevTools)
- [ ] Color contrast verified (4.5:1 minimum)
- [ ] Touch targets 44px minimum on mobile
- [ ] Responsive design tested (mobile 320px, tablet 768px, desktop 1024px+)
- [ ] Unit tests written (API call mocking, response handling, error cases)
- [ ] Integration tests written (component + API integration)
- [ ] Performance tested (load times, memory usage)
- [ ] Peer code review approved
- [ ] Browser compatibility confirmed (Chrome, Firefox, Safari, Edge)
- [ ] Documentation updated (API integration guide, component usage)

---

## Notes

**API Integration Points:**

1. **Search API (592874):**
   - Endpoint: `GET /api/dmd/medicines/search`
   - Parameters: query, limit, offset
   - Response: Array of medicines with id, name, strength, form

2. **Packs API (595522):**
   - Endpoint: `GET /api/dmd/medicines/{medicineId}/packs`
   - Parameters: medicineId, limit
   - Response: Array of pack sizes with id, quantity, unit

**Performance Requirements:**
- Search API response: < 2 seconds
- Packs API response: < 2 seconds
- Component UI responsiveness: No blocking calls

**Error Handling Strategy:**
- Timeout: 2 second limit with user-friendly message
- Retry logic: Allow user to retry after error
- Logging: Log all API errors for monitoring/support
- Graceful degradation: Show meaningful messages, not technical errors

**Data Security:**
- All API calls include Bearer token authentication
- Sensitive data (medication IDs) handled securely
- HTTPS only (no HTTP)

**Related Components:**
- Parent form/page that consumes selected medication data
- Error Summary component (DHCW Design System V2)
- Loading indicator component
- Results display (Summary List or Table - DHCW Design System V2)

**Design Reference:**
DHCW Design System V2: https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2

✅ **Story Complete and Ready for Review!**
