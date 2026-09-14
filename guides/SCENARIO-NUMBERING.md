# Scenario Numbering Best Practices

## Why Number Scenarios?

Numbering acceptance criteria scenarios provides:

✅ **Easy Reference** — "Let's discuss Scenario 3" is faster than quoting the whole scenario  
✅ **QA Traceability** — "Scenario 3 failed on date X" clearly identifies which test failed  
✅ **Code Comments** — "This handles Scenario 1" shows purpose of code block  
✅ **Sprint Discussions** — Developers and QA can quickly align on which scenarios are ready  

---

## Numbering Convention

### Standard Format

```markdown
### Scenario [#]: [Title]
**Title**: [What this scenario tests]

[Gherkin Given/When/Then block]
```

### Example

```markdown
### Scenario 1: Page Structure & Content
**Title**: Tool selection page displays all available options

Given the user has selected a clinical condition
When they navigate to the tool selection page
Then the following elements are displayed:
  - Page heading: "Select Assessment Tool"
  - Radio button group with options
  - Continue button
```

---

## Numbering Rules

### ✅ DO's

1. **Start at 1** — Always begin numbering at Scenario 1, not 0

2. **Number Sequentially** — 1, 2, 3, ... (no gaps like 1, 3, 5)

3. **Group by Section** — UI stories typically have ~6 scenarios covering:
   - Scenario 1: Page Structure & Content
   - Scenario 2: Default State
   - Scenario 3: User Interaction / Action
   - Scenario 4: Navigation / Flow
   - Scenario 5: Validation / Error Handling
   - Scenario 6: Accessibility

4. **Keep Numbering Across Versions** — If you remove a scenario, mark it as struck-through:
   ```markdown
   ~~### Scenario 3: [Removed Feature]~~ 
   (Removed in v2; feature is out of scope)
   
   ### Scenario 4: [Next Scenario]
   ```
   **Never renumber existing scenarios** to avoid breaking references.

5. **Use Consistent Format** — All stories use same number style: `### Scenario 1:`, `### Scenario 2:`, etc.

---

## Removing Scenarios (Don't Renumber!)

### ❌ WRONG — Renumbering after removal

```markdown
### Scenario 1: Page Structure
### Scenario 2: Default State
### Scenario 3: Accessibility  ← Was Scenario 4; DON'T RENUMBER!
```

### ✅ CORRECT — Mark as removed, keep numbering

```markdown
### Scenario 1: Page Structure
### Scenario 2: Default State
~~### Scenario 3: User Interaction~~ (Removed in v1.1; feature cancelled)
### Scenario 4: Navigation
### Scenario 5: Validation
### Scenario 6: Accessibility
```

**Why?** Because developers may have already written code that references "Scenario 4". Renumbering breaks that reference and causes confusion.

---

## Scenario Numbering in Development

### During Coding

```javascript
// Scenario 1: Verify page loads correctly
describe('Page Load', () => {
  it('should display all required elements', () => {
    // Test code for Scenario 1
  });
});

// Scenario 2: Verify default state
describe('Default State', () => {
  it('should have no selection on first load', () => {
    // Test code for Scenario 2
  });
});

// Scenario 4: Navigation (Scenario 3 removed)
describe('Navigation', () => {
  it('should navigate to next page on Continue click', () => {
    // Test code for Scenario 4
  });
});
```

### During QA Testing

**QA Test Case Document:**
```
Test Case: TS-585587-001 (Story ID-Scenario)
Title: Clinical Tool Selection - Page Structure
Scenario: Scenario 1
Status: ✅ PASSED on 2026-09-14

Test Case: TS-585587-002
Title: Clinical Tool Selection - Default State
Scenario: Scenario 2
Status: ✅ PASSED on 2026-09-14

Test Case: TS-585587-003
Title: Clinical Tool Selection - [Removed]
Scenario: Scenario 3 [REMOVED - see comments]
Status: SKIPPED - Out of scope in v1.1

Test Case: TS-585587-004
Title: Clinical Tool Selection - Navigation
Scenario: Scenario 4
Status: ⏳ IN PROGRESS
```

### Sprint Planning / Standups

**Developer's standup note:**
```
"Scenario 1 and 2 are done. Scenario 4 is 80% complete. 
Scenario 5 and 6 blocked waiting for design specs."
```

**QA's standup note:**
```
"Scenario 1, 2 passing. Scenario 4 has 1 failing edge case. 
Recommending we fix before moving to Scenario 5."
```

---

## Example: Story Evolution with Numbering

### Version 1.0 (Initial)
```markdown
### Scenario 1: Page Structure & Content
[Gherkin]

### Scenario 2: Default State
[Gherkin]

### Scenario 3: User Action
[Gherkin]

### Scenario 4: Navigation
[Gherkin]

### Scenario 5: Validation
[Gherkin]

### Scenario 6: Accessibility
[Gherkin]
```

### Version 1.1 (Scenario 3 Removed - Feature Changed)
```markdown
### Scenario 1: Page Structure & Content
[Gherkin - UNCHANGED]

### Scenario 2: Default State
[Gherkin - UNCHANGED]

~~### Scenario 3: User Action~~
(Removed in v1.1; original action flow replaced with simplified single-click selection)

### Scenario 4: Navigation
[Gherkin - UPDATED to reflect new flow]

### Scenario 5: Validation
[Gherkin - UNCHANGED]

### Scenario 6: Accessibility
[Gherkin - UNCHANGED]
```

**Benefits:**
- Developers who wrote code for "Scenario 3" see it's removed ✅
- QA knows Scenarios 1, 2, 4, 5, 6 are still active ✅
- If someone asks "what happened to Scenario 3?", history is clear ✅
- No confusion from renumbering ✅

---

## Linking Scenarios to Code/Tests

### In Code Comments

```python
# Scenario 5: Validation - User cannot proceed without selection
def test_cannot_submit_without_selection():
    """Verifies that clicking Submit without selecting a tool shows error."""
    # Arrange
    page.navigate_to_tool_selection()
    # Act
    page.click_continue_without_selecting()
    # Assert
    assert page.error_message_displays() == "Please select a tool"
```

### In Issue Tracking

**Azure DevOps Work Item Comment:**
```
"Found defect in Scenario 4 - Navigation flow.
When I click Continue, I'm not being redirected properly.
Expected: Navigate to assessment page
Actual: Page refreshes but no navigation occurs
Environment: Chrome 120, UAT
Reproducible: Yes, 100%
Severity: HIGH (blocks entire user flow)"
```

### In Test Reports

**Test Execution Report:**
```
Feature: 577033 - Clinical Conditions Management
User Story: US-585587 - Tool Selection

Test Results:
✅ Scenario 1: Page Structure & Content          [PASSED]    [Duration: 2.3s]
✅ Scenario 2: Default State                     [PASSED]    [Duration: 1.8s]
⏭️  Scenario 3: [REMOVED]                        [SKIPPED]   [N/A]
⚠️  Scenario 4: Navigation                       [FAILED]    [Duration: 5.2s]
✅ Scenario 5: Validation                        [PASSED]    [Duration: 3.1s]
✅ Scenario 6: Accessibility                     [PASSED]    [Duration: 8.4s]

Summary: 4/5 active scenarios passing (80%)
Blocker: Scenario 4 navigation defect - see bug #585599
Next Steps: Fix Scenario 4, retest before UAT sign-off
```

---

## When Numbering Changes (Rare)

If you **must** renumber (e.g., major story restructuring for new version):

1. **Create new version of story** (v2.0)
2. **Clearly mark old scenario numbers** as deprecated
3. **Map old → new scenarios** for reference:
   ```markdown
   ## Scenario Mapping (v1.0 → v2.0)
   - v1.0 Scenario 1 → v2.0 Scenario 1 (unchanged)
   - v1.0 Scenario 2 → v2.0 Scenario 2 (unchanged)
   - v1.0 Scenario 3 → v2.0 Scenario 4 (updated)
   - v1.0 Scenario 4 → v2.0 Scenario 3 (reordered)
   ```
4. **Notify all teams** of renumbering (email + wiki announcement)

**Avoid if possible** — Strikes in text are better than renumbering.

---

## Summary

| Aspect | Best Practice |
|--------|---------------|
| **Start number** | Always 1 (not 0) |
| **Sequence** | 1, 2, 3, ... (no gaps) |
| **After removal** | ~~Strike through~~ text; keep numbering |
| **Renumbering** | Avoid; only on major version changes with mapping |
| **Format** | `### Scenario #: [Title]` (consistent across all stories) |
| **In code** | Reference scenario number in comments |
| **In QA reports** | Use scenario numbers for test case IDs |
| **In standups** | Reference by scenario number for clarity |

---

**Golden Rule:** Numbers are permanent references. Once assigned, they're part of the story's identity. Maintain them across story versions to avoid confusion and rework.
