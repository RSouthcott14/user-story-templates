# Acceptance Criteria Best Practices

**Last Updated:** 25 September 2026

## What Are Acceptance Criteria?

**Acceptance Criteria** define the conditions that must be met for a user story to be considered "done". They specify what the feature should do, not how it should be built.

### Purpose

✅ **Clarity** — Both dev and QA understand what "done" means  
✅ **Testability** — Each criterion can be verified/tested  
✅ **Scope Control** — Prevents scope creep (if it's not in criteria, it's out of scope)  
✅ **Traceability** — Each acceptance criterion maps to test cases  

---

## Structure: Given/When/Then Format

All acceptance criteria use **Gherkin** format (Given/When/Then) for consistency and automation compatibility.

```gherkin
Scenario: [What this scenario tests]
  Given [precondition/context]
  When [user action]
  Then [expected result]
  And [additional results]
```

---

## Story Type-Specific Criteria

### UI Stories (6 Sections)

UI stories consistently follow this 6-section pattern:

1. **Scenario 1: Page Structure & Content** — What elements are displayed?
2. **Scenario 2: Default State** — What's the initial state when page loads?
3. **Scenario 3: User Interaction / Action** — What happens when user acts?
4. **Scenario 4: Navigation / Flow** — How does user move to next page?
5. **Scenario 5: Validation / Error Handling** — What happens on invalid input?
6. **Scenario 6: Accessibility** — WCAG 2.1 AA compliance (keyboard, screen reader, contrast)

**Example (Tool Selection Page):**
```gherkin
Scenario 1: Page Structure & Content
  Given the user navigates to tool selection page
  When the page loads
  Then radio button group displays with options
  And Continue button is visible
  And page heading shows "[Condition Name]"

Scenario 2: Default State
  Given the user navigates to tool selection page
  When the page loads for first time
  Then no radio button is pre-selected
  And Continue button is enabled

Scenario 3: User Action
  Given the user is on tool selection page
  When the user clicks "FourMats" radio button
  Then "FourMats" becomes selected
  And visual feedback shows selection

Scenario 4: Navigation
  Given the user has selected a tool
  When the user clicks Continue
  Then the user navigates to assessment page
  And selected tool is passed to next page

Scenario 5: Validation
  Given the user is on tool selection page
  When the user clicks Continue without selecting
  Then error message displays
  And user remains on current page

Scenario 6: Accessibility
  Given the page is rendered
  When keyboard user navigates with Tab
  Then focus order is: Back → Radio 1 → Radio 2 → Continue
  And screen reader announces: "[Tool Name], radio button"
```

---

### BUG Stories (2-3 Scenarios)

Bug stories focus on reproducing the defect and verifying the fix:

1. **Scenario 1: Defect Reproduced** — Following exact steps recreates the problem
2. **Scenario 2: Fix Verified** — After fix, expected behavior occurs
3. **Scenario 3: No Regression** — Related functionality still works

```gherkin
Scenario 1: Defect Reproduced
  Given the user is on Clinical Assessment page
  When the user clicks first radio button
  And presses RIGHT arrow key
  Then focus should move to next radio button
  But focus remains on first radio button (DEFECT)

Scenario 2: Defect Fixed
  Given the defect has been fixed
  When the user presses RIGHT arrow key on radio button
  Then focus moves to next radio button
  And selection updates correctly

Scenario 3: No Regression
  Given the radio button fix is deployed
  When existing functionality is tested:
    - Direct clicking still works
    - Tab navigation still works
  Then no new defects are introduced
```

---

### ENABLER Stories (4 Sections)

Enabler stories follow technical evaluation → implementation → testing → documentation flow:

1. **Scenario 1: Technology Evaluation** — Options evaluated; selection made
2. **Scenario 2: Implementation** — Capability delivered and integrated
3. **Scenario 3: Quality & Testing** — Tests pass; performance validated
4. **Scenario 4: Documentation & Training** — Team understands capability

```gherkin
Scenario 1: Technology Selection
  Given OAuth options are evaluated
  When comparison matrix is created
  Then Spring Security OAuth2 is recommended
  And rationale is documented

Scenario 2: Implementation Complete
  Given OAuth2 server is selected
  When implementation is done
  Then Bearer token generation works
  And integration with existing services complete
  And performance meets targets

Scenario 3: Quality Assurance
  Given implementation is complete
  When test suite runs
  Then all tests pass (80%+ coverage)
  And performance benchmarks met
  And security audit passes

Scenario 4: Documentation
  Given everything is built and tested
  When documentation is reviewed
  Then includes: architecture, API specs, integration guide
  And team training is complete
```

---

### SPIKE Stories (4 Sections)

Spike stories focus on research → analysis → assessment → recommendation:

1. **Scenario 1: Research & Documentation** — Questions answered with evidence
2. **Scenario 2: Analysis & Findings** — Findings analyzed; trade-offs identified
3. **Scenario 3: Risk Assessment** — Risks identified; mitigation proposed
4. **Scenario 4: Recommendation** — Clear recommendation with rationale

```gherkin
Scenario 1: Research Complete
  Given the spike has started
  When research is conducted
  Then all key questions are answered with evidence
  And findings are documented in report
  And external resources referenced

Scenario 2: Analysis Complete
  Given research is done
  When team analyzes findings
  Then trade-offs are identified
  And risks are listed
  And assumptions documented

Scenario 3: Risk Assessment
  Given findings are analyzed
  When risk assessment conducted
  Then technical risks identified
  And mitigation strategies proposed
  And delivery impact assessed

Scenario 4: Recommendation Made
  Given all research is complete
  When team recommends approach
  Then recommended option is clear
  And rationale is documented
  And next steps defined
```

---

### API Stories (6-7 Scenarios)

API stories cover success path, validation, authentication, authorization, and performance:

1. **Scenario 1: Success Path** — Valid request returns expected response
2. **Scenario 2: Input Validation** — Invalid data rejected with error
3. **Scenario 3: Authentication** — Missing/invalid token rejected
4. **Scenario 4: Authorization** — Insufficient permissions rejected
5. **Scenario 5: Not Found** — Non-existent resource returns 404
6. **Scenario 6: Data Integrity** — Data persisted correctly
7. **Scenario 7: Performance** — Response time meets targets

```gherkin
Scenario 1: Success Path
  Given client has valid token
  When POST /api/ipstatus is called
  Then API returns 200 OK
  And response includes expected fields

Scenario 2: Validation
  Given invalid request body (missing required field)
  When request is sent
  Then API returns 400 Bad Request
  And error message identifies missing field

Scenario 3: Authentication
  Given client has no valid token
  When request is sent
  Then API returns 401 Unauthorized

Scenario 4: Authorization
  Given client has valid token but insufficient role
  When request is sent
  Then API returns 403 Forbidden

Scenario 5: Not Found
  Given request references non-existent resource
  When request is sent
  Then API returns 404 Not Found

Scenario 6: Data Integrity
  Given valid request
  When API creates/updates resource
  Then data is persisted correctly
  And subsequent GET returns same data

Scenario 7: Performance
  Given valid request
  When API processes
  Then response time < 200ms (95th percentile)
```

---

### ISSUE Stories (2 Scenarios)

Issue stories focus on making a decision:

1. **Scenario 1: Decision Made** — Issue discussed; decision reached
2. **Scenario 2: Action Defined** — Next steps clear

```gherkin
Scenario 1: Decision Made
  Given issue has been discussed
  When team reaches agreement
  Then decision is documented
  And rationale recorded
  And stakeholders informed

Scenario 2: Action Defined
  Given decision is made
  When next steps are determined
  Then action items are clear
  And owners assigned
  And timeline defined
```

---

## Writing Effective Acceptance Criteria

### ✅ DO's

1. **Be Specific** — Not vague
   ```gherkin
   ✓ Then the error message displays: "Enter a valid email address"
   ✗ Then an error displays
   ```

2. **Make Results Verifiable** — QA/Dev can test it
   ```gherkin
   ✓ Then response time is < 200ms (95th percentile)
   ✗ Then the API is fast
   ```

3. **Include Edge Cases** — Boundary conditions matter
   ```gherkin
   Scenario: Wrap-around on last item
     Given focus is on last radio button
     When user presses RIGHT arrow
     Then focus wraps to first radio button
   ```

4. **Cover Happy Path & Error Path**
   ```gherkin
   Scenario: Success (happy path)
   Scenario: Validation Error (error path)
   ```

5. **Test One Thing per Scenario**
   ```gherkin
   ✓ Scenario 1: Page loads with correct structure
   ✓ Scenario 2: Default values are set
   ✗ Scenario 1: Page loads, defaults are set, user can click, and response is returned
   ```

---

### ❌ DON'Ts

1. **Don't Include Implementation Details**
   ```gherkin
   ✗ Then the JavaScript function setRadioValue() is called
   ✗ Then localStorage is updated
   ```

2. **Don't Test Too Many Things in One Scenario**
   ```gherkin
   ✗ When user clicks button AND enters text AND selects option
   ```

3. **Don't Use Vague Words**
   ```gherkin
   ✗ Then something happens
   ✗ Then it works as expected
   ✗ Then the system behaves correctly
   ```

4. **Don't Assume Technical Knowledge**
   ```gherkin
   ✓ Then the application navigates to the next page
   ✗ Then the router changes to /assessment route
   ```

5. **Don't Leave Out Important Context**
   ```gherkin
   ✗ When the user clicks a button (which button? where? under what conditions?)
   ```

---

## Acceptance Criteria Checklist

Before marking a story as "ready for sprint", verify:

- [ ] All acceptance criteria follow Given/When/Then format
- [ ] Each scenario has a descriptive title
- [ ] Scenarios are numbered sequentially (1, 2, 3, ...)
- [ ] Each result is verifiable/testable
- [ ] No implementation details (HOW), only behavior (WHAT)
- [ ] Both happy path and error path covered
- [ ] Edge cases considered
- [ ] Accessibility criteria included (if UI story)
- [ ] Performance criteria specified (if API story)
- [ ] Security criteria covered (if applicable)
- [ ] QA can write test cases from these criteria
- [ ] Dev can implement to satisfy these criteria
- [ ] Stakeholders understand what "done" means

---

## Traceability: Story → Test Cases → Code

```
User Story US-585587
  ├─ Scenario 1: Page Structure
  │   └─ Test Case TS-585587-001
  │       └─ Code: test_tool_selection_displays_elements()
  │
  ├─ Scenario 2: Default State
  │   └─ Test Case TS-585587-002
  │       └─ Code: test_default_state_no_selection()
  │
  ├─ Scenario 3: User Action
  │   └─ Test Case TS-585587-003
  │       └─ Code: test_radio_selection_persists()
  │
  ├─ Scenario 4: Navigation
  │   └─ Test Case TS-585587-004
  │       └─ Code: test_navigation_on_continue()
  │
  ├─ Scenario 5: Validation
  │   └─ Test Case TS-585587-005
  │       └─ Code: test_error_without_selection()
  │
  └─ Scenario 6: Accessibility
      └─ Test Case TS-585587-006
          └─ Code: test_keyboard_navigation()
          └─ Code: test_screen_reader_announcements()
```

---

**Remember: Acceptance Criteria = the contract between BA/stakeholder and Dev/QA. Make it clear, specific, and verifiable!**
