# BUG Example: Radio Button Group Not Responding to Keyboard Input

## Story Type: BUG | Severity: HIGH

---

## Description

### Summary
Radio button group on Clinical Conditions page not responding to arrow key navigation; users cannot change selections using keyboard.

### Environment
- **Environment**: UAT
- **Affected Component**: Clinical Assessment Page / Condition Selection
- **Severity**: HIGH (blocks accessibility compliance)
- **Browser/Device**: 
  - Chrome v120 on Windows 11
  - Firefox v123 on macOS 14
  - Safari v17 on iPad Pro

### Pre-requisites
- User must be on the Clinical Conditions selection page
- Radio button group must contain 2+ options
- Keyboard navigation enabled (default state)

---

## Steps to Reproduce

```
1. Navigate to: https://[app]/clinical-assessment/conditions
2. Observe the radio button group for clinical conditions
3. Click on the first radio button to focus it
4. Press the DOWN arrow key (or RIGHT arrow key)
5. Observe the result
```

---

## Actual Result
[What actually happens - describe the defective behavior]

When pressing arrow keys (DOWN, RIGHT) to move between radio options:
- Radio button selection does NOT change
- Focus remains on the first radio button
- No visual feedback or error appears
- Only clicking directly on each option changes the selection

### Evidence
- **Screenshot**: [Shows radio button group with focus indicator on first option]
- **Video**: [5-second recording showing user pressing arrow key, no change]
- **Error Log**: 
  ```
  [No JavaScript errors in console]
  [No focus management events detected in React DevTools]
  ```

---

## Expected Result
When the user presses arrow keys while a radio button has focus:
- The next/previous radio option receives focus
- The radio button selection updates
- Visual focus indicator moves to the newly focused option
- Screen reader announces the newly focused option

---

## Impact Analysis

### Business Impact
This defect **blocks WCAG 2.1 AA compliance** for keyboard navigation. Affects:
- Assistive technology users (screen reader + keyboard only)
- Power users who prefer keyboard navigation
- Accessibility audit failures
- Potential legal/compliance risk

### Scope
- **Affected Users**: All users on Clinical Assessment page; critical for accessibility users
- **Affected Features**: 
  - Condition selection radio button group
  - Potentially other radio button groups on platform (same code pattern)

---

## Acceptance Criteria (Resolution)

### Scenario 1: Arrow Keys Navigate Radio Buttons
```gherkin
Given the user is on the Clinical Assessment page
And the first radio button has focus
When the user presses the RIGHT arrow key
Then focus moves to the second radio button
And the second radio button becomes selected
And the selection change is announced to screen readers
```

### Scenario 2: Wrapping Navigation
```gherkin
Given focus is on the last radio button
When the user presses the RIGHT arrow key
Then focus wraps to the first radio button
And the first radio button becomes selected
```

### Scenario 3: No Regression
```gherkin
Given the radio button group has been fixed
When the user performs these actions:
  - Click a radio button directly
  - Tab to the group and use arrows
  - Use screen reader (NVDA/JAWS) + keyboard
Then all interactions work correctly
And no new defects are introduced
```

---

## Root Cause Analysis

[To be completed by Development team after investigation]

**Suspected Root Cause**: Missing keyboard event handlers (keydown listener for arrow keys) on the radio button group. Standard HTML `<fieldset>` + `<input type="radio">` elements have this built-in; if using custom component (e.g., custom div-based button group), keyboard handling may not be implemented.

---

## Related Work Items
- Feature 577033: Clinical Conditions Management
- US 585586: Display clinical conditions
- US 585587: Tool selection (also uses radio buttons)

---

## Definition of Done

- [ ] Root cause identified (missing keyboard handler, focus management issue, etc.)
- [ ] Fix implemented (keyboard event listener added, focus management updated)
- [ ] Code reviewed and approved
- [ ] Unit tests written covering arrow key navigation (LEFT, RIGHT, UP, DOWN)
- [ ] Manual regression testing: clicking, tabbing, arrow keys all work
- [ ] Accessibility testing completed:
  - [ ] NVDA (Windows)
  - [ ] JAWS (Windows)
  - [ ] VoiceOver (macOS)
- [ ] Browser compatibility verified:
  - [ ] Chrome v120+
  - [ ] Firefox v123+
  - [ ] Safari v17+
  - [ ] Edge latest
- [ ] Bug verified fixed in UAT environment
- [ ] Ready for production release

---

## Notes
- This is a regression or initial implementation gap in keyboard navigation
- Keyboard handling for radio buttons should follow WAI-ARIA pattern: https://www.w3.org/WAI/ARIA/apg/patterns/radiobutton/
- Consider audit of all radio button groups on platform for similar issues
- May require update to component library if this is a reusable component
