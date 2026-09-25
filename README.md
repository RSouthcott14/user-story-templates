local GW

# User Story Templates for GitHub Copilot

This repository provides **standardized user story templates** for GitHub Copilot to generate consistent, high-quality user stories in the same format every time.

## 📋 Story Types Included

1. **UI** - User interface and user journey stories
2. **BUG** - Defect and issue tracking
3. **ENABLER** - Technical enablers and infrastructure
4. **SPIKE** - Research and investigation work
5. **API** - API endpoint and backend stories
6. **ISSUE** - Process/design decisions and discussions

## 🎨 Design System

**Figma Design System (DHCW Design System V2):**
https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2

Use this design system when creating UI stories. Always use the [`UI-Template`](templates/UI-Template.md), validate components against [`COMPONENTS.md`](COMPONENTS.md), and reference the DHCW Design System V2 link listed next to each component.

---

## 🚀 Quick Start

### For First-Time Users

1. Clone this repository: `git clone <repo-url>`
2. Read `README.md` (this file)
3. Review the `/examples` folder to see complete story examples
4. Use the appropriate template from `/templates` folder
5. Use the [`UI-Template`](templates/UI-Template.md) and reference the DHCW Design System V2 for UI stories

### Using with GitHub Copilot

When you want to generate a user story, provide:
1. **Story Type** (UI / BUG / ENABLER / SPIKE / API / ISSUE)
2. **Input/Context** (feature description, design, requirements)
3. **Feature ID** for Azure DevOps linking (e.g., "577034")
4. **Reference the template**: "Use the [STORY_TYPE] template from this repo"

**Example Prompt:**
```
Generate a UI user story using the template in this repo.
Context: A clinical assessment page where users select a scoring tool (FourMats or Centor).
The page has radio buttons, validation on continue, and must be WCAG 2.1 AA compliant.
Use Figma design at [link].
Feature ID: 577034 (for Azure DevOps linking)
```

### Creating in Azure DevOps

**Important:** Every user story created in Azure DevOps must be linked to a parent feature.

When requesting story creation, include:
- **Feature ID** — The feature this story belongs to (e.g., "577034")
- Stories will be automatically linked as **Parent → User Story** relationship
- See `DEVOPS-WORK-ITEM-STRUCTURE.md` for linking guidelines

## 📁 Repository Structure

```
user-story-templates/
├── README.md                          # This file
├── APPLICATION-CONTEXT.md             # Choose Pharmacy services overview
├── COPILOT-INSTRUCTIONS.md            # Prompting guide for consistent output
├── templates/
│   ├── UI-Template.md                 # UI story template
│   ├── BUG-Template.md                # Bug template
│   ├── ENABLER-Template.md            # Enabler template
│   ├── SPIKE-Template.md              # Spike template
│   ├── API-Template.md                # API template
│   └── ISSUE-Template.md              # Issue template
├── examples/
│   ├── UI-Example.md                  # Complete UI story example
│   ├── BUG-Example.md                 # Complete Bug example
│   ├── ENABLER-Example.md             # Complete Enabler example
│   ├── SPIKE-Example.md               # Complete Spike example
│   ├── API-Example.md                 # Complete API example
│   └── ISSUE-Example.md               # Complete Issue example
├── guides/
│   ├── ACCEPTANCE-CRITERIA.md         # How to write acceptance criteria
│   ├── SCENARIO-NUMBERING.md          # Scenario numbering best practices
│   ├── GHERKIN-FORMAT.md              # Gherkin (Given/When/Then) guide
│   └── ACCESSIBILITY-CHECKLIST.md     # WCAG 2.1 AA requirements
├── standards/
│   ├── README.md                      # Index of standards
│   ├── API-PATH-CONVENTIONS.md        # API base path, versioning, resource groups, HTTP methods
│   └── API-CODE-SYSTEMS.md            # API code system rules, naming, URI pattern, domain enumerations
├── WORKFLOW.md                        # Story generation & creation workflow
├── DEVOPS-WORK-ITEM-STRUCTURE.md      # Azure DevOps linking requirements
├── COMPONENTS.md                      # Design system component reference (31 DHCW components)
└── .github/
    └── copilot-instructions.md        # Auto-loaded instructions for GitHub Copilot
```

## 📖 Using Each Template

### **1. UI Stories** 
For user-facing features, pages, and journeys.

**When to use:** Creating a new page, feature, or user interaction

**Key sections:**
- User Story Card (As a/I want/So that)
- Story Context (why it matters)
- Out of Scope
- Related/Dependent Stories
- Acceptance Criteria (6 sections):
  - Page Structure & Content
  - Default State
  - Action
  - Navigation
  - Validation
  - Accessibility

**→ See `/templates/UI-Template.md` and `/examples/UI-Example.md`**

---

### **2. BUG Stories**
For defects, issues, and unexpected behavior.

**When to use:** Reporting a defect in existing functionality

**Key sections:**
- Description (clear summary)
- Environment (DEV/TEST/UAT/PROD)
- Pre-requisites
- Steps to Reproduce
- Actual Result (what happens)
- Expected Result (what should happen)
- Impact (business/user impact)
- Acceptance Criteria
- Evidence (screenshots/videos)
- Related Work Items

**→ See `/templates/BUG-Template.md` and `/examples/BUG-Example.md`**

---

### **3. ENABLER Stories**
For technical infrastructure, dependencies, and capabilities.

**When to use:** Building technical foundations for other stories

**Key sections:**
- User Story Card
- Context (why needed)
- Objective (what capability is delivered)
- Scope (in/out)
- Key Considerations (dependencies, risks, assumptions)
- Acceptance Criteria (4 areas):
  - Technology Evaluation
  - Implementation
  - Quality & Testing
  - Documentation

**→ See `/templates/ENABLER-Template.md` and `/examples/ENABLER-Example.md`**

---

### **4. SPIKE Stories**
For research, investigation, and decision-making work.

**When to use:** Exploring options, evaluating solutions, or gathering requirements

**Key sections:**
- User Story Card
- Objectives (what questions need answering)
- Key Questions (research questions)
- Scope (what will be researched)
- Acceptance Criteria:
  - Research & Documentation
  - Analysis & Findings
  - Risk Assessment
  - Recommendation & Decision
- Deliverables (what gets produced)

**→ See `/templates/SPIKE-Template.md` and `/examples/SPIKE-Example.md`**

---

### **5. API Stories**
For backend APIs, endpoints, and services.

**When to use:** Building API functionality or endpoints

**Key sections:**
- User Story Card
- Background (why needed)
- Objective (what the API provides)
- Endpoint Details (Method, Endpoint, Path)
- Request (structure, parameters)
- Response (success and error responses)
- Error Handling (400, 401, 404, 500)
- Dependencies (database, external services)
- Acceptance Criteria (7 areas):
  - API Contract
  - Business Rules & Logic
  - Authentication & Authorization
  - Validation & Error Handling
  - Audit & Logging
  - Quality & Testing
  - Documentation

**→ See `/templates/API-Template.md` and `/examples/API-Example.md`**

---

### **6. ISSUE Stories**
For decisions, design discussions, and process issues requiring resolution.

**When to use:** Capturing decisions, discussing options, or raising concerns

**Key sections:**
- Title
- Context (why raised)
- Issue/Problem Statement
- Current Position
- Considerations (factors to account for)
- Options (Option 1, Option 2, etc.)
- Recommendation
- Dependencies
- Risks/Impact
- Action Required
- Outcome/Decision (filled after resolution)
- Related Work Items

**→ See `/templates/ISSUE-Template.md` and `/examples/ISSUE-Example.md`**

---

## 🤖 Copilot Prompting Guide

See `COPILOT-INSTRUCTIONS.md` for:
- Exact prompts to use
- Context to provide
- How to reference designs/requirements
- Tips for consistent output

**Quick Example:**
```
Generate a [STORY_TYPE] user story using the template in the user-story-templates repo.

Title: [What you want to build]
Context: [Background/why]
Design Reference: DHCW Design System V2 — [Figma link or description]
Key Requirements:
- [Requirement 1]
- [Requirement 2]
```

---

## 🔧 API Standards

**See `standards/` for:**
- Base path and versioning conventions (`/api/v1/`)
- Resource group structure and patient-scoped path patterns
- HTTP method usage
- Path segment naming style

**Use this when creating API stories to:**
- Ensure endpoint paths follow the established pattern
- Correctly scope patient vs non-patient resources
- Apply the right HTTP method for the operation

---

## 📚 Application Context

**See `APPLICATION-CONTEXT.md` for:**
- Choose Pharmacy NextGen overview and purpose
- All 7 services: CAS, STTT, UTI, DMR, EMS, Contraception, Independent Prescribers
- 28 Common Ailments supported
- User roles and responsibilities
- Data integration points
- Key metrics and outcomes
- Clinical workflow pathways

**Use this when creating stories to:**
- Understand the clinical context
- Reference correct service pathways
- Identify appropriate user roles
- Ensure clinical accuracy in acceptance criteria

---

## ✨ Key Features

✅ **Consistency** — Same structure every time, whether UI, API, or Enabler  
✅ **Quality** — Includes best practices (Gherkin format, accessibility, WCAG 2.1 AA)  
✅ **Scalability** — Share with your entire team; everyone produces stories in the same format  
✅ **Traceability** — Scenario numbering for easy reference during dev/QA  
✅ **Completeness** — Templates cover all necessary information (no ambiguity)  
✅ **GitHub Copilot Ready** — Designed for AI-assisted generation

---

## 📊 Acceptance Criteria Best Practices

All story types include acceptance criteria in a consistent format:

### **Standard Sections** (Used in UI Stories)
1. **Page Structure & Content** — What the user sees
2. **Default State** — Initial appearance
3. **Action** — User interactions
4. **Navigation** — Page flow
5. **Validation** — Error handling
6. **Accessibility** — WCAG 2.1 AA compliance

### **Scenario Numbering**
Each acceptance criterion is numbered (Scenario 1, 2, 3...) for:
- Easy reference during dev/QA discussion
- Tracking which scenarios have been validated
- Traceability in defect triage

---

## 📋 Gherkin Format (Given/When/Then)

All acceptance criteria use **Gherkin format** for clarity:

```gherkin
Scenario: User selects a clinical scoring tool
  Given the user is on the clinical scoring selection page
  When I click the "FourMats" radio button
  Then "FourMats" is selected
  And the selection is visually highlighted
```

---

## 🎯 Quick Reference: Which Template to Use?

| Story Type | Use When | Example |
|-----------|----------|---------|
| **UI** | Building a new page, feature, or user interaction | "Record patient symptoms" form |
| **BUG** | Reporting a defect in existing functionality | "Radio button layout broken" |
| **ENABLER** | Building technical infrastructure or dependencies | "Configure OAuth integration" |
| **SPIKE** | Researching options or investigating decisions | "Evaluate FluentAssertions licensing" |
| **API** | Creating an API endpoint or service | "GET /api/ipstatus endpoint" |
| **ISSUE** | Discussing design decisions or process questions | "Should we use v7 or v8?" |

---

**Last Updated**: September 2026  
**Status**: Ready for GitHub  
**Maintainers**: [Your BA/PO Team]
