# Azure DevOps Work Item Structure & Linking

## 📋 Overview

This document defines the required work item hierarchy and linking structure for all user stories created through this repository and added to Azure DevOps.

---

## 🔗 Work Item Hierarchy

### Standard Hierarchy (Choose Pharmacy NextGen Project)

```
Epic / Feature
├── User Story (UI, BUG, ENABLER, etc.)
├── User Story
└── User Story

Feature (Parent)
├── User Story (Child/Related)
├── User Story
└── User Story
```

---

## ✅ Linking Requirements

### For User Stories

**Every user story created must have ONE of the following:**

1. **✅ PREFERRED: Feature as Parent**
   - Relationship Type: `Parent`
   - When to use: Story is directly part of a feature delivery
   - Example: US-609534 → Feature-577034 (Parent)

2. **✅ ALTERNATIVE: Related Link to Feature**
   - Relationship Type: `Related`
   - When to use: Story must exist independently but is related to feature
   - Example: US-609534 → Feature-577034 (Related)

3. **✅ ALTERNATIVE: Related Link to Epic**
   - Relationship Type: `Related`
   - When to use: Story is part of larger epic initiative
   - Example: US-609534 → Epic-000 (Related)

---

## 🚫 What NOT to Do

❌ Create a user story with NO links to feature/epic  
❌ Create orphan stories with no parent/related work items  
❌ Create multiple parent links (Azure DevOps allows only 1 parent)  
❌ Link a story to a story (link to feature/epic instead)

---

## 📊 Link Types in Azure DevOps

### Parent/Child Relationship
- **Use for:** Feature → User Stories (1:many)
- **Meaning:** Story is a deliverable of the feature
- **Effect:** Story appears under feature in backlog hierarchy
- **Limit:** Each story can have ONLY 1 parent
- **When creating:** Story is part of core feature delivery

**Example:**
```
Feature 577034: CCM Condition Assessment
  └─ Parent Link
    └─ US-609534: Select Condition (Child)
    └─ US-609535: Assess Symptoms (Child)
    └─ US-609536: Save Assessment (Child)
```

### Related Link
- **Use for:** When parent/child doesn't fit
- **Meaning:** Story is connected but not a direct child
- **Effect:** Shows link on work item, no hierarchy change
- **Limit:** No limit on related links
- **When creating:** Story is related to feature but not a direct deliverable

**Example:**
```
Feature 577034: CCM Condition Assessment
  └─ Related Link
    └─ US-609534: Select Condition (Related)
    └─ SPIKE-609537: Research SNOMED CT Integration (Related)
```

---

## 🔄 Creating Stories with Proper Linking

### Step 1: Identify the Feature/Epic
- Determine which feature this story belongs to
- Get the feature ID from Azure DevOps
- Example: Feature 577034 = "CCM Condition Assessment Template Selection"

### Step 2: Create the Story
When creating story, note:
- Story ID will be auto-generated (e.g., 609534)
- Story title, description, acceptance criteria populated
- Tags assigned
- **Link to parent feature** (Step 3)

### Step 3: Link to Parent Feature

**Option A: During Story Creation (Preferred)**
- If creating directly in Azure DevOps UI:
  1. Create new User Story
  2. In story form, scroll to "Links" section
  3. Click "Add link" or "Link to parent feature"
  4. Select "Parent" relationship type
  5. Search for feature (e.g., "577034")
  6. Click "Link"

**Option B: After Story Creation (via API)**
```powershell
# Link story to parent feature
$storyId = 609534
$featureId = 577034
$relationship = "Parent"

# API call to create link
# POST /work-items/{storyId}/links
```

**Option C: Manually in DevOps UI**
1. Open story (US-609534)
2. Click "Links" tab
3. Click "Add link" or "+ Link"
4. Choose relationship: "Parent"
5. Enter feature ID: 577034
6. Click "Save"

---

## 📝 Process: Story Creation with Proper Linking

### When I Create Story in DevOps:

1. ✅ Generate story draft (in chat)
2. ✅ You review and approve
3. ✅ You provide: **Feature ID** (e.g., 577034)
4. ✅ I create story in Azure DevOps
5. ✅ I automatically link to feature as:
   - **Parent** (if parent/child is appropriate)
   - **Related** (if parent/child can't be used)
6. ✅ Return story link to you
7. ✅ Story appears under feature in backlog

---

## 🎯 Story Type → Linking Strategy

| Story Type | Primary Link | Link Type | Reason |
|-----------|-------------|----------|--------|
| **UI** | Feature | Parent | Direct feature deliverable |
| **BUG** | Feature | Parent | Part of feature fix |
| **ENABLER** | Epic or Feature | Parent/Related | Infrastructure for features |
| **SPIKE** | Feature | Related | Research supporting feature |
| **API** | Feature | Parent | Backend for feature |
| **ISSUE** | Epic | Related | Strategic decision supporting multiple features |

---

## 🔍 Checking Work Item Links

### In Azure DevOps UI:
1. Open work item (US-609534)
2. Scroll down to "Links" section
3. See all parent/child/related links
4. Links show:
   - Link type (Parent, Child, Related)
   - Related work item ID and title
   - Relationship direction

### In Azure DevOps API:
```json
"relations": [
  {
    "rel": "System.LinkTypes.Hierarchy-Reverse",
    "url": "https://dev.azure.com/.../wit/workItems/577034",
    "attributes": {
      "name": "Parent",
      "comment": "CCM Condition Assessment Feature"
    }
  }
]
```

---

## ⚠️ Common Issues & Solutions

### Issue 1: "Cannot add Parent link - already has parent"
**Problem:** Story already has a parent, trying to add another  
**Solution:** Remove old parent link first, then add new one  
**Prevention:** Verify story has no parent before linking

### Issue 2: "Cannot create child link to User Story"
**Problem:** Trying to link story to another story  
**Solution:** Link to feature/epic instead, or use "Related" link  
**Prevention:** Always link stories to features, not other stories

### Issue 3: Story appears orphaned (no links)
**Problem:** Story created but not linked to any feature  
**Solution:** Manually add parent/related link in Azure DevOps  
**Prevention:** Add feature ID when requesting story creation

### Issue 4: Multiple parent links showing
**Problem:** Azure DevOps shows story with multiple parents  
**Solution:** Not possible - Azure DevOps only allows 1 parent per item  
**If needed:** Use "Related" links for secondary connections

---

## 📋 Linking Checklist

When creating a user story in Azure DevOps, verify:

- [ ] Story ID created (e.g., 609534)
- [ ] Story title correct
- [ ] Description populated
- [ ] Acceptance criteria complete
- [ ] Tags assigned (UI, CCM, CAS, etc.)
- [ ] **Feature ID identified** (e.g., 577034)
- [ ] Story linked to feature as **Parent** OR **Related**
- [ ] Link visible in story's "Links" tab
- [ ] Link also visible on feature work item
- [ ] Story appears in feature's child items backlog

---

## 🔄 Multi-Story Scenario

### Scenario: Creating 3 stories for a feature

**Feature:** 577034 - CCM Condition Assessment Template Selection

**Stories to Create:**
1. US-609534: Select Condition (linked as Parent)
2. US-609535: Assess Symptoms (linked as Parent)
3. US-609536: Save Assessment (linked as Parent)

**Result in Azure DevOps:**
```
Feature 577034: CCM Condition Assessment Template Selection
├─ US-609534: Select Condition (Parent-Child)
├─ US-609535: Assess Symptoms (Parent-Child)
└─ US-609536: Save Assessment (Parent-Child)
```

**Each story also shows:**
- Parent: Feature 577034
- Related: Other stories with "relates to" or "blocks"

---

## 📞 Requesting Story Creation

### Required Information for Story Creation:

```
Please create a user story:

Story Type: UI
Feature: CCM Condition Assessment - Select Condition
Feature ID: 577034  ← REQUIRED FOR LINKING
Page Name: Select condition
User Role: pharmacy professional
Key Requirements: [list requirements]
Design Reference: [Figma link]
```

**If you don't provide Feature ID:**
- I'll ask you for it before creating
- Story can't be properly linked without it
- Takes extra step to link after creation

---

## 🎯 Best Practices

### DO ✅
- ✅ Always link stories to features/epics
- ✅ Use Parent link when story is direct feature deliverable
- ✅ Use Related link when story supports but isn't core to feature
- ✅ Verify link before closing creation process
- ✅ Include Feature ID in story creation request
- ✅ Check feature backlog to see linked stories

### DON'T ❌
- ❌ Create orphan stories with no links
- ❌ Try to create multiple parent links (Azure DevOps won't allow)
- ❌ Link stories to other stories (link to features instead)
- ❌ Forget to link after story creation
- ❌ Mix up feature ID with story ID
- ❌ Create links manually after if you can request linking during creation

---

## 🔗 Reference: Feature IDs in Choose Pharmacy NextGen

Common features for linking:
- **577033**: CCM - Condition Assessment Overview
- **577034**: CCM - Condition Assessment Template Selection (Example)
- **577035**: CCM - Symptoms Assessment
- **577036**: CCM - Assessment Save
- **585586**: CAS - Radio Button Selection Pattern
- **594519**: IP Status Validation
- **606759**: Independent Prescriber Validation

*Update this list as new features are created*

---

## 📚 Related Documentation

- **WORKFLOW.md** — Story generation process
- **COMPONENTS.md** — Design system validation
- **README.md** — Repository overview
- **ACCEPTANCE-CRITERIA.md** — Acceptance criteria patterns

---

## 🎓 Training

### For BAs/POs:
- Always provide Feature ID when requesting stories
- Verify story appears under feature in backlog
- Check linked work items are correct

### For Developers:
- Use parent link to navigate from feature to stories
- Use child stories to organize feature work
- Reference parent feature for context

### For QA:
- Use links to find related stories
- Report bugs linked to parent feature
- Track story progress through backlog

---

**Last Updated:** 2026-09-14  
**Version:** 1.0

---

## Example: Story 609534 Linking

**Before Creating:**
```
Feature 577034: CCM Condition Assessment Template Selection
(No child stories yet)
```

**After Creating Story 609534:**
```
Feature 577034: CCM Condition Assessment Template Selection
├─ Parent Link
  └─ US-609534: Select Condition
```

**View in Story 609534:**
```
Links:
├─ Related to: Feature 577034 (Parent)
│  └─ "CCM Condition Assessment Template Selection"
```

**View in Feature 577034:**
```
Related Items:
├─ Child: US-609534 (Select Condition)
└─ Links show in "Links" tab
```

---

**Questions? Check WORKFLOW.md for story creation process.** 🚀
