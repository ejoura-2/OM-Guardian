# STATEMENT OF WORK

---

## Guardian Application Migration & Consolidation

This Statement of Work ("**SOW**") is entered into pursuant to the Master Services Agreement ("**MSA**") between **I4C Information Technology Consulting Inc.** ("**Provider**" or "**i4C**") and **Owens & Minor, Inc.** ("**Client**"), effective as of [MSA Effective Date]. Capitalized terms not defined in this SOW have the meanings set forth in the MSA.

---

## 1. Parties

**Client:**  
Owens & Minor, Inc.  
10900 Nuckols Road, Suite 400  
Glen Allen, VA 23060

**Authorized Business Contact:**  
Name: [TBD]  
Email: [TBD]

**Provider:**  
I4C Information Technology Consulting Inc.  
201-1283 Teron Road  
Ottawa, Ontario K2K 0J7, Canada

**Authorized Signatory:**  
Chris Atack, Chief Operating Officer  
Email: catack@i4c.com

---

## 2. Background and Objectives

Client currently operates two parallel Foundry workspaces: the "Guardian" application (in the "Carbon" workspace) serving approximately 200 internal teammates including Customer Advocates and Sales personnel, and the "Beacon/Insight" application serving external customers. This dual-system architecture has resulted in technical redundancy, duplicated data objects, and unnecessary compute costs.

The objective of this SOW is for Provider to supply experienced Foundry engineering services to support:

i. consolidation of the Guardian application into the Beacon (Insight) workspace to create a single source of truth;

ii. technical migration and merger of the Ontology, Data Pipelines, and User Interface "as-is" to the new environment;

iii. implementation of a security model supporting both internal (cross-account) and external (single-account) access patterns within a unified ontology;

iv. targeted pipeline optimization to leverage Snowflake for data processing where feasible, reducing Foundry compute costs; and

v. decommissioning of redundant Guardian-specific artifacts to realize cost savings.

**Target Completion:** June 30, 2026, subject to timely Client provisioning and approvals as outlined in Section 11.

---

## 3. Scope of Services

Provider shall perform the following services (the "Services") in close coordination with Client stakeholders. This section describes scope at a high level.

### 3.1 Discovery & Technical Assessment

Provider will conduct a comprehensive assessment of both the Guardian (Carbon) and Beacon workspaces to establish the baseline for migration planning.

**3.1.1 Access & Onboarding**
- Obtain Foundry workspace access (Carbon & Beacon environments)
- Obtain Snowflake EDW credentials and Omni/SQL Server connection details
- Obtain repository access for all relevant codebases
- Obtain JIRA access for project tracking
- Conduct stakeholder introductions and communication channel setup

**3.1.2 Ontology Object Cataloging**
- Create complete inventory of ALL Guardian ontology objects (Object Types, Properties, Links, Actions)
- Create complete inventory of ALL Beacon ontology objects
- Perform object-by-object comparison to identify:
  - Duplicate objects (e.g., Item, Customer, Product Health)
  - Guardian-unique objects requiring migration
  - Schema differences requiring reconciliation
- Document object dependencies and relationship hierarchies

**3.1.3 Pipeline & Data Source Inventory**
- Catalog all Guardian pipelines with: name, type, schedule, inputs/outputs, estimated compute cost
- Catalog all Beacon pipelines with same detail
- Map primary data sources (Omni/SQL Server, Snowflake EDW) and ingestion patterns
- Identify high-cost pipelines and optimization opportunities
- Document pipeline dependencies and sequencing

**3.1.4 Function & Logic Audit**
- Catalog all TypeScript functions and Python transforms (e.g., `gargantuan_function`, `type-fit-functions`)
- Identify duplicated logic between Guardian and Beacon repositories
- Document function dependencies and calling patterns
- Identify candidates for consolidation

**3.1.5 Security Model Assessment**
- Document current Guardian permission structure (users, groups, roles, object access)
- Document current Beacon permission structure
- Identify security gaps and risky patterns (e.g., ad hoc grants, overly broad permissions)
- Assess requirements for dual-access model (internal cross-account vs. external restricted)

**3.1.6 Workshop & UI Audit**
- Document Guardian Workshop modules (Item Health, DC Item Health) with full feature inventory
- Document widgets, filters, actions, navigation patterns
- Create feature parity checklist for migration validation
- Identify any Beacon UI components that may conflict or require coordination

**3.1.7 Risk Assessment & Migration Planning**
- Identify blockers, dependencies, and compatibility issues
- Create Risk Register with mitigation strategies
- Develop detailed Migration Plan with sequencing and rollback points
- Define success criteria and acceptance test scenarios

**Deliverable:** Migration Assessment & Technical Plan (including Object Comparison Matrix, Pipeline Inventory, Security Assessment, Risk Register)

---

### 3.2 Architecture & Design

Provider will design the target-state architecture for the unified environment prior to implementation.

**3.2.1 Unified Security Model Design**
- Design Object Security Policies (OSPs) for dual-access requirements:
  - Internal Users: Cross-account visibility (Customer Advocates viewing multiple Health Systems)
  - External Users: Strict row-level isolation (each customer sees only their own data)
- Design user group structure and role definitions
- Design permission inheritance model
- Document security model for Client approval

**3.2.2 Ontology Merge Strategy**
- Define merge strategy for each duplicate object:
  - Which object becomes the "Single Source of Truth"
  - Property mapping and reconciliation approach
  - Link/relationship migration approach
- Design unified schema accommodating both Guardian and Beacon requirements
- Document migration sequence and dependencies

**3.2.3 Pipeline Architecture Design**
- Design optimized pipeline architecture leveraging:
  - Snowflake push-down processing for heavy transformations (joins, aggregations)
  - Direct Snowflake ingestion where applicable ("Zero-ETL")
  - Foundry Pipeline Builder for orchestration and control flow
- Define pipeline migration sequence
- Estimate cost savings from optimization

**3.2.4 Detailed Workplan Development**
- Create activity-level project plan with:
  - Task sequencing and dependencies
  - Resource assignments
  - Milestone definitions
  - Rollback/contingency points
- Define parallel run approach and data validation methodology

**Deliverable:** Architecture Design Document + Detailed Workplan

---

### 3.3 Backend Implementation

Provider will execute the backend migration including ontology, pipelines, and security.

**3.3.1 Security Implementation**
- Implement approved OSPs and Row-Level Security (RLS) in Beacon workspace
- Configure user groups and role assignments
- Migrate existing user group memberships (e.g., "Customer Advocate Users")
- Validate access patterns through test scenarios

**3.3.2 Ontology Object Migration**
- Execute object migration per approved merge strategy:
  - Merge duplicate objects into unified definitions
  - Migrate Guardian-unique objects to Beacon
  - Reconcile properties, links, and relationships
- Refactor object configurations as needed for unified environment

**3.3.3 Function & Transform Consolidation**
- Refactor Python transforms to align with unified ontology
- Consolidate TypeScript functions into single repository
- Eliminate duplicated logic between Guardian and Beacon codebases
- Update function references and dependencies

**3.3.4 Pipeline Migration & Optimization**
- Migrate pipelines per approved architecture:
  - Reconfigure data source connections
  - Implement Snowflake push-down transformations
  - Retire redundant pipeline branches
- Validate pipeline outputs against legacy data
- Document pipeline operations (purpose, inputs/outputs, schedules, monitoring)

**3.3.5 Data Validation**
- Execute data accuracy validation between legacy Guardian and new Beacon outputs
- Document validation methodology and results
- Remediate data discrepancies

**Deliverable:** Unified Ontology + Consolidated Pipelines + Security Configuration

---

### 3.4 Frontend Implementation

Provider will recreate the Guardian user interface within the Beacon workspace.

**3.4.1 Workshop Module Recreation**
- Recreate Guardian "Item Health" Workshop module in Beacon workspace
- Recreate Guardian "DC Item Health" Workshop module in Beacon workspace
- Configure module navigation and access controls

**3.4.2 Feature Parity Implementation**
- Implement all Guardian features per parity checklist:
  - Filtering by Customer/Account Groups
  - Column Configuration capabilities
  - Action Items and Critical Items lists
  - DC Level Statistics and Health views
  - Note taking functionality (add, edit, view notes on Items/Products)
  - Status management (modifying or clearing item statuses)
- Ensure UX consistency with legacy Guardian experience

**3.4.3 Internal Testing**
- Conduct Provider-side functional testing of all Workshop features
- Validate data display accuracy
- Test cross-browser compatibility
- Document and remediate identified defects

**Deliverable:** Guardian Application (Beacon Space) with full feature parity

*Note: This scope assumes a lift-and-shift of existing UI functionality. Integration of Guardian views into the Insight product navigation (e.g., unified tabs, cohesive branding) is subject to Product Team definition and is considered a future enhancement.*

---

### 3.5 Validation & User Acceptance

Provider will facilitate formal validation including parallel run and user acceptance testing.

**3.5.1 Parallel Run Execution**
- Configure parallel run environment with both systems operational
- Define data comparison checkpoints and validation rules
- Execute parallel run for agreed duration
- Generate data comparison report documenting accuracy metrics

**3.5.2 User Acceptance Testing (UAT)**
- Support Client SME validation of all Workshop features
- Provide test scenarios and expected results documentation
- Facilitate UAT sessions as requested
- Track and triage UAT findings

**3.5.3 Security Validation**
- Execute test scenarios validating:
  - Internal users can access cross-account data appropriately
  - External users cannot view other customers' data (isolation testing)
- Document security test results

**3.5.4 Performance Validation**
- Validate Workshop response times against Client-approved performance criteria
- Validate pipeline run times and compare to legacy
- Document performance metrics and improvements

**3.5.5 Defect Remediation**
- Prioritize and remediate UAT-identified defects
- Conduct regression testing following fixes
- Obtain Client sign-off on resolution

**Deliverable:** Parallel Run Validation Report + UAT Sign-off

---

### 3.6 Cutover & Transition

Provider will support the transition of users to the new environment.

**3.6.1 Cutover Planning**
- Develop Cutover Runbook including:
  - Detailed step-by-step cutover activities
  - Timing and scheduling (recommend low-usage period)
  - Communication templates for user notification
  - Rollback procedures and decision criteria
- Obtain Client approval of cutover plan

**3.6.2 User Transition Execution**
- Support transition of ~200 internal users to Beacon-based Guardian
- Monitor for issues during transition window
- Provide rapid response to cutover-related incidents

**3.6.3 Hypercare Support**
- Provide post-cutover hypercare support for a mutually agreed number of business days (to be confirmed during cutover planning)
- Prioritize and remediate post-go-live defects
- Track issues through JIRA with agreed SLAs

**3.6.4 Decommissioning Preparation**
- Compile list of Carbon workspace artifacts for retirement:
  - Redundant pipelines
  - Duplicate ontology objects
  - Legacy Workshop modules
  - Unused repositories
- Estimate cost savings from decommissioning
- Present Decommissioning Recommendation for Client approval
- Disable/archive approved legacy artifacts in Carbon (no permanent deletion)

**Deliverable:** Successful Cutover + Cutover Runbook + Decommissioning Recommendation

---

### 3.7 Handover & Closure

Provider will complete formal handover and project closure activities.

**3.7.1 Documentation Finalization**
- Finalize technical documentation:
  - Unified ontology documentation (object definitions, relationships)
  - Pipeline documentation (operations runbook, monitoring, troubleshooting)
  - Security model documentation (groups, roles, OSP rules)
  - Known issues log and resolution status

**3.7.2 Knowledge Transfer**
- Conduct knowledge transfer sessions with Client technical team covering:
  - System architecture and design decisions
  - Operations and monitoring procedures
  - Troubleshooting common issues
  - Future enhancement considerations

**3.7.3 Cost Savings Validation**
- Measure and document compute cost reduction:
  - Before: Guardian (Carbon) + Beacon compute costs
  - After: Unified Beacon compute costs
- Quantify storage savings from decommissioned duplicates
- Present cost savings report

**3.7.4 Project Closure**
- Conduct project retrospective / lessons learned
- Obtain final deliverable acceptance
- Archive project artifacts
- Formal project closure

**Deliverable:** Documentation Package + Knowledge Transfer Complete + Cost Savings Report

---

## 4. Out-of-Scope Items

Unless expressly agreed in a written change order executed in accordance with the MSA, the following are out of scope for this SOW:

- End-user training or change management for business teams (Client to lead business adoption and internal communication regarding the platform transition).
- Material redesign or re-architecture of the Ontology or User Interface beyond the targeted amendments required for migration, consolidation, and feature parity.
- Integration of Guardian functionality into the "Insight" commercial application framework (e.g., merging UI capabilities into a single unified product experience, cohesive branding) unless explicitly defined as a migration requirement.
- Material refactoring or redesign of system ontology architecture beyond targeted amendments required for object consolidation.
- Performance optimization of existing Snowflake or external database ETL processes outside of Foundry pipeline scope.
- Data migration from external systems not already integrated with the Foundry platform.
- Support for systems, applications, or environments not explicitly made available by Client for the Guardian migration scope of this SOW.
- Production support outside agreed working hours and the agreed hypercare window, or 24x7 on-call coverage.
- Security certification, penetration testing, or compliance audits (except reasonable engineering support within scope for permissions implementation).
- Permanent deletion of legacy artifacts (Provider will disable/archive approved artifacts; Client approves and executes permanent deletion).

---

## 5. Deliverables and Acceptance

### 5.1 Deliverables

| # | Deliverable | Description | Acceptance Criteria | Format / Location |
| :--- | :--- | :--- | :--- | :--- |
| D1 | **Migration Assessment & Technical Plan** | Comprehensive inventory of objects, pipelines, functions, and security; Object Comparison Matrix; Pipeline Inventory; Risk Register; Detailed migration plan and sequencing. | Client written acknowledgment of approach; migration strategy approved. | Document + JIRA |
| D2 | **Architecture Design Document** | Target-state architecture including unified security model design, ontology merge strategy, pipeline optimization design, detailed workplan. | Client approval of security model and architecture prior to implementation. | Document |
| D3 | **Unified Ontology** | Consolidated Ontology objects backing both Guardian and Insight; merged duplicates; migrated Guardian-unique objects. | Data accuracy validation in Beacon workspace against legacy Carbon during Parallel Run. | Foundry Ontology |
| D4 | **Security Configuration** | Implemented OSPs, RLS, and user groups/roles separating Internal (cross-account) vs. External (restricted) visibility. | Successful security test scenarios; internal/external access patterns validated. | Palantir Management + JIRA |
| D5 | **Consolidated Pipelines** | Migrated and optimized pipelines with Snowflake push-down; pipeline documentation. | Pipeline outputs validated against legacy; documentation accepted by Client. | Foundry Pipelines + Docs |
| D6 | **Guardian Application (Beacon Space)** | Recreated Workshop modules (Item Health, DC Item Health) with full feature parity. | Feature parity verification by Client SMEs; JIRA ticket acceptance. | Foundry Workshop |
| D7 | **Parallel Run Validation Report** | Data comparison results between legacy Guardian (Carbon) and new Beacon implementation. | Client sign-off confirming data accuracy and functional equivalence. | Document + JIRA |
| D8 | **Cutover Runbook** | Step-by-step cutover plan, communications, and rollback procedures. | Client approval prior to cutover execution. | Document |
| D9 | **Decommissioning Recommendation** | List of legacy artifacts for retirement with estimated cost savings. | Client review and acknowledgment prior to decommissioning execution. | Document / Memo |
| D10 | **Documentation Package** | Technical documentation, operations runbook, known issues log. | Documentation reviewed and accepted by Client technical team. | Repository / Confluence |
| D11 | **Knowledge Transfer** | Training sessions with Client technical team; handover complete. | Client acknowledgment of knowledge transfer completion. | Sessions + Materials |
| D12 | **Cost Savings Report** | Documented compute and storage cost reduction achieved. | Client acknowledgment of savings metrics. | Document |

### 5.2 Acceptance Process

Unless otherwise specified in an applicable JIRA ticket or change order, the following acceptance process applies:

i. Provider will submit deliverables or sprint increments via the repositories and/or JIRA tickets designated by Client;

ii. Client will review within five (5) business days and either accept or provide written notice of material non-conformance;

iii. if Client does not respond within the review period, the deliverable will be deemed accepted; and

iv. Provider will remediate validated non-conformance and resubmit for acceptance.

---

## 6. Project Timeline and Approach

The parties intend to commence discovery activities in Week 1 (the week of January 26, 2026), in parallel with contracting and onboarding, recognizing that schedule and throughput are dependent on timely access provisioning and Client prioritization decisions. The timeline below is an operational plan and is not a guaranteed schedule unless expressly stated as a contractual milestone in a mutually executed change order.

**Target Completion:** June 30, 2026

### 6.1 Project Phases Summary

| Phase | Activities | Duration | Key Deliverables |
| :--- | :--- | :--- | :--- |
| **Phase 1: Discovery & Assessment** | Access/onboarding, object cataloging, pipeline inventory, function audit, security assessment, UI audit, risk assessment, migration planning | Weeks 1-3 | D1: Migration Assessment & Technical Plan |
| **Phase 2: Architecture & Design** | Security model design, ontology merge strategy, pipeline architecture, detailed workplan | Weeks 4-5 | D2: Architecture Design Document |
| **Phase 3: Backend Implementation** | Security implementation, ontology migration, function consolidation, pipeline refactoring, data validation | Weeks 6-14 | D3: Unified Ontology, D4: Security Configuration, D5: Consolidated Pipelines |
| **Phase 4: Frontend Implementation** | Workshop recreation, feature parity, internal testing | Weeks 15-17 | D6: Guardian Application (Beacon Space) |
| **Phase 5: Validation & UAT** | Parallel run, UAT, security validation, performance validation, defect remediation | Weeks 18-20 | D7: Parallel Run Validation Report |
| **Phase 6: Cutover & Transition** | Cutover planning, user transition, hypercare, decommissioning preparation | Week 21 | D8: Cutover Runbook; D9: Decommissioning Recommendation |
| **Phase 7: Handover & Closure** | Documentation finalization, knowledge transfer, cost savings validation, project closure | Week 22 | D10, D11, D12: Docs, KT, Cost Report |

### 6.2 Key Milestones

| Milestone | Target | Gate Criteria |
| :--- | :--- | :--- |
| **M1: Discovery Complete** | End of Week 3 | Migration Assessment approved by Client |
| **M2: Architecture Approved** | End of Week 5 | Architecture Design approved; green light to implement |
| **M3: Backend Complete** | End of Week 14 | Unified ontology validated; pipelines operational |
| **M4: UI Complete** | End of Week 17 | Guardian Workshop feature-complete in Beacon |
| **M5: UAT Complete** | End of Week 20 | Parallel Run validated; Client sign-off on UAT |
| **M6: Go-Live** | Week 21 | Users transitioned; hypercare active |
| **M7: Project Closure** | Week 22 | All deliverables accepted; handover complete |

*Note: Cutover activities may be scheduled during low-usage periods (e.g., weekends) to minimize business disruption, subject to Client coordination.*

---

## 7. Roles and Responsibilities

### 7.1 Client Responsibilities

- Provide timely access to Foundry workspaces (Carbon & Beacon), Snowflake EDW, Omni/SQL Server connections, datasets, pipelines, and source repositories necessary for discovery and migration.
- Provide access to JIRA and any applicable design artifacts, workflow diagrams, and existing documentation as available.
- Designate a Product Owner and technical Subject Matter Experts (SMEs) empowered to provide timely requirements clarification, prioritization, UI/UX direction decisions, and acceptance decisions.
- Participate in milestone reviews and provide timely approvals at gate checkpoints.
- Manage communication and training for internal business users (~200 Guardian users) regarding the platform transition.
- Provide security and change control procedures and facilitate approvals required for permission model changes and production-impacting deployments.
- Coordinate internal scheduling for cutover activities to minimize business disruption.
- Execute decommissioning of legacy artifacts following Provider recommendation and Client approval.

### 7.2 Provider Responsibilities

- Perform the Services using personnel with appropriate technical expertise in Palantir Foundry, Ontology design, and data engineering.
- Follow the methodology outlined in Section 3, executing activities systematically with appropriate documentation.
- Operate within Client's delivery processes unless otherwise agreed in writing, and maintain work tracking through JIRA tickets and repository artifacts.
- Adhere to Client security, access, and compliance requirements provided in writing.
- Communicate material risks, blockers, or dependencies in a timely manner, including where schedule or scope changes may be required to achieve Target Completion Date.
- Provide regular status updates and progress reporting at agreed cadence.
- Facilitate milestone reviews and gate checkpoint discussions.

---

## 8. Staffing and Resourcing

Provider anticipates assigning a core team of approximately two to three (2-3) dedicated resources, scaling as needed during peak implementation periods, subject to Client priorities and approval. Provider may adjust staffing composition while maintaining continuity and capability.

| Role | Estimated Allocation | Primary Activities |
| :--- | :--- | :--- |
| **Engagement Lead / Solution Architect** | 0.5–1.0 FTE | Discovery, architecture design, security model, delivery governance, Client liaison, milestone reviews |
| **Senior Foundry Engineer(s)** | 2.0–3.0 FTE | Ontology migration, pipeline refactoring, function consolidation, Workshop implementation, UAT support |
| **DevOps / Migration Support (as needed)** | 0.0–0.5 FTE | Parallel run execution, cutover coordination, decommissioning support, documentation |

---

## 9. Fees and Payment

Fees for the Services shall be charged on a [time-and-materials / fixed-fee] basis as specified below. Invoicing and payment terms shall follow the MSA. All fees are exclusive of applicable taxes.

**⚠️ FOR REVIEW:** Awaiting fee schedule confirmation from Chris/Johan before submission.

[Insert Fee Schedule or Rate Card Here]

---

## 10. Change Management

Any changes to scope, deliverables, timelines, or fees shall be managed through a written change order executed by both parties in accordance with the MSA. Provider is not obligated to perform out-of-scope work absent an executed change order. Material changes (defined as impacts exceeding 10% of estimated effort or two (2) weeks of schedule) shall require formal change order approval prior to implementation.

---

## 11. Assumptions and Dependencies

Services are performed under the governance of the MSA. Client procurement and legal onboarding may proceed in parallel with early discovery activities, subject to applicable policies. Provider's delivery sequencing and Target Completion Date are dependent on:

- Timely access provisioning to all required systems (Foundry workspaces, Snowflake, repositories) within one (1) week of SOW execution.
- Availability of Client stakeholders (Product Owner, SMEs) for clarification, milestone reviews, approvals, and UAT within agreed review periods.
- No material redesign of existing ontology architecture being required beyond targeted amendments for consolidation.
- Assumption that Guardian and Beacon ontologies have substantial overlap (~90% as indicated in preliminary discussions), minimizing reconciliation complexity.
- Client Product Team providing UI/UX direction decision (standalone Workshop vs. Insight-integrated) prior to Phase 4 commencement.
- Client prioritization decisions provided in a timely manner to support sprint planning and milestone gates.
- Client availability for cutover window scheduling during low-usage period.
- Required identity attribute for RLS (e.g., `account_id` or `health_system_id`) is available in Foundry/IdP and mapped to user profiles.

---

## 12. Term and Termination

This SOW shall commence on the Effective Date and continue until completion of the Services or termination in accordance with the MSA.

**Target Completion Date:** June 30, 2026

---

## 13. Signatures

IN WITNESS WHEREOF, the parties have executed this Statement of Work by their duly authorized representatives.

**Owens & Minor, Inc.**

Signature: __________________________  
Name: _____________________________  
Title: ______________________________  
Date: ______________________________

**I4C Information Technology Consulting Inc.**

Signature: __________________________  
Name: Chris Atack  
Title: Chief Operating Officer  
Date: ______________________________
