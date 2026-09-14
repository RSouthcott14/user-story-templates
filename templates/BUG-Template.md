# BUG User Story Template

## Story Type: BUG

---

## Description

### Summary
[One-line summary of the defect]

### Environment
- **Environment**: [DEV / TEST / UAT / PRODUCTION]
- **Affected Component**: [Module/Page/API]
- **Severity**: [CRITICAL / HIGH / MEDIUM / LOW]
- **Browser/Device** (if applicable): [Chrome v120 on Windows 11 / iPhone 15 Safari / etc.]

### Pre-requisites
- [What must be true before reproducing]
- [Any setup required]

---

## Steps to Reproduce

```
1. [First action]
2. [Second action]
3. [Third action]
```

---

## Actual Result
[What actually happens - describe the defective behavior]

### Evidence
- **Screenshot(s)**: [Attach screenshot showing defect]
- **Video**: [If helpful, attach video]
- **Error Log**: [Paste any error messages from console/logs]

---

## Expected Result
[What should happen - describe correct behavior]

---

## Impact Analysis

### Business Impact
[How does this affect users? e.g., "Blocks users from submitting forms", "Causes data loss", "Blocks entire user journey"]

### Scope
- **Affected Users**: [All users / Specific role / Specific geography]
- **Affected Features**: [Which features are broken]

---

## Acceptance Criteria (Resolution)

### Scenario 1: Defect Resolved
```gherkin
Given the bug conditions are met (see Steps to Reproduce)
When the user performs the action
Then the expected result occurs: [Expected Result]
And no error occurs
And the data is [saved/processed/displayed] correctly
```

### Scenario 2: No Regression
```gherkin
Given the defect has been fixed
When the user performs [related action]
Then no new defects are introduced
And existing functionality continues to work
```

---

## Root Cause Analysis
[Optional - dev team fills in after investigation]

---

## Definition of Done

- [ ] Root cause identified and documented
- [ ] Fix implemented and code reviewed
- [ ] Unit tests passing (80%+ coverage)
- [ ] Regression testing completed
- [ ] Test case added to prevent recurrence
- [ ] Bug verified fixed in TEST environment
- [ ] Documentation updated (if applicable)
- [ ] Ready for UAT verification

---

## Notes
[Any additional context, workarounds, or technical details]
