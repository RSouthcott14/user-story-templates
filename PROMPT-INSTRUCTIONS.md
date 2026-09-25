# Copilot Instructions: Generating User Stories

Use these prompts with GitHub Copilot to generate high-quality, consistent user stories.

---

## Quick Start Prompts

### Generate a UI User Story

**Prompt:**
```
Generate a UI user story using the UI-Template from this repo.

Feature: [Feature Name]
Feature ID: [Ticket ID or leave blank]
Page Name: [Page Title]
User Role: [Pharmacist / Clinician / Admin]
Context: [Brief description of why this page exists]
Design Reference: DHCW Design System V2 — [Figma link or description]

Key Requirements:
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

Include all 6 scenario sections (Page Structure, Default State, Action, Navigation, Validation, Accessibility).
Use Gherkin format (Given/When/Then).
Use the structure in [`templates/UI-Template.md`](templates/UI-Template.md), validate every UI component against [`COMPONENTS.md`](COMPONENTS.md), and include the DHCW Design System V2 reference listed next to each component.
```

**Example:**
```
Generate a UI user story using the UI-Template from this repo.

Feature: Clinical Conditions Management - Template Selection
Feature ID: 577034
Page Name: Template Selection Page
User Role: Clinical Assessment Specialist
Context: After selecting a clinical condition, user chooses a template-based assessment path (standard vs. rapid assessment).
Design Reference: See Figma link in CCM project, frame "Template Selection"

Key Requirements:
- Display 2-3 template options as selectable cards
- Each card shows template name, description, and estimated time
- User can select one template and proceed
- Selection should be retained if user navigates back
- Accessibility: Full keyboard navigation and WCAG 2.1 AA compliance

Include all 6 scenario sections (Page Structure, Default State, Action, Navigation, Validation, Accessibility).
Use Gherkin format (Given/When/Then).
Use the structure in [`templates/UI-Template.md`](templates/UI-Template.md), validate every UI component against [`COMPONENTS.md`](COMPONENTS.md), and include the DHCW Design System V2 reference listed next to each component.
```

---

### Generate a BUG User Story

**Prompt:**
```
Generate a BUG user story using the BUG-Template from this repo.

Summary: [One-line defect description]
Component: [Module/Page/API affected]
Severity: [CRITICAL / HIGH / MEDIUM / LOW]
Environment: [DEV / TEST / UAT / PROD]
Browser: [Chrome v120 / Firefox v123 / Safari v17 / etc.]

Steps to Reproduce:
1. [Step 1]
2. [Step 2]
3. [Step 3]

Actual Result: [What happens (defective behavior)]
Expected Result: [What should happen]
Business Impact: [How does this affect users?]

Include 2 acceptance criteria scenarios (Defect Resolved, No Regression).
Use Gherkin format.
```

**Example:**
```
Generate a BUG user story using the BUG-Template from this repo.

Summary: Radio button arrow key navigation not working on clinical conditions page
Component: Clinical Conditions Selection / Radio Button Group
Severity: HIGH (blocks keyboard accessibility)
Environment: UAT
Browser: Chrome v120 on Windows 11

Steps to Reproduce:
1. Navigate to https://[app]/clinical-assessment/conditions
2. Click the first radio button to focus it
3. Press the DOWN arrow key (or RIGHT arrow)
4. Observe the result

Actual Result: Focus remains on first radio button; selection doesn't change
Expected Result: Focus moves to next radio button; selection updates
Business Impact: Blocks WCAG 2.1 AA keyboard navigation compliance; affects assistive tech users

Include 2 acceptance criteria scenarios (Defect Resolved, No Regression).
Use Gherkin format.
```

---

### Generate an ENABLER User Story

**Prompt:**
```
Generate an ENABLER user story using the ENABLER-Template from this repo.

Capability: [What technical capability is being delivered?]
Context: [Why is this needed? What stories depend on it?]
Tech Stack: [Technology/framework/library to use]
Scope In: [What will be built]
Scope Out: [What's explicitly NOT included]

Key Considerations:
- Dependencies: [What must exist first?]
- Risks: [Technical risks or unknowns]
- Performance Target: [e.g., < 50ms response time]
- Security: [Any security concerns?]

Downstream Stories:
- [Story ID]: [Story that depends on this]
- [Story ID]: [Story that depends on this]

Include 4 scenario sections (Technology Evaluation, Implementation, Quality & Testing, Documentation).
Use Gherkin format.
```

**Example:**
```
Generate an ENABLER user story using the ENABLER-Template from this repo.

Capability: OAuth 2.0 Authorization Server
Context: Currently authentication logic is duplicated across Web API, Mobile API, and Admin Portal. OAuth 2.0 server will centralize token management and enable single sign-on. Dependent stories: user login, multi-tenant access, role-based authorization.
Tech Stack: Spring Security OAuth2 (Java)
Scope In: Bearer token generation/validation, role-based access control, token refresh mechanism
Scope Out: Social login, multi-factor authentication, LDAP integration

Key Considerations:
- Dependencies: PostgreSQL database, Redis for caching
- Risks: Security of token generation; high volume of validation requests
- Performance Target: Token validation < 50ms (95th percentile)
- Security: HTTPS only, RS256 algorithm, rate limiting

Downstream Stories:
- US 585590: User login flow (Web)
- US 585591: User login flow (Mobile API)
- US 585592: Role-based access control

Include 4 scenario sections (Technology Evaluation, Implementation, Quality & Testing, Documentation).
Use Gherkin format.
```

---

### Generate a SPIKE User Story

**Prompt:**
```
Generate a SPIKE user story using the SPIKE-Template from this repo.

Research Topic: [What are we investigating?]
Background: [Why is this spike needed? What uncertainty are we reducing?]
Key Questions: 
- [Question 1]
- [Question 2]
- [Question 3]

Research Scope In: [What will be researched]
Research Scope Out: [What's explicitly NOT researched]

Expected Deliverables:
- [Deliverable 1]
- [Deliverable 2]

Estimated Effort: [Hours/days]

Include 4 scenario sections (Research & Documentation, Analysis & Findings, Risk Assessment, Recommendation & Decision).
Use Gherkin format.
```

**Example:**
```
Generate a SPIKE user story using the SPIKE-Template from this repo.

Research Topic: FluentAssertions v8 to v7 Downgrade Feasibility
Background: FluentAssertions v8 introduced commercial licensing. We need to decide: pay licensing fees for v8 or downgrade to v7 and accept potential maintenance burden.
Key Questions:
- What features do we use from v8 that aren't in v7?
- How many hours to refactor tests for v7?
- How long is v7 supported?
- What's the cost-benefit analysis?

Research Scope In: Feature comparison, code impact analysis, effort estimation, licensing cost analysis
Research Scope Out: Implementation of actual downgrade, evaluating alternative libraries

Expected Deliverables:
- Feature comparison matrix
- Code impact analysis report
- Migration effort estimate
- Financial cost-benefit analysis
- Recommendation document

Estimated Effort: 13 hours

Include 4 scenario sections (Research & Documentation, Analysis & Findings, Risk Assessment, Recommendation & Decision).
Use Gherkin format.
```

---

### Generate an API User Story

**Prompt:**
```
Generate an API user story using the API-Template from this repo.

Endpoint: [e.g., GET /api/patients/{id}]
HTTP Method: [GET / POST / PUT / PATCH / DELETE]
Purpose: [What does this endpoint do?]
Context: [Why is this API needed?]

Request:
- Parameters: [path, query, body]
- Headers: [Authorization, Content-Type, etc.]

Response (Success):
- Status: [200, 201, etc.]
- Body: [Sample JSON response]

Response (Error):
- Status: [400, 401, 403, 404, 500]
- Body: [Sample error response]

Authentication: [Bearer Token / API Key / OAuth 2.0]
Authorization: [Roles/permissions required]

Performance Target: [e.g., < 200ms]
Rate Limiting: [e.g., 1000 req/min]

Include 6-7 scenario sections (Success Path, Validation, Authentication, Authorization, Not Found, Data Integrity, Performance).
Use Gherkin format.
```

**Example:**
```
Generate an API user story using the API-Template from this repo.

Endpoint: GET /api/ipstatus
HTTP Method: GET
Purpose: Retrieve Independent Prescriber (IP) status for a user
Context: Clinical system needs to determine if user is an IP to control access level and available tools.

Request:
- Parameters: userId (UUID, required), cached (boolean, optional, default: true)
- Headers: Authorization: Bearer {token}, Content-Type: application/json

Response (Success):
- Status: 200 OK
- Body: {"userId": "...", "isIndependentPrescriber": true, "status": "Active", "registrationDate": "2023-06-15", "expiryDate": "2026-06-15"}

Response (Error):
- Status: 400 Bad Request, 401 Unauthorized, 404 Not Found, 500 Server Error

Authentication: Bearer Token (OAuth 2.0)
Authorization: User must have role: Pharmacist or Clinician

Performance Target: < 200ms (95th percentile)
Rate Limiting: 1000 requests/minute

Include 6-7 scenario sections (Success Path, Validation, Authentication, Authorization, Not Found, Data Integrity, Performance).
Use Gherkin format.
```

---

### Generate an ISSUE User Story

**Prompt:**
```
Generate an ISSUE user story using the ISSUE-Template from this repo.

Issue Title: [What decision or problem needs addressing?]
Context: [Current situation, why raised now?]

Options to Consider:
Option 1: [Title] - [Pros/Cons, cost, effort]
Option 2: [Title] - [Pros/Cons, cost, effort]
Option 3: [Title] - [Pros/Cons, cost, effort]

Recommendation: [Which option and why?]

Stakeholders: [Who's affected?]
Timeline: [When do we need to decide?]

Include 2 scenario sections (Decision Made, Action Defined).
Use Gherkin format.
```

**Example:**
```
Generate an ISSUE user story using the ISSUE-Template from this repo.

Issue Title: Resolve FluentAssertions Licensing Strategy: v8 (Commercial) vs v7 (Outdated)
Context: FluentAssertions v8 introduced commercial licensing. Budget review is Sept 30. We must decide: pay £2,000/year for v8 or downgrade to v7 (50-60 hours effort).

Options to Consider:
Option 1: Stay on v8, pay licensing - Zero migration effort, latest features, ongoing vendor dependency
Option 2: Downgrade to v7 - One-time 50-60 hour effort, no licensing, but v7 support ends June 2026
Option 3: Hybrid approach - Use v8 for new code, gradually migrate to v7 over 3 sprints

Recommendation: Downgrade to v7 - Best ROI (save £6,000 over 3 years), one-time effort, clear scope.

Stakeholders: Development team, Product Manager, Finance
Timeline: Decision by Sept 10, downgrade completion by Sept 30

Include 2 scenario sections (Decision Made, Action Defined).
Use Gherkin format.
```

---

## Tips for Best Results

### 1. **Provide Complete Context**
Copilot generates better stories when you give full context:
- Feature name and ID
- User role
- Design references or requirements
- Related stories
- Any constraints or dependencies

### 2. **Use Concrete Examples**
Instead of "requirements", give specific examples:
```
✗ "The form should work well"
✓ "The form should support DD/MM/YYYY and MM/YYYY date formats"
```

### 3. **Reference the Templates**
Always mention "using the [STORY_TYPE]-Template from this repo". Copilot will follow the structure automatically.

For UI stories, use [`templates/UI-Template.md`](templates/UI-Template.md) and the DHCW Design System V2. Validate component choices against [`COMPONENTS.md`](COMPONENTS.md) and include the design system reference listed next to each component.

### 4. **Specify the Format**
Always request "Use Gherkin format (Given/When/Then)" to ensure consistent output.

### 5. **Request Scenario Count**
For stories, specify how many scenarios and what sections:
```
"Include 4 scenario sections: Technology Evaluation, Implementation, 
Quality & Testing, Documentation. Use Gherkin format."
```

### 6. **Iterate if Needed**
If Copilot's output isn't quite right:
```
"Good start! Can you improve Scenario 3 to be more specific about the validation logic?"
```

---

## Validation Checklist

After Copilot generates a story, verify:

- [ ] Template structure is followed (correct sections)
- [ ] All scenarios use Gherkin format (Given/When/Then)
- [ ] Scenarios are numbered 1, 2, 3, ...
- [ ] Acceptance criteria are specific and verifiable
- [ ] No implementation details (HOW), only behavior (WHAT)
- [ ] Happy path and error path covered
- [ ] Edge cases considered
- [ ] Business value is clear
- [ ] Dependencies/related stories listed
- [ ] Definition of Done is complete
- [ ] For UI stories: Accessibility criteria included
- [ ] For API stories: All error codes covered
- [ ] For SPIKE stories: Clear deliverables
- [ ] For ENABLER stories: Technology decision clear

---

## Multi-Story Generation

To generate multiple related stories at once:

**Prompt:**
```
Generate 3 related user stories using templates from this repo:

Feature: Clinical Conditions Management
Feature ID: 577034
Related Stories:
1. UI Story: Template Selection Page - user chooses template
2. API Story: GET /api/templates - backend returns available templates
3. ENABLER Story: Template Caching Service - cache templates for performance

For each story, include:
- Full story content with all sections
- Scenario sections with Gherkin format
- Story dependencies/relationships noted

Generate each story using its appropriate template (UI-Template, API-Template, ENABLER-Template).
```

---

## Saving & Sharing

### Copy to Azure DevOps
1. Copilot generates story content
2. Copy the content (Ctrl+C)
3. Open Azure DevOps work item
4. Paste into description field
5. Format as needed (markdown should transfer)

### Copy to Excel/Spreadsheet
1. Copy scenario titles and descriptions
2. Paste into spreadsheet columns
3. Use for backlog planning/tracking

### Share with Team
1. Save generated story as .md file
2. Share via GitHub/repo
3. Team can review, comment, iterate

---

## Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| Copilot doesn't follow template structure | Restate: "Use the UI-Template structure with these sections: ..." |
| Scenarios aren't in Gherkin format | Add to prompt: "All scenarios must use Given/When/Then format." |
| Missing edge cases | Ask: "What edge cases should be included? Consider boundary conditions..." |
| Too technical / implementation details | Remind: "Focus on WHAT the user sees/does, not HOW it's implemented." |
| Scenarios aren't numbered | Say: "Number scenarios 1, 2, 3, ... sequentially." |

---

## Next Steps After Generation

1. **Review**: Read generated story; verify accuracy and completeness
2. **Refine**: Adjust scenarios, add missing details, fix typos
3. **Share**: Copy to Azure DevOps or team wiki
4. **Plan**: Add to backlog; estimate effort
5. **Assign**: Assign to developer/QA for implementation
6. **Track**: Reference story scenarios during dev/QA discussion

---

**Remember: Copilot is a productivity tool. You still need to review, refine, and validate generated content before committing to backlog!**
