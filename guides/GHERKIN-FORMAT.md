# Gherkin Format Guide

## What is Gherkin?

**Gherkin** is a simple language for writing **Given/When/Then** scenarios that describe application behavior. It's human-readable and can be automated by testing tools.

### Why Use Gherkin?

✅ **Clear & Readable** — Non-technical stakeholders can understand test scenarios  
✅ **Automation-Ready** — BDD tools (Cucumber, SpecFlow, Behave) can execute Gherkin directly  
✅ **Traceability** — Each scenario maps to a test case  
✅ **Consistency** — Same format across all stories, teams, projects  

---

## Basic Structure: Given/When/Then

Every Gherkin scenario has three parts:

### **Given** (Precondition)
**What** is the starting state or context?

- Describes the initial conditions
- Sets up data or state required for the test
- Answers: "What must be true before the action?"

**Examples:**
```gherkin
Given the user is logged in as a Pharmacist
Given the database contains 100 patient records
Given the system time is 9:00 AM on Monday
```

### **When** (Action)
**What** action does the user or system take?

- Describes a single action or interaction
- Answers: "What does the user do?"

**Examples:**
```gherkin
When the user clicks the "Submit" button
When the API receives a POST request with invalid data
When the system processes the monthly reconciliation
```

### **Then** (Expected Result)
**What** should happen as a result?

- Describes the observable outcome
- Must be verifiable (dev can write test assertion for it)
- Answers: "What should change?"

**Examples:**
```gherkin
Then the form displays an error message: "Email is required"
Then the API returns status 200 OK
Then the patient record is updated with the new data
```

---

## Complete Scenario Example

```gherkin
Scenario: User successfully logs in with valid credentials
  Given the user is on the login page
  When the user enters email "user@example.com"
  And the user enters password "ValidPassword123"
  And the user clicks the Login button
  Then the user is redirected to the dashboard
  And the user's name displays in the header
  And a success message shows: "Welcome, John"
```

---

## Keywords

### **And / But**
Use **And** and **But** to write multiple conditions or results without repeating Given/When/Then:

```gherkin
Scenario: User updates profile successfully
  Given the user is logged in
  And the user is on the profile page
  And the profile form is pre-populated with current data
  When the user updates their email address
  And the user clicks Save
  Then the profile is saved successfully
  And a confirmation message displays
  And the user receives a confirmation email
```

### **But** (negation)
```gherkin
Then the form displays an error message
But the form data is not cleared
```

---

## Writing Good Scenarios

### ✅ DO's

1. **Be Specific & Concrete**
   ```gherkin
   ✓ Given the user is on the Clinical Assessment page
   ✗ Given the user is on a page
   ```

2. **Use One Action per "When"** (or separate with "And")
   ```gherkin
   ✓ When the user clicks the radio button for "FourMats"
   ✗ When the user clicks the radio button and checks the box and submits
   ```

3. **Make Results Verifiable**
   ```gherkin
   ✓ Then the API returns status 200
   ✗ Then everything works fine
   ```

4. **Use Realistic Data**
   ```gherkin
   ✓ Given the patient ID is "PAT123456"
   ✗ Given some patient
   ```

5. **Avoid Technical Implementation Details**
   ```gherkin
   ✓ When the user clicks the Continue button
   ✗ When the JavaScript click event fires on element id="btn-continue"
   ```

---

### ❌ DON'Ts

1. **Don't mix multiple actions in "When"**
   ```gherkin
   ✗ When the user fills in all fields and clicks Submit and waits for response
   ```

2. **Don't make vague assertions**
   ```gherkin
   ✗ Then the system behaves correctly
   ```

3. **Don't test implementation details**
   ```gherkin
   ✗ Then the cache is cleared and the database is updated
   ```

4. **Don't use ambiguous words**
   ```gherkin
   ✗ Then something happens
   ```

5. **Don't include "And" without preceding Given/When/Then**
   ```gherkin
   ✗ And the user sees the page  (should start with Given/When/Then)
   ```

---

## Common Scenario Patterns

### **Happy Path (Success)**
```gherkin
Scenario: User successfully records an adverse reaction
  Given the user is on the adverse reaction form
  When the user enters all required fields
  And the user clicks Submit
  Then the reaction is saved to the database
  And a success message displays
  And the user is navigated to the confirmation page
```

### **Error Path (Validation)**
```gherkin
Scenario: User cannot submit form with invalid data
  Given the user is on the adverse reaction form
  When the user enters an invalid date (31/04/2025)
  And the user clicks Submit
  Then an error message displays: "Enter a valid date"
  And the form data is preserved (not cleared)
  And the user remains on the form
```

### **Edge Case**
```gherkin
Scenario: Partial date is accepted when day is unknown
  Given the user is recording a last reaction date
  When the user enters only month (MM) and year (YYYY)
  And leaves the day field blank
  And clicks Submit
  Then the system accepts the partial date
  And the date is stored as MM/YYYY format
```

### **Authentication/Authorization**
```gherkin
Scenario: Unauthorized user cannot access admin panel
  Given the user is logged in as a Pharmacist
  When the user navigates to /admin/users
  Then the API returns status 403 Forbidden
  And the user is redirected to the access denied page
```

---

## Tips for Testing Teams

### Converting Gherkin to Test Code

**Gherkin:**
```gherkin
Scenario: User selects a clinical scoring tool
  Given the user is on the tool selection page
  When the user clicks the "FourMats" radio button
  Then "FourMats" is selected
```

**Test Code (Cypress/Selenium example):**
```javascript
describe('Tool Selection', () => {
  it('should select FourMats when radio button is clicked', () => {
    // Given
    cy.visit('/clinical/tool-selection');
    
    // When
    cy.get('input[value="FourMats"]').click();
    
    // Then
    cy.get('input[value="FourMats"]').should('be.checked');
  });
});
```

---

## Tools That Support Gherkin

- **Cucumber** (Ruby, Java)
- **SpecFlow** (C#/.NET)
- **Behave** (Python)
- **Robot Framework** (Keyword-driven)
- **Cypress** + plugins
- **Playwright** + plugins

These tools can execute Gherkin scenarios directly!

---

## Gherkin in This Repository

All acceptance criteria in user stories (UI, BUG, ENABLER, SPIKE, API, ISSUE) use Gherkin format. This ensures:

✅ Consistency across all story types  
✅ Easy automation by QA team  
✅ Clear communication with stakeholders  
✅ Traceability from story → test case → executed test  

---

**Remember: Gherkin is for humans and testing tools, not for implementation details. Write scenarios that business stakeholders can understand!**
