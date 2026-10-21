# SPIKE: Payment Report Generation Strategy

## Story Type: SPIKE | Effort: 20 hours | Status: Ready for Sprint

---

## Description

### Story Card
**As a** Development Team & NWSSP Integration Lead  
**I want** to investigate the optimal technical approach for generating monthly payment reports  
**So that** we can select a solution that meets NWSSP requirements, ensures data accuracy, and is maintainable at scale

### Background
Feature 608726 requires Choose Pharmacy to generate 7 standardized monthly reports for NWSSP payment and reimbursement processing. These reports must:
- Run reliably on the 1st of each month
- Aggregate clinical and service data from multiple entities (consultations, medicines supplied, registrations)
- Support audit trails and data validation
- Output to a network share (\\rqfvtsrvsqlpms1\choosepharmacyimport)
- Ensure only completed, valid activity is included

The team needs to decide between:
1. **Native PostgreSQL Approach** (stored procedures + scheduled jobs)
2. **ETL/Reporting Tool** (Informatica, Azure Data Factory, Tableau Server)
3. **Hybrid Approach** (PostgreSQL export + .NET export service)

This spike will evaluate each option against functional requirements, performance, maintainability, cost, and operational complexity.

### Scope

**In Scope:**
- Technical feasibility of each approach given PostgreSQL in Azure
- Data aggregation requirements for all 7 report types
- Monthly scheduling reliability (1st of month)
- Network share output integration (SMB/CIFS)
- Audit trail and data validation capabilities
- Performance testing with full database
- Operational complexity and runbook requirements
- Cost analysis (licensing, infrastructure, dev effort)
- Proof of concept for leading approach

**Out of Scope:**
- UI/API for report download or viewing (depends on this decision)
- Implementation of chosen solution (separate story)
- NWSSP data format specifications (assume provided)
- Historical report backfill (addressed post-implementation)

---

## Research Objectives

1. **Technical Feasibility**: Assess which approaches are viable given PostgreSQL in Azure, network connectivity, and existing .NET stack
2. **Performance Assessment**: Determine if each approach can aggregate 7 reports with full pharmacy dataset in acceptable time
3. **Reliability & Scheduling**: Evaluate monthly scheduling options and failure recovery mechanisms for each approach
4. **Data Quality & Audit**: Compare how each approach handles data validation, completeness checks, and audit logging
5. **Operational Readiness**: Assess monitoring, alerting, and support burden for each approach
6. **Cost-Benefit Analysis**: Calculate licensing, infrastructure, and ongoing support costs vs. development effort
7. **Implementation Effort**: Estimate dev hours for each approach from research to production-ready

---

## Key Questions to Answer

- **PostgreSQL Native Approach**: Can PostgreSQL stored procedures reliably aggregate all 7 reports monthly? What's the query complexity for medicine/consultation joins?
- **Scheduling**: Which approach provides the most reliable monthly scheduling? (Azure Scheduler vs. SQL Agent vs. .NET background job)
- **Network Output**: How should reports be written to \\rqfvtsrvsqlpms1\choosepharmacyimport? (Azure VM with SMB access, application-level write, hybrid?)
- **Data Completeness**: How do we ensure only "completed" activity is included? What validation logic is required for each report type?
- **Performance**: With full production data, how long will each approach take to generate all 7 reports? Is single-run acceptable or must they be parallelized?
- **Audit Trail**: Which approach provides the best audit logging of report generation, data quality checks, and delivery?
- **Monitoring & Alerts**: How should we detect failed runs, data anomalies, or delivery failures? What's the operational model?
- **Cost**: What are licensing costs for each approach? What's the infrastructure cost in Azure?
- **Team Skills**: Does the team have expertise to support each approach? (PostgreSQL, ETL tools, .NET services)
- **Maintenance Burden**: If NWSSP requires report format changes, how easily can each approach adapt?

---

## Acceptance Criteria

**Total Scenarios: 4**

### Scenario 1: Technical Feasibility Research & Documentation

```gherkin
Given the spike has been started
When technical feasibility is researched for each approach
Then research findings are documented including:
  - PostgreSQL Native: Sample queries for 2–3 complex reports (CAS medicines + registrations)
  - ETL Tool: Licensing availability in UK NHS environment
  - Hybrid: .NET service architecture and Azure service dependencies
And data models are reviewed to confirm joins/aggregations are possible
And network share connectivity is verified (SMB from Azure)
And existing .NET technology stack is assessed for compatibility
And findings are compiled into a technical feasibility report
```

### Scenario 2: Performance, Reliability & Data Quality Assessment

```gherkin
Given technical feasibility is confirmed
When performance and reliability are evaluated
Then findings include:
  - PostgreSQL Native: Query performance estimate (execution time with full dataset)
  - ETL Tool: Scheduling reliability and SLA guarantees
  - Hybrid: Background job execution model and failure recovery options
And monthly scheduling options are compared with failure handling
And data validation approaches are documented:
  - How each approach ensures only "completed" activity is included
  - Which approach best supports audit logging of data quality checks
And data completeness validation logic is sketched for each approach
And a performance comparison matrix is created
```

### Scenario 3: Operational Readiness, Cost & Implementation Effort Estimate

```gherkin
Given feasibility and performance data are collected
When operational readiness and costs are assessed
Then findings are documented:
  - Monitoring: Alert strategy for each approach (failed runs, data anomalies)
  - Support Model: Runbooks and escalation paths for each approach
  - Cost Analysis: Licensing + infrastructure + dev effort for each approach
  - Implementation Effort: Hours to deliver production-ready solution
  - Skills Gap: Team expertise assessment (PostgreSQL, ETL tools, .NET)
  - Maintenance Burden: Estimate for NWSSP format changes
And cost-benefit analysis is completed for 3-year horizon
And operational complexity is ranked (High/Medium/Low)
```

### Scenario 4: Recommendation & Decision

```gherkin
Given all research is complete
When the team makes a recommendation
Then recommendation is documented with:
  - Recommended Approach: [PostgreSQL Native / ETL Tool / Hybrid] because [reasoning]
  - Rationale: Key trade-offs that drive the recommendation
  - Risks: Technical, operational, and financial risks of chosen approach
  - Mitigation: Proposed mitigations for identified risks
  - Next Steps: Phased implementation plan (pilot, full rollout)
  - Implementation Stories: Ready for backlog (from architecture forward)
And recommendation is approved by Product Lead & Tech Lead
And stakeholders (NWSSP liaison, Ops team) are informed
```

---

## Deliverables

- [ ] **Technical Feasibility Report**
  - Overview of 3 approaches (PostgreSQL, ETL tool, Hybrid)
  - Sample query designs for complex report types (medicines + registrations)
  - Architecture diagram for leading approach(es)
  - Network connectivity assessment (SMB output)

- [ ] **Performance & Reliability Assessment**
  - Query execution time estimates (PostgreSQL approach)
  - Scheduling reliability matrix (monthly scheduling options)
  - Data quality & audit trail comparison
  - Sample validation logic for report completeness

- [ ] **Operational Readiness & Cost Analysis**
  - Monitoring & alerting strategy per approach
  - Support runbooks and escalation procedures
  - Cost breakdown (licensing, infrastructure, dev effort)
  - Implementation effort estimate (hours per approach)
  - Team skills assessment
  - 3-year TCO (Total Cost of Ownership) comparison

- [ ] **Proof of Concept** (for leading approach)
  - Sample PostgreSQL queries for 2–3 reports OR
  - ETL pipeline mockup OR
  - .NET background job skeleton
  - Demonstrates aggregation logic and output format

- [ ] **Recommendation Document**
  - Executive summary (1 page)
  - Technical recommendation with rationale
  - Risk register and mitigation strategy
  - Implementation roadmap (Phase 1: pilot, Phase 2: full rollout)

- [ ] **Implementation Stories** (created in backlog, ready to plan)
  - Architecture & Setup
  - Report Generation (per report type or batch)
  - Scheduling & Automation
  - Monitoring & Alerting
  - Testing & Validation

---

## Success Criteria

- [ ] All 9 key questions answered with evidence
- [ ] Recommendation is data-driven (not assumption-based)
- [ ] Performance estimate includes realistic PostgreSQL execution times
- [ ] Cost analysis compares all 3 approaches fairly (3-year TCO)
- [ ] Operational model is defined (who runs monthly reports? how are failures handled?)
- [ ] Team alignment on recommendation (dev team, product, ops)
- [ ] Stakeholders (NWSSP, business) informed and approve decision
- [ ] Implementation stories are ready for sprint planning (can start immediately)

---

## Definition of Done

- [ ] Technical feasibility research conducted (all 3 approaches evaluated)
- [ ] Performance testing completed (query times, scheduling reliability)
- [ ] Operational readiness assessed (monitoring, support model)
- [ ] Cost analysis reviewed by finance/product team
- [ ] Proof of concept demonstrates viability of leading approach
- [ ] Recommendation peer-reviewed by Tech Lead & Architect
- [ ] Stakeholders informed of decision and roadmap
- [ ] Implementation stories created and ready for backlog
- [ ] Ready for sprint planning

---

## Estimated Effort
**20 hours** across 1 sprint:
- Technical research & feasibility (PostgreSQL queries, ETL evaluation): 6 hours
- Performance testing & reliability assessment: 4 hours
- Operational readiness & cost analysis: 4 hours
- Proof of concept (leading approach): 4 hours
- Recommendation & documentation: 2 hours

---

## Research Approach

### Technical Feasibility (6 hours)
1. **PostgreSQL Native**: Design queries for CAS Prescribed Items + CAS Registration reports (most complex joins)
   - Review ChoosePharmacy.Domain.Types for consultation/medicine/pharmacy entities
   - Draft SQL for monthly aggregation with completion status filtering
   - Test query performance assumptions with sample data
2. **ETL Tool**: Research Azure Data Factory capabilities and NHS UK license availability
   - Confirm SMB/file output support from Azure
   - Review scheduling and error handling
3. **Hybrid (.NET)**: Design background job architecture (Hangfire, Quartz, Azure Service Bus)
   - Assess how existing Choose Pharmacy API service integrates with reports
   - Evaluate outbound connectivity to network share

### Performance & Reliability (4 hours)
4. Test leading approach with realistic dataset (or estimate based on schema)
5. Evaluate monthly scheduling (Azure Scheduler vs. SQL Agent vs. background job)
6. Design data validation and audit logging strategy
7. Document failure recovery procedures

### Operational & Cost Analysis (4 hours)
8. Define monitoring, alerting, and escalation procedures for each approach
9. Create support runbooks (what to do if reports don't run, data looks wrong)
10. Collect licensing costs for ETL tools (if pursuing that path)
11. Calculate dev effort and 3-year cost comparison

### Proof of Concept (4 hours)
12. Implement leading approach on isolated branch
    - PostgreSQL: Deploy sample queries to test environment
    - ETL: Build minimal pipeline for one report type
    - Hybrid: Deploy background job skeleton to dev environment
13. Verify output format meets NWSSP expectations (or validate against spec)

### Recommendation & Documentation (2 hours)
14. Synthesize findings and create recommendation document
15. Obtain approval from Tech Lead, Product Lead, and stakeholders
16. Draft implementation stories ready for backlog

---

## Related Work Items

- **Depends on**: Feature 608726 (Payment Reports NWSSP) — this spike answers "how to build it"
- **Blocks**: Implementation stories for report generation (all downstream work)
- **Related**: API stories (if reports exposed via endpoint), UI stories (if report download UI), Monitoring/Alerting enablers

---

## Notes

- **Assumption**: NWSSP has provided formal specification for report format and fields (CSV, XML, structured export). If not, clarify before spike conclusion.
- **Contingency**: If PostgreSQL approach is too complex (query performance poor), ETL tool becomes default recommendation.
- **Timeline**: Spike should complete in current sprint. Decision & implementation planning kick off in next sprint.
- **Stakeholders to Involve**:
  - NWSSP Integration Lead (confirm requirements, validate recommendation)
  - Tech Lead / Architect (technical feasibility & long-term maintainability)
  - Operations Team (monitoring, support model, runbooks)
  - Product Manager (business case, cost-benefit)
  - Database Administrator (PostgreSQL performance, scheduling)

---

## Azure & PostgreSQL Context Notes

- **Database**: PostgreSQL in Azure (Cloud or VM-based)
- **Technology Stack**: .NET (C#), existing Choose Pharmacy API service
- **Output**: Network share (\\rqfvtsrvsqlpms1\choosepharmacyimport) — requires SMB/CIFS connectivity from Azure
- **Scheduling**: Multiple options — Azure Scheduler, SQL Agent (if available on PostgreSQL), .NET background job (Hangfire/Quartz), or Azure Functions
- **Audit Requirements**: NHS Wales audit standards likely apply — full data lineage and report generation audit trail required
