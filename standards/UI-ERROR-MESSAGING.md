# UI Error Messaging Standards

**Platform:** Choose Pharmacy NextGen  
**Version:** 1.0  
**Applies To:** All UI user stories and acceptance criteria

---

## Overview

All error messages in UI user stories MUST follow a standardized format to ensure consistency, accessibility, and clarity for pharmacy users. This standard applies to validation errors, system errors, and any user-facing error messaging.

---

## Error Message Format Rules

### 1. Message Tone & Clarity

✅ **DO:**
- Write in plain language, avoiding jargon
- Use active voice and direct instructions
- Be specific: state WHAT is wrong and HOW to fix it
- Use "you" to address the user directly where appropriate
- Keep messages concise (ideally under 120 characters)

❌ **DON'T:**
- Use technical error codes or cryptic messages
- Blame the user ("You entered an invalid value")
- Use passive voice ("An error has occurred")
- Provide vague guidance ("Something went wrong")
- Capitalize entire messages (use standard capitalization)

### 2. Message Structure

Every error message MUST follow this structure:

```
[Problem] + [Guidance] + [Optional: Why it matters]
```

**Examples:**
- `"Patient ID is required"`  
- `"Assessment date must be today or in the past"`  
- `"Search term must be at least 2 characters"`  
- `"Please select a treatment option before continuing"`

### 3. Standardized Error Message Categories

All error messages MUST follow the exact format templates defined by Choose Pharmacy NextGen standards.

| Error Type | Message Format |
|-----------|---------|
| **Mandatory field** | "Enter *{fieldName}*" |
| **Mandatory field (from selection)** | "Select *{fieldName}*" |
| **Invalid format** | "Enter a valid *{fieldName}*" |
| **Correct format needed** | "Enter *{fieldName}* in the correct format" |
| **Out of range** | "Enter *{fieldName}* between *X* and *Y*" |
| **Character limit (maximum)** | "*{fieldName}* must be {N} characters or less" |
| **Character limit (minimum)** | "*{fieldName}* must be {N} characters or more" |
| **Special Character error** | "*{fieldName}* must not include *{x}* or *{y}*" |
| **Date must be in the past** | "*{fieldName}* must be in the past" |
| **Date must be in the future** | "*{fieldName}* must be in the future" |
| **Date must be today or in the past** | "*{fieldName}* must be today or in the past" |
| **Date missing year** | "*{fieldName}* must include a year" |
| **Date missing day** | "*{fieldName}* must include a day" |
| **Date missing month** | "*{fieldName}* must include a month" |
| **Invalid date (e.g., 13 months)** | "*{fieldName}* must be a real date" |
| **Date comparison: same or after** | "*{fieldNameOne}* must be the same or after *{fieldNameTwo}*" |
| **Date comparison: same or before** | "*{fieldNameOne}* must be the same or before *{fieldNameTwo}*" |
| **Date comparison: after another date** | "*{fieldNameOne}* must be after *{fieldNameTwo}*" |
| **Date between two dates** | "must be between *{fieldNameOne}* and *{fieldNameTwo}*" |
| **File Upload: size limit** | "The *{FileFormat}* must be smaller than 2MB" |
| **File is empty** | "The *{FileName}* is empty" |
| **File contains a virus** | "The *{FileName}* contains a virus" |
| **File is password protected** | "The *{FileName}* is password protected" |
| **File could not be uploaded** | "The *{FileName}* could not be uploaded - try again" |
| **Simultaneous file upload limit** | "You can only select up to *{N}* files at the same time" |
| **File must use template** | "The selected file must use the template" |

**Template Variable Rules:**
- `*{fieldName}*` — Replace with actual field label (e.g., "Patient ID", "Assessment Date")
- `*{FileFormat}*` — Replace with file type (e.g., "PDF", "Excel", "Word document")
- `*{FileName}*` — Replace with actual file name
- `*{N}*` — Replace with numeric limit
- `*{X}* and *{Y}*` — Replace with range values
- `*{fieldNameOne}*` and `*{fieldNameTwo}*` — Replace with two field labels for comparisons

**Examples in Practice:**
- "Enter Patient ID" (not "Patient ID is required")
- "Select Assessment Tool" (for selection fields)
- "Enter a valid NHS Number" (for format errors)
- "Assessment Date must be in the past" (for date constraints)
- "Notes must be 500 characters or less" (for character limits)
- "Appointment Start Date must be the same or after Appointment Date" (for date comparisons)

### 4. Validation Scenario Format (Acceptance Criteria)

All validation scenarios in acceptance criteria MUST include:

1. **Given:** A precondition that violates a business rule or validation requirement
2. **When:** The user action that triggers validation
3. **Then:** 
   - Exact error message text (in quotes)
   - Error location (near which field/element)
   - Visual styling (color, icon)
   - Accessibility announcement (role/aria-live)
   - Form state remains on current page
   - No data is persisted

**Template:**
```gherkin
Given [precondition that violates validation rule]
When the user [action that triggers validation]
Then an error message displays: "[Exact error message text]"
And the error appears [location: near field name / top of form]
And the error is styled with: [color: red / icon: warning triangle]
And the error message has role="alert" for screen reader announcement
And aria-live="polite" for dynamic content
And the user remains on [current page name]
And no data is persisted
```

**Example:**
```gherkin
Given the Assessment Tool field is empty
When the user clicks Continue
Then an error message displays: "Assessment tool is required"
And the error appears near the radio button group
And the error is styled in red with a warning icon
And the error message includes role="alert" for screen readers
And the user remains on the tool selection page
And focus returns to the Assessment Tool field
And no data is persisted
```

---

## Accessibility Requirements for Error Messages

All error messages MUST be accessible:

### Screen Reader Support
- `role="alert"` for immediate, important errors
- `aria-live="polite"` for dynamic content announcements
- `aria-describedby` to link field to error message
- Error announcement includes field name + error message

**Example HTML:**
```html
<div role="alert" aria-live="polite" class="error-message">
  Assessment tool is required
</div>

<fieldset aria-describedby="tool-error">
  <legend>Select Assessment Tool</legend>
  <!-- radio buttons -->
  <div id="tool-error" role="alert">
    Assessment tool is required
  </div>
</fieldset>
```

### Visual Design
- **Color:** Red or error color (must have 4.5:1 contrast against background)
- **Icon:** Warning/error icon (decorative, with `aria-hidden="true"`)
- **Position:** Immediately after field label or above form
- **Text Style:** Regular weight (not bold), clearly readable
- **Focus Management:** Focus returns to the field with error

### Keyboard Navigation
- Error messages do NOT trap focus
- User can Tab away from error message
- Focus management supports screen reader users

---

## Timing & Presentation

### When Errors Appear

| Trigger | Timing | Persistence |
|---------|--------|-------------|
| Missing required field | On blur or form submit | Until field is filled |
| Invalid format | On blur or form submit | Until corrected |
| Server validation | On form submit response | Until resubmitted/corrected |
| System error | Immediate | Persistent, with retry option |

### Error Dismissal
- Errors clear when user corrects the field
- Errors do NOT require explicit dismissal (e.g., Close button)
- System errors may include a "Try Again" button

---

## Validation Error Messages Reference

Use these exact message templates from the Choose Pharmacy NextGen standard. Replace placeholders in *{curly braces}* with actual values.

### Mandatory Field Errors
- `"Enter *{fieldName}*"` — For text/input fields
- `"Select *{fieldName}*"` — For selection/dropdown fields

**Examples:**
- "Enter Patient ID"
- "Select Assessment Tool"
- "Enter NHS Number"

### Format Errors
- `"Enter a valid *{fieldName}*"` — For invalid format
- `"Enter *{fieldName}* in the correct format"` — For format clarification

**Examples:**
- "Enter a valid NHS Number"
- "Enter Assessment Date in the correct format"

### Range/Character Limit Errors
- `"Enter *{fieldName}* between *X* and *Y*"` — For value ranges
- `"*{fieldName}* must be {N} characters or less"` — For maximum character limit
- `"*{fieldName}* must be {N} characters or more"` — For minimum character limit

**Examples:**
- "Enter Assessment Score between 0 and 10"
- "Notes must be 500 characters or less"
- "Search term must be 2 characters or more"

### Special Character Errors
- `"*{fieldName}* must not include *{x}* or *{y}*"` — When specific characters are not allowed

**Examples:**
- "Patient Name must not include numbers or special characters"
- "Reference Code must not include spaces or commas"

### Date Errors
- `"*{fieldName}* must be in the past"` — Date must be before today
- `"*{fieldName}* must be in the future"` — Date must be after today
- `"*{fieldName}* must be today or in the past"` — Date must be today or earlier
- `"*{fieldName}* must include a year"` — Year is missing
- `"*{fieldName}* must include a day"` — Day is missing
- `"*{fieldName}* must include a month"` — Month is missing
- `"*{fieldName}* must be a real date"` — Invalid date (e.g., 13 months, day 31 in Feb)

**Examples:**
- "Assessment Date must be in the past"
- "Appointment Date must be in the future"
- "Date of Birth must be in the past"
- "Consultation Date must include a year"

### Date Comparison Errors
- `"*{fieldNameOne}* must be the same or after *{fieldNameTwo}*"` — First date ≥ second date
- `"*{fieldNameOne}* must be the same or before *{fieldNameTwo}*"` — First date ≤ second date
- `"*{fieldNameOne}* must be after *{fieldNameTwo}*"` — First date > second date
- `"must be between *{fieldNameOne}* and *{fieldNameTwo}*"` — Date between two dates

**Examples:**
- "Appointment End Date must be the same or after Appointment Start Date"
- "Assessment Completion Date must be before Consultation Date"
- "Review Date must be after Initial Assessment Date"

### File Upload Errors
- `"The *{FileFormat}* must be smaller than 2MB"` — File size exceeds limit
- `"The *{FileName}* is empty"` — File is empty
- `"The *{FileName}* contains a virus"` — File failed virus scan
- `"The *{FileName}* is password protected"` — File cannot be processed
- `"The *{FileName}* could not be uploaded - try again"` — Upload failed (transient error)
- `"You can only select up to *{N}* files at the same time"` — Too many files selected
- `"The selected file must use the template"` — File does not match required template

**Examples:**
- "The Excel must be smaller than 2MB"
- "The patient-data.pdf could not be uploaded - try again"
- "You can only select up to 5 files at the same time"

---

## Example Acceptance Criteria with Error Messages

### Scenario 1: User sees validation error for empty required field

```gherkin
Given the user is on the assessment page
When the user clicks Submit without entering patient ID
Then an error message displays: "Enter Patient ID"
And the error appears near the Patient ID field
And the error is styled in red with a warning icon
And the error message has role="alert" for screen reader announcement
And aria-live="polite" for dynamic announcement
And the user remains on the assessment page
And focus moves to the Patient ID field
And no data is persisted
```

### Scenario 2: User sees validation error for selection field

```gherkin
Given the user is on the assessment page
When the user clicks Submit without selecting an assessment tool
Then an error message displays: "Select Assessment Tool"
And the error appears near the Assessment Tool field
And the error is styled in red with a warning icon
And the error message has role="alert" for screen reader announcement
And aria-live="polite" for dynamic announcement
And the user remains on the assessment page
And focus moves to the Assessment Tool field
And no data is persisted
```

### Scenario 3: User sees validation error for invalid date format

```gherkin
Given the user is on the assessment page
When the user enters "32/13/2024" in the Assessment Date field
Then an error message displays: "Assessment Date must be a real date"
And the error appears near the Assessment Date field
And the error is styled in red with a warning icon
And the error message has role="alert" for screen reader announcement
And aria-live="polite" for dynamic announcement
And the user remains on the assessment page
And focus returns to the Assessment Date field
And no data is persisted
```

### Scenario 4: User sees validation error for date constraint

```gherkin
Given the user is on the assessment page
When the user enters a future date in the "Assessment Date" field
Then an error message displays: "Assessment Date must be in the past"
And the error appears near the Assessment Date field
And the error is styled in red with a warning icon
And the error message has role="alert" for screen reader announcement
And aria-live="polite" for dynamic announcement
And the user remains on the assessment page
And focus returns to the Assessment Date field
And no data is persisted
```

### Scenario 5: User corrects validation error

```gherkin
Given an error message "Enter Patient ID" is displayed near the Patient ID field
When the user enters a valid patient ID
Then the error message disappears
And focus remains in the Patient ID field
And the field styling returns to normal state
```

### Scenario 6: Multiple validation errors displayed

```gherkin
Given the user is on the assessment page
When the user clicks Submit without entering Patient ID or selecting Assessment Tool
Then error messages display:
  - "Enter Patient ID" (near Patient ID field)
  - "Select Assessment Tool" (near Assessment Tool field)
And both errors are styled in red with warning icons
And both errors have role="alert"
And aria-live="polite" for dynamic announcement
And the user remains on the assessment page
And focus moves to the first error (Patient ID field)
And no data is persisted
```

---

## Rules for Acceptance Criteria Validation Scenarios

✅ **MUST:**
- Include exact error message text in quotation marks
- Specify error location (near field name, top of form, etc.)
- Include accessibility attributes (role="alert", aria-live)
- State that user remains on current page
- End with: "And no data is persisted"
- Describe visual styling (color, icon)
- Describe focus management (where focus returns)

❌ **MUST NOT:**
- Use vague error descriptions ("Error occurred")
- Omit exact error message text
- Forget accessibility requirements
- Assume data is persisted (explicitly state it is NOT)
- Use sub-numbering for error variations (each is a separate scenario)

---

## Integration with UI Story Template

All UI stories MUST include a **Validation scenario** in the Acceptance Criteria that:
1. Tests at least one validation error path
2. Uses exact error message text from this standard
3. Includes all accessibility requirements
4. Follows the scenario template above
5. Declares role="alert" and aria-live requirements

**Reference this file in UI stories:**
```markdown
### Validation

[Scenario for validation errors]

**Note:** Error messages follow [standards/UI-ERROR-MESSAGING.md](../standards/UI-ERROR-MESSAGING.md).
```

---

## Links & References

- [WCAG 2.1 Level AA Accessibility Standards](https://www.w3.org/WAI/WCAG21/quickref/)
- [ARIA: Alert Role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/alert_role)
- [ARIA: Live Regions](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions)
- [Form Validation Best Practices](https://www.smashingmagazine.com/2022/09/inline-validation-web-forms-ux/)
- [UI-Template.md](../templates/UI-Template.md) — UI story template
- [ACCESSIBILITY-CHECKLIST.md](../guides/ACCESSIBILITY-CHECKLIST.md) — WCAG 2.1 AA checklist
