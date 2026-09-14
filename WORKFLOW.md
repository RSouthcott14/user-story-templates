# User Story Generation Workflow

## 📋 Process Overview

This document describes the workflow for generating user stories using this repository and creating them in Azure DevOps.

---

## 🔄 User Story Generation Process

### Step 1: Request Story (You Ask)
You provide:
- **Feature/Page name** and context
- **User role** who will use the feature
- **Key requirements** or acceptance criteria
- **Design reference** (Figma links, screenshots)
- **Story type** (UI, BUG, ENABLER, SPIKE, API, ISSUE)

**Example:**
```
Generate a UI user story using the UI-Template from this repo.

Feature: CCM Condition Assessment - Select condition
Page Name: [UI: CCM Condition Assessment - Select condition (CAS Pathway)]
User Role: pharmacy user
Context: This page allows a pharmacy professional to identify and select 
the patient's presenting condition...
Key Requirements:
- User can start typing, when they have typed 3 characters or more the search is performed
- User can then select a condition from the returned list
- When no results are found return "no results found"
```

---

### Step 2: Story Generated in Chat (I Draft)
I will:
1. ✅ Review your requirements against the template
2. ✅ Validate components against COMPONENTS.md design system
3. ✅ Draft the complete story with all acceptance criteria
4. ✅ Use Gherkin format (Given/When/Then) for all scenarios
5. ✅ Display the full story in the chat for your review

**What you'll see:**
- Complete story with all 6 sections (Page Structure, Default State, Action, Navigation, Validation, Accessibility)
- Component validation notes
- Definition of Done checklist
- Design System reference

---

### Step 3: Review & Approve (You Review)
You review the drafted story and:
- ✅ Approve as-is
- ❌ Request changes (tell me what to modify)
- ❓ Ask questions about specific scenarios

**If you want changes:**
Tell me: "Update Scenario 3 to include...", "Change the validation to...", "Add a new scenario for..."

I'll update and redraft the story in the chat.

---

### Step 4: Create in Azure DevOps (I Create)
Once approved, I'll ask:

> **Would you like me to create this story in Azure DevOps now?**
> - [ ] Yes, create it
> - [ ] No, just save the draft
> - [ ] Create it with modifications (tell me what to change)

**If you choose "Yes, create it":**

I'll need:
- [ ] **Project name** (e.g., "Choose Pharmacy NextGen")
- [ ] **Feature ID** (e.g., "577034") — story will be added under this feature
- [ ] **Story title** (if different from generated title)
- [ ] **Story type in DevOps** (User Story, Bug, Task, etc.)

Then I'll:
1. ✅ Create the work item in Azure DevOps
2. ✅ Populate all fields (title, description, acceptance criteria)
3. ✅ Link to feature/epic
4. ✅ Add tags (UI, CCM, CAS, etc.)
5. ✅ Return the DevOps link to you

---

## 📝 Story Sections Included

Every generated story includes:

| Section | Purpose | Gherkin Scenarios |
|---------|---------|-------------------|
| **Story Card** | As A / I Want / So That | N/A |
| **Context** | Business background & pathway | N/A |
| **Out of Scope** | What's NOT included | N/A |
| **Related Stories** | Dependencies & linked work | N/A |
| **Scenario 1** | Page Structure & Content | 1 scenario (Given/When/Then) |
| **Scenario 2** | Default State | 1 scenario |
| **Scenario 3** | User Interaction / Action | 2-5 sub-scenarios (a, b, c, d, e...) |
| **Scenario 4** | Navigation / Flow | 2-4 sub-scenarios (a, b, c, d...) |
| **Scenario 5** | Validation / Error Handling | 2-5 sub-scenarios (a, b, c, d, e...) |
| **Scenario 6** | Accessibility (WCAG 2.1 AA) | 4-9 sub-scenarios (a, b, c, d, e, f, g, h, i...) |
| **Definition of Done** | Checklist | 20-25 items (dev, QA, design verification) |
| **Component Checklist** | Design System validation | Components used + accessibility requirements |
| **Notes** | Dev & QA guidance | Implementation hints, testing notes |

---

## 🎯 What You Can Modify

During review, you can ask for:

### Content Changes
- "Add a scenario for [specific edge case]"
- "Remove Scenario 5d (not applicable)"
- "Change the button text from 'Save' to 'Confirm'"
- "Update the help text to..."

### Component Changes
- "Use a different button style (outline instead of filled)"
- "Switch from dropdown to radio buttons"
- "Add a loading spinner during search"

### Accessibility Changes
- "Add keyboard shortcut guidance"
- "Specify screen reader requirements"
- "Add mobile responsiveness details"

### Scope Changes
- "This should support multiple condition selections"
- "Include color customization options"
- "Add export functionality"

---

## 💾 Story Storage

Once created in Azure DevOps, stories are:
1. ✅ Linked to feature/epic
2. ✅ Assigned to team/sprint (your choice)
3. ✅ Ready for refinement with team
4. ✅ Tracked in backlog & sprints
5. ✅ Can be refined further by team

**Copy in Repo:**
- Accepted stories may also be saved to `/stories/` folder as markdown backup
- Useful for historical reference and pattern matching

---

## 🔍 Story Validation

Before creation in DevOps, each story is validated for:

### Template Compliance
- ✅ All required sections present
- ✅ Gherkin format used correctly (Given/When/Then)
- ✅ Scenarios logically ordered
- ✅ Definition of Done checklist included

### Design System Compliance
- ✅ Components exist in COMPONENTS.md
- ✅ Accessibility requirements met (WCAG 2.1 AA)
- ✅ Color contrast verified
- ✅ Keyboard navigation specified
- ✅ Screen reader support detailed

### Business Logic
- ✅ Story matches feature context
- ✅ Requirements are clear and testable
- ✅ Acceptance criteria cover happy path + edge cases
- ✅ Error handling defined
- ✅ Dependencies called out

---

## 📊 Story Generation Times

Typical generation times:

| Story Type | Scenarios | Generation Time | Review Time |
|-----------|-----------|-----------------|------------|
| **UI** | 6 sections (3-9 sub-scenarios) | 2-3 minutes | 5-10 min |
| **BUG** | 2-3 sections | 1-2 minutes | 2-5 min |
| **ENABLER** | 4 sections | 2-3 minutes | 5-10 min |
| **SPIKE** | 4 sections | 2-3 minutes | 5-10 min |
| **API** | 6-7 sections | 3-4 minutes | 10-15 min |
| **ISSUE** | 2 sections | 1-2 minutes | 3-5 min |

**Total time from request to DevOps creation: 5-20 minutes**

---

## 🚀 Quick Start Example

### Your Request:
```
Generate a UI story for a login form.
Context: NHS professional logging into Choose Pharmacy app
Key requirements:
- Email/password fields with validation
- "Forgot password" link
- "Sign in" button
- Remember me checkbox
```

### What Happens:
1. ⏳ I draft story (2 min) with all 6 scenarios
2. 💬 Story appears in chat
3. ❓ I ask: "Review this story. Does it look correct?"
4. ✅ You: "Looks good, create it in DevOps"
5. 📋 I create work item in DevOps
6. 🔗 I return link: https://dev.azure.com/...#/wit/edit/123456

**Total time: 5-10 minutes**

---

## 📞 Common Questions

### Q: Can I request changes after seeing the draft?
**A:** Yes! Tell me what to change and I'll redraft. No problem.

### Q: Will the story be saved to the repo?
**A:** It can be. If you want a markdown copy in `/stories/`, I can save it.

### Q: Can I generate multiple stories at once?
**A:** Yes! Give me a list of stories and I'll draft them one by one.

### Q: What if I want to batch create stories in DevOps?
**A:** I can draft multiple stories, then create them all in DevOps in one go.

### Q: Can you update existing stories in DevOps?
**A:** Yes, if you provide the story ID. I can add scenarios, modify acceptance criteria, update descriptions.

### Q: What if a story needs more information?
**A:** I'll ask clarifying questions before generating. Tell me what's unclear.

---

## ✅ Workflow Checklist

For each story generation request:

- [ ] Request includes: Feature name, page context, user role
- [ ] Key requirements clearly stated
- [ ] Design reference provided (Figma, screenshots)
- [ ] Story type specified (UI/BUG/ENABLER/SPIKE/API/ISSUE)
- [ ] Story drafted with all sections
- [ ] Components validated against COMPONENTS.md
- [ ] Story reviewed and approved by you
- [ ] (Optional) Changes requested and redrafted
- [ ] Final approval received
- [ ] Ready to create in Azure DevOps
- [ ] Project name & feature ID confirmed
- [ ] Story created in DevOps
- [ ] Link returned to you
- [ ] Story linked to feature/epic
- [ ] Marked ready for refinement

---

## 🔄 Iterative Refinement

Stories can be refined iteratively:

1. **Generation Phase** (Chat) — Initial draft with all sections
2. **Review Phase** (Chat) — You review and request changes
3. **Refinement Phase** (Chat) — I update story based on feedback
4. **Approval Phase** (Chat) — You approve final version
5. **Creation Phase** (DevOps) — Story created in work item tracking
6. **Team Refinement Phase** (DevOps) — Team refines in sprint planning

---

## 📚 Related Resources

- **Templates:** `/templates/` folder — templates for each story type
- **Examples:** `/examples/` folder — completed examples
- **Guides:** `/guides/` folder — Gherkin, Accessibility, Acceptance Criteria
- **Components:** `COMPONENTS.md` — design system validation
- **Copilot Instructions:** `COPILOT-INSTRUCTIONS.md` — prompting tips

---

## 🎉 Ready to Generate!

Next time you need a story, just provide:
1. Feature & page name
2. User role & context
3. Key requirements
4. Design reference

I'll handle the rest! 🚀

---

**Last Updated:** 2026-09-14  
**Version:** 1.0
