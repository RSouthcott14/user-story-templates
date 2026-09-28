# UI User Story: DM+D API Integration - Medication Search

## Story Type: UI (API Integration)

---

## Description

### Story Card
**As a** pharmacy user  
**I want** the medication search field to retrieve medications and pack sizes from the DM+D APIs  
**So that** I can search and select the correct medication with accurate, real-time data from the authoritative source

### Context
This story covers the technical integration of two backend APIs into the existing Medicines Selection component (Story 586964). The search field must call the DM+D GET Medicine Search API to retrieve available medications, and when a medication is selected, trigger the DM+D GET Medicine Packs API to retrieve available pack sizes. This ensures users always access current, validated data from NHS DM+D.

### Out of Scope
- Component layout, styling, and accessibility (covered by Story 586964)
- Error UI and validation messaging (covered by Story 586964)
- State management and form integration (covered by Story 586964)
- Pagination and filtering (covered by Story 586964)

### Related/Dependent Stories
- **592874**: API – GET DM+D Medicine Search — *blocks this story*
- **595522**: API – GET DM+D Medicine Packs — *blocks this story*
- **586964**: ENABLER: Component - Medicines Selection Table — *relates to / component context*

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Components used:**
- Text Input (search field)
- Select/Dropdown (pack size selection)
- Loading Spinner
- Inset Text (API response status)

---

## Acceptance Criteria

**Total Scenarios: 1**

### API Integration: Search Field to DM+D APIs

**Scenario 1: Search field retrieves medications from DM+D Medicine Search API**

```gherkin
Given the Medicines Selection component is loaded
And the DM+D Medicine Search API (Story 592874) is configured and available
And the DM+D Medicine Packs API (Story 595522) is configured and available

When the user types in the medication search field
And waits 300ms after the last keystroke (debounce)
And the search text length is >= 3 characters

Then a GET request is sent to the DM+D Medicine Search API with:
  - Query parameter: search_term = [user input]
  - Query parameter: limit = 50 (or as defined by API contract)
  - Query parameter: offset = 0 (for first search)
  - Headers: Authorization (NHS API token), Content-Type: application/json

And the API response (array of medicines) is parsed and stored in component state
And the search results dropdown displays the returned medicines

When the user selects a medication from the search results
Then the selected medication ID is stored

And a GET request is sent to the DM+D Medicine Packs API with:
  - Query parameter: medicine_id = [selected medication ID]
  - Headers: Authorization (NHS API token), Content-Type: application/json

And the API response (array of pack sizes) is parsed and stored in component state
And the pack size dropdown is populated with the returned pack sizes

And the selected medication name is displayed in the search field
```

**Data Flow Sequence:**
1. User types ≥3 characters → debounce 300ms
2. Search field emits GET request to API 592874 (Medicine Search)
3. Component receives array of medicines → update dropdown options
4. User selects medicine → store medicine_id
5. Component emits GET request to API 595522 (Medicine Packs) with medicine_id
6. Component receives array of packs → populate pack size dropdown
7. Parent form component receives selected medication data (id, name, pack_id)

**API Contract Requirements:**
- **Success Response Status:** 200 OK
- **Response Timeout:** 2000ms (2 seconds)
- **Response Format:** JSON array of objects
- **Caching Strategy:** Results may be cached at component level for identical search terms within 5 minutes

---

## Implementation Notes

- Use debounce on search input to reduce API calls (recommended: 300ms)
- Validate search term length (minimum 3 characters) before API call
- Handle empty responses gracefully (show "No medicines found" message)
- Ensure medicine_id and pack_id are persisted for form submission
- Component should expose these methods/props to parent form:
  - `getSelectedMedicine()` → returns {medicine_id, medicine_name, pack_id, pack_size}
  - `clearSelection()` → resets search field and selections

---

## Acceptance Criteria Validation

✅ API 592874 (GET DM+D Medicine Search) contract satisfied  
✅ API 595522 (GET DM+D Medicine Packs) contract satisfied  
✅ Debounce logic prevents excessive API calls  
✅ Response timeout < 2 seconds  
✅ Parent form can access selected medication and pack data  
✅ Handles empty/null API responses  

---

## Story Points
8 (depends on API availability and integration complexity)

## Relates to Feature
Clinical Conditions Management - Medication Lookup

---

*Generated using `templates/UI-Template.md` | Component guidance: `COMPONENTS.md`*
