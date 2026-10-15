# Mock Designs Guideline

## Rule: All UI Mock Designs Save to `/mock-designs` Folder

All interactive HTML mockups, prototypes, and UI design artifacts must be saved in the `mock-designs/` folder at the repository root.

---

## Purpose

- **Centralized location** for all UI mockups and prototypes
- **Easy discovery** for designers, developers, and stakeholders
- **Clear separation** between documentation (stories), examples, and interactive designs
- **Consistency** across the repository structure

---

## Guidelines

### File Naming Convention

Use descriptive kebab-case names that clearly indicate the feature:

```
✅ GOOD:
- dashboard-mock.html
- consultation-submission-form-mock.html
- patient-search-results-mock.html

❌ AVOID:
- mock.html
- design.html
- prototype-v2-final-ACTUAL.html
```

### File Location

All mock designs go in:

```
mock-designs/
├── dashboard-mock.html
├── consultation-submission-form-mock.html
├── patient-search-results-mock.html
└── [other-mock-designs].html
```

### Subdirectories (Optional)

For complex features with multiple related mockups, create subdirectories:

```
mock-designs/
├── dashboard/
│   ├── dashboard-mock.html
│   ├── dashboard-mobile.html
│   └── README.md
├── consultation-forms/
│   ├── consultation-form-mock.html
│   ├── consultation-validation-mock.html
│   └── README.md
```

### Documentation

Include a `README.md` at the root of `mock-designs/` or within subfolders describing:

- What the mockup demonstrates
- Which user story it supports
- Key features and interactions
- Browser/device compatibility notes
- Any known limitations

### Linking from User Stories

When referencing a mockup in a user story, use the path:

```markdown
**Design Reference**
- Mock-up provided: `mock-designs/dashboard-mock.html` in the repository
```

---

## Examples

### Example 1: Single Feature Mock

**User Story:** `stories/UI-ReportingDashboard.md`  
**Mock Design:** `mock-designs/dashboard-mock.html`  
**Reference in Story:**
```markdown
- Mock-up provided: `mock-designs/dashboard-mock.html` in the repository
```

### Example 2: Multi-File Feature Mock

**User Story:** `stories/UI-ConsultationSubmission.md`  
**Mock Designs:**
```
mock-designs/consultation-submission/
├── consultation-submission-form-mock.html
├── consultation-validation-mock.html
├── consultation-confirmation-mock.html
└── README.md
```

**Reference in Story:**
```markdown
- Mock-ups provided: `mock-designs/consultation-submission/` in the repository
- Forms demonstrate: submission flow, validation errors, confirmation screen
```

---

## Maintenance

- Review mockups during code review to ensure they remain accurate
- Update mockups if the user story acceptance criteria change
- Archive or remove mockups when the feature is fully implemented
- Keep mockup HTML clean and well-commented for future reference

---

## Current Mockups

| Mock Design | User Story | Status |
|---|---|---|
| `dashboard-mock.html` | `stories/UI-ReportingDashboard.md` | ✅ Active |

---

**Last Updated:** 2026-10-15
