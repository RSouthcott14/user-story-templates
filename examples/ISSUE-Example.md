# ISSUE Example: Should We Use FluentAssertions v8 or Downgrade to v7?

## Story Type: ISSUE | Status: Decision Pending

---

## Title
Resolve FluentAssertions Licensing Strategy: v8 (Commercial) vs v7 (Outdated)

---

## Context

### Background
On August 2024, Fluent Assertions v8 introduced a commercial licensing model. Our team uses FluentAssertions extensively in automated tests across Web and API solutions. We need to decide: continue with v8 and pay licensing fees, or downgrade to v7 and accept potential maintenance risks?

### Issue Statement
**We must make a strategic decision on FluentAssertions versioning before the next budget review (Sept 30).** This affects:
- Licensing budget (£X per year)
- Dev effort (downgrade = 50+ hours)
- Long-term maintenance (v7 is unsupported after 2026)

---

## Current Position

**Current State:** 
- All Web and API solutions using FluentAssertions v8.x (latest)
- No licensing fees paid yet (grace period ending Sept 30)
- After Sept 30: either pay annual licensing fee or downgrade

**Constraint:** 
- Budget freeze approaching; licensing decision must be made before Sept 15
- Migration window: Sept 15 - Sept 30 (2 weeks)

---

## Considerations

- **Licensing Cost**: £2,000/year for commercial use of v8
- **Development Effort**: Estimated 50-60 hours to refactor tests for v7 (2 developers, 1 sprint)
- **Support Timeline**: v7 supported until June 2026; v8 supported indefinitely
- **Feature Parity**: We use ~80% of v8's assertion methods; v7 alternatives exist for most
- **Risk Profile**: v7 is stable but aging; v8 is newer but has licensing strings attached
- **Team Morale**: Some devs prefer newer features in v8; others prefer avoiding licensing complexity

---

## Options Under Consideration

### Option 1: Stay on FluentAssertions v8 (Pay Licensing)
**Description:** Continue using v8, pay annual licensing fee, accept commercial terms

**Pros:**
- No migration effort required (0 hours)
- Latest features available
- Full vendor support
- Easier for new team members (modern library)
- No technical debt accumulation

**Cons:**
- Annual cost: £2,000/year (£6,000 over 3 years)
- License compliance overhead
- Vendor lock-in (if terms change)
- Tied to commercial licensing model

**Effort:** Minimal (just pay the fee)  
**Cost:** £2,000/year

---

### Option 2: Downgrade to FluentAssertions v7 (One-Time Effort)
**Description:** Downgrade to v7.x, refactor all test code, remove licensing dependency

**Pros:**
- No annual licensing fees (save £6,000 over 3 years)
- No vendor lock-in
- One-time effort, then done
- Simplifies procurement/compliance

**Cons:**
- 50-60 hours dev effort (1 sprint for 2 devs)
- Support ends June 2026 (18 months)
- Fewer features; some assertions require workarounds
- Potential for bugs during migration
- Long-term: v7 will be unsupported (tech debt after June 2026)

**Effort:** 50-60 hours  
**Cost:** Dev time ~£2,000 (1 sprint), but saves £6,000 in licensing

---

### Option 3: Hybrid Approach (Gradual Migration)
**Description:** Use v8 for new code, gradually migrate existing tests to v7 over 3 sprints

**Pros:**
- Spreads out effort (not a 1-sprint crunch)
- Allows experimenting with v7 assertions
- Lower risk (can stop midway if problems emerge)
- Buys time to evaluate v8 future

**Cons:**
- Maintains two versions temporarily (complexity)
- Extends licensing fee costs (6 more months of v8 fees: £1,000)
- Requires discipline (new code goes to v7 only)
- Total cost higher than pure downgrade

**Effort:** 60-70 hours spread over 3 sprints  
**Cost:** £1,000 licensing + dev time

---

## Recommendation

**Recommended Option:** Option 2 - Downgrade to v7

**Rationale:**
1. **Financial**: Saves £6,000 over 3 years (strong ROI: 3:1)
2. **Simplicity**: One-time effort, then no ongoing licensing overhead
3. **Risk Acceptable**: v7 is stable; refactoring is low-risk
4. **Timeline**: 2-week window before Sept 30 is sufficient

**Key Benefits:**
- Eliminates licensing dependency
- One-time sprint of focused work
- No vendor lock-in
- Clear cost savings

**Risks Mitigated:**
- v7 support ends June 2026: mitigation = start v8 alternatives evaluation Q1 2026
- Migration effort = high but manageable in 1 sprint
- Bugs during migration = mitigated by comprehensive test suite

---

## Dependencies

- This decision blocks: Implementation stories for downgrade (if approved)
- Blocked by: Budget approval for v8 licensing (if Option 1 chosen)
- Related: Spike 585585 - "Investigate FluentAssertions v8 to v7 Downgrade" (provides data for this decision)

---

## Risks & Impact

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|-----------|
| Migration fails; bugs introduced | Low (20%) | Medium | Comprehensive testing; rollback plan ready |
| v7 support ends; tech debt accumulates | Medium (60%) | Medium | Start evaluation Q1 2026 for v8 alternatives |
| Team resistance to downgrade | Low (15%) | Low | Communicate cost savings; involve team in planning |
| Migration overshoots timeline | Low (20%) | Medium | Allocate 2 full developers for 1 sprint; no context switching |

---

## Acceptance Criteria

### Scenario 1: Decision Made
```gherkin
Given the issue has been discussed with tech lead and product manager
When the team reaches agreement
Then the decision is documented: "Downgrade to v7"
And the rationale is recorded in this issue
And stakeholders (finance, dev team) are informed
```

### Scenario 2: Implementation Stories Created
```gherkin
Given the decision to downgrade has been made
When implementation stories are generated
Then stories are ready for backlog:
  - Web solution v7 migration
  - API solution v7 migration
  - Test refactoring and validation
  - Rollback plan
And stories include effort estimates and acceptance criteria
```

---

## Action Required

- [ ] Tech Lead reviews recommendation and data (from Spike 585585)
- [ ] Product Manager approves budget decision
- [ ] Finance confirms no licensing purchase order needed (Option 2)
- [ ] Development team estimates effort (validation/refinement)
- [ ] Implementation stories created and prioritized
- [ ] Communication to team: decision, timeline, impact
- [ ] Downgrade scheduled for Sprint [next] (if approved)

---

## Outcome / Decision (To be filled after resolution)

**Final Decision:** [To be decided by Tech Lead + Product Manager]

**Decided By:** [Name/Role]  
**Date Decided:** [To be filled]  
**Justification:** [Why this decision?]

**Next Steps:** 
- If **Option 2 approved**: Create implementation stories, schedule for next sprint
- If **Option 1 approved**: Purchase v8 commercial license, continue as-is
- If **Option 3 approved**: Create hybrid migration plan

---

## Related Work Items

- Spike 585585: "Investigate FluentAssertions v8 to v7 Downgrade" (provides supporting analysis)
- Future: "Evaluate v8 Alternatives" (Spike, scheduled Q1 2026 if v7 downgrade chosen)

---

## Stakeholders

- **Primary Decision Maker:** Tech Lead (Dev)
- **Affected Teams:** Web Team, API Team (all developers using FluentAssertions)
- **Key Reviewers:** Product Manager, Finance Lead, Architect
- **Informed:** Full dev team

---

## Timeline

- **By Sept 10:** Decision finalized
- **By Sept 15:** Implementation stories created (if downgrade approved)
- **Sept 16 - Sept 30:** Downgrade execution (1 sprint)
- **Oct 1:** v7-only codebase live

---

## Notes
- **Important**: Spike 585585 has detailed financial analysis and migration effort estimates
- **Contingency**: If v7 downgrade fails during Sept 16-30 window, we can extend through October (gives another month buffer before licensing decision due Oct 1)
- **Future Consideration**: Start evaluating v8 alternatives (like AssertJ) in Q1 2026 before v7 support ends
