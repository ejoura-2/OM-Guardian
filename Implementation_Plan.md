# Guardian to Beacon Migration - Implementation Plan

**Project:** Guardian Application Consolidation into Beacon/Insight  
**Client:** Owens & Minor  
**Platform:** Palantir Foundry  
**Prepared By:** i4C - Foundry Architecture Team  
**Date:** January 20, 2026  
**Target Completion:** June 30, 2026

---

## Executive Summary

This Implementation Plan provides a structured, logical approach to consolidating the Guardian application (Carbon workspace) into the Beacon/Insight workspace. As a Palantir Foundry Expert Architect, I have analyzed the requirements and designed a methodology that addresses both the technical complexity and the business continuity requirements for ~200 internal users.

The plan follows Foundry best practices for ontology consolidation, security model unification, and compute optimization, while ensuring zero downtime through a parallel run validation approach.

---

## 1. Project Analysis & Architecture Assessment

### 1.1 Current State Analysis

Based on the requirements specification, the current architecture presents the following challenges:

| Aspect | Current State | Problem |
|--------|--------------|---------|
| **Workspaces** | Two parallel workspaces (Carbon/Guardian + Beacon/Insight) | Operational overhead, duplicated management |
| **Ontology Objects** | Duplicate objects (Item, Customer, Product Health, Substitution) | Storage redundancy, data inconsistency risk |
| **Pipelines** | Separate pipeline sets for each workspace | Duplicated compute costs, maintenance burden |
| **Functions/Logic** | Copy-pasted code between repositories | Technical debt, divergence risk |
| **Security** | Separate permission models per workspace | Inconsistent access control |
| **User Base** | ~200 internal (Guardian) + external customers (Insight) | Different access requirements |

### 1.2 Target State Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    UNIFIED BEACON WORKSPACE                              │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │                    UNIFIED ONTOLOGY                              │   │
│  │  ┌─────────┐ ┌──────────┐ ┌────────────────┐ ┌──────────────┐  │   │
│  │  │  Item   │ │ Customer │ │ Product Health │ │ Substitution │  │   │
│  │  └─────────┘ └──────────┘ └────────────────┘ └──────────────┘  │   │
│  │                    (Single Source of Truth)                      │   │
│  └─────────────────────────────────────────────────────────────────┘   │
│                                                                          │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │                    SECURITY LAYER (OSPs + RLS)                   │   │
│  │  ┌─────────────────────────┐  ┌─────────────────────────────┐  │   │
│  │  │ INTERNAL USERS (Guardian)│  │ EXTERNAL USERS (Insight)   │  │   │
│  │  │ • Cross-account access   │  │ • Single-account isolation │  │   │
│  │  │ • Customer Advocate role │  │ • OSP-enforced filtering   │  │   │
│  │  │ • Full object visibility │  │ • Row-level security       │  │   │
│  │  └─────────────────────────┘  └─────────────────────────────┘  │   │
│  └─────────────────────────────────────────────────────────────────┘   │
│                                                                          │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │                    APPLICATIONS                                   │   │
│  │  ┌─────────────────────────┐  ┌─────────────────────────────┐  │   │
│  │  │ GUARDIAN WORKSHOPS      │  │ INSIGHT APPLICATION         │  │   │
│  │  │ • Item Health           │  │ • Customer Portal           │  │   │
│  │  │ • DC Item Health        │  │ • Analytics Dashboards      │  │   │
│  │  └─────────────────────────┘  └─────────────────────────────┘  │   │
│  └─────────────────────────────────────────────────────────────────┘   │
│                                                                          │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │                    DATA LAYER                                     │   │
│  │  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐ │   │
│  │  │ Snowflake (EDW) │  │ Omni (SQL Srvr) │  │ Foundry Storage │ │   │
│  │  │ • Push-down ETL │  │ • Operational   │  │ • Orchestration │ │   │
│  │  │ • Zero-ETL conn │  │ • Near-realtime │  │ • Derived only  │ │   │
│  │  └─────────────────┘  └─────────────────┘  └─────────────────┘ │   │
│  └─────────────────────────────────────────────────────────────────┘   │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

### 1.3 Requirements Traceability Matrix

| Req ID | Requirement | Implementation Approach | Phase |
|--------|-------------|------------------------|-------|
| REQ-F-01 | Support ~200 internal users | Migrate user groups; parallel run validation | 5-6 |
| REQ-F-02 | Feature parity (Item Health, Notes, Status) | Workshop recreation with identical functionality | 4 |
| REQ-F-03 | Navigation flexibility | Configure as standalone Workshop initially; Product Team decides integration | 4 |
| REQ-F-04 | Dual security models | OSP + RLS design with account-based filtering | 2, 3 |
| REQ-F-05 | User group migration | Migrate "Customer Advocate Users" and others | 3 |
| REQ-T-01 | Single Source of Truth | Merge duplicate objects into unified ontology | 3 |
| REQ-T-02 | Deduplication | Object-by-object merge strategy | 1, 3 |
| REQ-T-03 | Function consolidation | Refactor into single shared repository | 3 |
| REQ-T-04 | Compute optimization | Snowflake push-down, eliminate duplicate pipelines | 3 |
| REQ-T-05 | ETL strategy (Snowflake First) | Reconfigure ingestion to use Snowflake directly | 3 |
| REQ-T-06 | Data freshness (operational) | Maintain Omni/SQL Server CDC frequency | 3 |
| REQ-T-07 | Parallel run | Configure side-by-side validation environment | 5 |
| REQ-T-08 | Decommissioning | Prepare retirement list; execute post-validation | 6 |

---

## 2. Phase 1: Discovery & Baseline Assessment

**Duration:** Weeks 1-2  
**Objective:** Establish complete understanding of current state before any changes

### 2.1 Workstream 1A: Ontology Deep Dive

**Goal:** Create complete inventory and comparison of all ontology objects

| Task | Description | Output | Owner |
|------|-------------|--------|-------|
| 1A.1 | **Guardian Object Inventory** - Export complete list of Object Types from Carbon workspace | Object Type List + Property schemas | Foundry Engineer |
| 1A.2 | **Beacon Object Inventory** - Export complete list of Object Types from Beacon workspace | Object Type List + Property schemas | Foundry Engineer |
| 1A.3 | **Object Comparison Matrix** - Compare each object side-by-side: name, properties, links, actions | Comparison spreadsheet with match % | Solution Architect |
| 1A.4 | **Duplicate Identification** - Flag objects that exist in both (Item, Customer, Product Health, Substitution) | Duplicate Report | Solution Architect |
| 1A.5 | **Link/Relationship Mapping** - Document all object relationships and link types | Relationship Diagram | Foundry Engineer |
| 1A.6 | **Action Inventory** - List all Actions with input/output definitions | Action Catalog | Foundry Engineer |

**Foundry-Specific Approach:**
```
For each Object Type in Carbon workspace:
  1. Navigate to Ontology Manager → Object Types
  2. Export: TypeRid, Display Name, Properties (with types), Links, Actions
  3. Document backing dataset(s)
  4. Note any derived properties or Typeahead configurations

For comparison:
  1. Match by semantic meaning (not just name)
  2. Identify property-level differences (type mismatches, naming conventions)
  3. Flag any objects with different backing datasets
```

### 2.2 Workstream 1B: Pipeline & Compute Analysis

**Goal:** Understand all data flows, transformation logic, and compute costs

| Task | Description | Output | Owner |
|------|-------------|--------|-------|
| 1B.1 | **Pipeline Inventory (Guardian)** - List all builds, syncbacks, transforms in Carbon | Pipeline Catalog | Foundry Engineer |
| 1B.2 | **Pipeline Inventory (Beacon)** - List all builds in Beacon workspace | Pipeline Catalog | Foundry Engineer |
| 1B.3 | **Data Source Mapping** - Document all source connections (Snowflake, Omni, SQL Server) | Source Connection Map | Foundry Engineer |
| 1B.4 | **Compute Cost Analysis** - Pull Resource Manager data for both workspaces | Cost Report ($/month) | Solution Architect |
| 1B.5 | **Pipeline Dependency Graph** - Map input → transform → output for all pipelines | Dependency Diagram | Foundry Engineer |
| 1B.6 | **Schedule Analysis** - Document all pipeline schedules and SLAs | Schedule Matrix | Foundry Engineer |

**Foundry-Specific Approach:**
```
Resource Manager Analysis:
  1. Navigate to Resource Manager → Usage Dashboard
  2. Filter by Project: "Owens & Minor (Guardian)" and "Beacon"
  3. Export: Monthly compute hours, storage GB, pipeline run counts
  4. Identify top 10 cost drivers (specific pipelines)

Pipeline Documentation:
  1. For each pipeline, document:
     - Trigger type (scheduled, manual, event-driven)
     - Input datasets (with source system)
     - Transformation type (Python, SQL, Pipeline Builder, Contour)
     - Output datasets
     - Estimated compute time
```

### 2.3 Workstream 1C: Function & Code Audit

**Goal:** Catalog all TypeScript functions and Python transforms for consolidation

| Task | Description | Output | Owner |
|------|-------------|--------|-------|
| 1C.1 | **Repository Inventory** - List all repositories in scope (guardian-transforms, type-fit-functions) | Repo List | Foundry Engineer |
| 1C.2 | **Function Catalog** - Document all Functions with signatures and purposes | Function Catalog | Senior Engineer |
| 1C.3 | **Code Duplication Analysis** - Identify copy-pasted code between Guardian and Beacon repos | Duplication Report | Senior Engineer |
| 1C.4 | **Dependency Mapping** - Document which objects/pipelines call which functions | Call Graph | Senior Engineer |

**Foundry-Specific Approach:**
```
For Functions (TypeScript):
  1. Navigate to Code Repositories → guardian-type-fit-functions
  2. Review src/index.ts for exported functions
  3. Document: function name, input types, output types, purpose
  4. Check Ontology → Actions to see which actions call these functions

For Transforms (Python):
  1. Navigate to Code Repositories → guardian-transforms
  2. Map: input datasets → transform logic → output datasets
  3. Identify any logic that could be pushed to Snowflake SQL
```

### 2.4 Workstream 1D: Security Model Assessment

**Goal:** Understand current permissions and design dual-access security model

| Task | Description | Output | Owner |
|------|-------------|--------|-------|
| 1D.1 | **Guardian User Audit** - List all users/groups with Guardian access | User/Group List | Solution Architect |
| 1D.2 | **Beacon User Audit** - List all users/groups with Beacon access | User/Group List | Solution Architect |
| 1D.3 | **Permission Pattern Analysis** - Identify how access is currently granted (direct, group, OSP) | Permission Analysis | Solution Architect |
| 1D.4 | **Gap Analysis** - Identify security gaps or risky patterns | Risk Report | Solution Architect |
| 1D.5 | **Dual-Model Design** - Design OSP + RLS structure for unified workspace | Security Design Doc | Solution Architect |

**Foundry-Specific Security Design:**
```
Dual-Model Security Architecture:

1. Object Security Policies (OSPs):
   - Create "Internal Guardian User" policy:
     → Grant: All objects with read/write
     → Filter: None (cross-account visibility)
   
   - Create "External Customer" policy:
     → Grant: Filtered objects only
     → Filter: WHERE account_id = @user.account_id

2. Row-Level Security (RLS):
   - Apply to: Item, Customer, Product Health
   - Column: account_id or health_system_id
   - Logic: External users see only matching rows

3. User Groups:
   - Migrate: "Customer Advocate Users" → Internal role
   - Create: "External Customer - {HealthSystem}" groups
```

### 2.5 Workstream 1E: UI/Workshop Audit

**Goal:** Document all Guardian UI features for parity validation

| Task | Description | Output | Owner |
|------|-------------|--------|-------|
| 1E.1 | **Workshop Module Inventory** - List Guardian Item Health and DC Item Health modules | Module List | Foundry Engineer |
| 1E.2 | **Feature Catalog** - Document every widget, filter, action, navigation element | Feature Checklist | Foundry Engineer |
| 1E.3 | **User Workflow Documentation** - Document how users interact with each feature | Workflow Diagrams | Solution Architect |
| 1E.4 | **Data Binding Analysis** - Document which objects/properties power each widget | Data Binding Map | Foundry Engineer |

**Feature Parity Checklist (from REQ-F-02):**
```
□ Item Health App
  □ Filtering by Customer/Account Groups
  □ Column Configuration
  □ Action Items list
  □ Critical Items list

□ DC Level App
  □ DC Item Health view
  □ DC Level Statistics

□ Note Taking
  □ Add notes on Items/Products
  □ Edit notes
  □ View notes
  □ Near-real-time sync back to source

□ Status Management
  □ Modify item status (e.g., "In Transit")
  □ Clear item status
```

### 2.6 Phase 1 Deliverable: Migration Assessment & Technical Plan

At the end of Phase 1, deliver a comprehensive document containing:
1. Object Comparison Matrix (with merge recommendations)
2. Pipeline Inventory & Cost Analysis
3. Function Duplication Report
4. Security Model Design
5. Feature Parity Checklist
6. Risk Register
7. Detailed Migration Sequence Plan

**Milestone M1: Discovery Complete**
- Gate: Client approval of migration approach
- Decision: Proceed to Architecture & Design

---

## 3. Phase 2: Architecture & Design

**Duration:** Week 3  
**Objective:** Finalize target-state design with Client approval before implementation

### 3.1 Ontology Merge Strategy

For each duplicate object, define the merge approach:

| Object | Guardian Version | Beacon Version | Merge Strategy |
|--------|-----------------|----------------|----------------|
| **Item** | `Item` in Carbon | `Item` in Beacon | Beacon is SSOT; Add Guardian-specific properties |
| **Customer** | `Customer` in Carbon | `Beacon Customer` in Beacon | Merge into unified `Customer`; Migrate links |
| **Product Health** | `Product Health` in Carbon | `Beacon Enriched Product Health` | Merge; Reconcile property differences |
| **Substitution** | `Substitution` in Carbon | `Substitution` in Beacon | Deduplicate; Single backing dataset |

**Merge Decision Framework:**
```
For each duplicate object pair:
  1. Compare property sets → Identify superset
  2. Compare backing datasets → Decide which to retain
  3. Compare Action definitions → Consolidate
  4. Map links → Ensure relationship continuity
  5. Document migration steps
```

### 3.2 Security Model Finalization

**Object Security Policy Structure:**

```yaml
InternalGuardianPolicy:
  name: "Guardian Internal Users"
  applies_to:
    - "Customer Advocate Users" group
    - "Sales Team" group
  object_grants:
    Item: READ, WRITE
    Customer: READ
    ProductHealth: READ, WRITE
    Substitution: READ
  row_filter: NONE  # Cross-account visibility

ExternalCustomerPolicy:
  name: "Insight External Customers"
  applies_to:
    - "External Customer - {HealthSystem}" groups
  object_grants:
    Item: READ
    Customer: READ
    ProductHealth: READ
  row_filter:
    function: FilterByHealthSystem
    column: health_system_id
    operator: EQUALS
    value: @user.health_system_id
```

### 3.3 Pipeline Architecture

**Optimized Pipeline Design:**

```
Data Flow Architecture:

┌──────────────┐     ┌────────────────────────────────────────────┐
│  SNOWFLAKE   │────►│           FOUNDRY INTEGRATION               │
│  (EDW)       │     │  ┌────────────────────────────────────┐   │
│              │     │  │ Snowflake External Connection      │   │
│ • Sales Hist │     │  │ → Zero-ETL where possible          │   │
│ • Prod Hist  │     │  │ → SQL push-down for transforms     │   │
│              │     │  └────────────────────────────────────┘   │
└──────────────┘     │                    │                        │
                     │                    ▼                        │
┌──────────────┐     │  ┌────────────────────────────────────┐   │
│    OMNI      │────►│  │ Foundry Pipeline (Orchestration)   │   │
│ (SQL Server) │     │  │ → Lightweight transforms only      │   │
│              │     │  │ → Scheduling & monitoring          │   │
│ • Logistics  │     │  │ → Audit logging                    │   │
│ • Warehouse  │     │  └────────────────────────────────────┘   │
│ • Status     │     │                    │                        │
└──────────────┘     │                    ▼                        │
                     │  ┌────────────────────────────────────┐   │
                     │  │        UNIFIED ONTOLOGY             │   │
                     │  │  (Item, Customer, Product Health)   │   │
                     │  └────────────────────────────────────┘   │
                     └────────────────────────────────────────────┘
```

**Pipeline Optimization Actions:**
| Current State | Optimization | Expected Savings |
|--------------|--------------|------------------|
| Foundry Spark joins on 10M+ rows | Push down to Snowflake SQL | ~60% compute reduction |
| Duplicate ETL in Guardian + Beacon | Single pipeline feeding both | ~50% reduction |
| Hourly full refresh | Incremental CDC where applicable | ~30% reduction |

### 3.4 Repository Consolidation Plan

```
Repository Strategy:

BEFORE (Current):
├── guardian-transforms/
│   ├── src/
│   │   ├── transforms/
│   │   │   ├── item_health_transform.py
│   │   │   └── product_health_transform.py
├── guardian-type-fit-functions/
│   ├── src/
│   │   ├── index.ts
│   │   ├── gargantuan_function.ts
│   │   └── type-fit-functions.ts
├── beacon-transforms/
│   ├── src/
│   │   ├── transforms/
│   │   │   ├── item_health_transform.py  (DUPLICATE)
│   │   │   └── product_health_transform.py (DUPLICATE)

AFTER (Consolidated):
├── unified-ontology-transforms/
│   ├── src/
│   │   ├── transforms/
│   │   │   ├── item_health_transform.py  (SINGLE)
│   │   │   └── product_health_transform.py (SINGLE)
├── unified-type-functions/
│   ├── src/
│   │   ├── index.ts
│   │   ├── gargantuan_function.ts
│   │   └── type-fit-functions.ts
│   │   └── shared_utilities.ts (NEW - extracted common logic)
```

### 3.5 Phase 2 Deliverable: Architecture Design Document

1. Ontology Merge Strategy (per-object decisions)
2. Security Model Design (OSPs, RLS rules, user groups)
3. Pipeline Architecture Diagram
4. Repository Consolidation Plan
5. Detailed Implementation Workplan (task-level)
6. Risk Mitigation Plan

**Milestone M2: Architecture Approved**
- Gate: Client sign-off on architecture
- Decision: Green light to begin implementation

---

## 4. Phase 3: Backend Implementation

**Duration:** Weeks 4-6  
**Objective:** Execute ontology consolidation, pipeline migration, and security configuration

### 4.1 Week 4: Security & Foundation

| Day | Task | Details |
|-----|------|---------|
| Mon-Tue | Implement OSPs in Beacon workspace | Configure "Guardian Internal Users" and "External Customer" policies |
| Wed | Implement RLS on key objects | Apply row-level filters to Item, Customer, Product Health |
| Thu | Migrate user groups | Create/migrate "Customer Advocate Users" to Beacon |
| Fri | Security validation | Test internal cross-account access; Test external isolation |

**Foundry Implementation Steps:**
```
OSP Creation:
1. Navigate to Ontology → Security → Object Security Policies
2. Create policy: "Guardian Internal Users"
3. Configure grants for each Object Type
4. Associate with "Customer Advocate Users" group

RLS Implementation:
1. Navigate to Ontology → Object Type → Security
2. Configure Row-Level Security
3. Create filter function:
   FUNCTION FilterByHealthSystem(user_health_system_id):
     RETURN WHERE health_system_id = user_health_system_id
4. Apply to external user groups
```

### 4.2 Week 5: Ontology Migration

| Day | Task | Details |
|-----|------|---------|
| Mon | Migrate/merge Item object | Add Guardian properties to Beacon Item; Update backing dataset |
| Tue | Migrate/merge Customer object | Consolidate Beacon Customer + Guardian Customer |
| Wed | Migrate/merge Product Health | Reconcile property differences; Single backing dataset |
| Thu | Migrate remaining objects | Substitution, other Guardian-unique objects |
| Fri | Refactor links and relationships | Update all object links to point to unified objects |

**Foundry Implementation Steps:**
```
Object Merge Process:
1. Identify target object (typically Beacon version)
2. Add Guardian-specific properties:
   - Navigate to Object Type → Properties → Add Property
   - Match data types from Guardian schema
3. Update backing dataset:
   - Either: Reconfigure to unified dataset
   - Or: Create combined view/transform
4. Update Actions:
   - Review each Action definition
   - Ensure references point to correct properties
5. Test object:
   - Verify data appears correctly
   - Verify Actions execute properly
```

### 4.3 Week 6: Pipeline Migration & Optimization

| Day | Task | Details |
|-----|------|---------|
| Mon | Configure Snowflake direct connections | Set up Zero-ETL or External Integration |
| Tue | Implement push-down transforms | Convert Spark transforms to Snowflake SQL |
| Wed | Migrate remaining pipelines | Reconfigure to single pipeline set |
| Thu | Decommission duplicate pipelines | Disable Guardian-specific duplicates |
| Fri | Data validation | Compare outputs: Guardian vs. Beacon for accuracy |

**Foundry Implementation Steps:**
```
Snowflake Integration:
1. Navigate to Data Connection → Snowflake
2. Configure External Integration (or use existing)
3. Create virtual dataset pointing to Snowflake table
4. Configure sync schedule

Push-down Optimization:
1. Identify heavy Spark transforms
2. Rewrite as Snowflake SQL in External Transform
3. Update pipeline to use Snowflake-based dataset
4. Compare output accuracy
5. Measure compute savings
```

**Milestone M3: Backend Complete**
- Gate: Unified ontology operational; Pipelines producing valid data
- Validation: Data accuracy verified against legacy Guardian

---

## 5. Phase 4: Frontend Implementation

**Duration:** Weeks 7-8  
**Objective:** Recreate Guardian Workshop modules with full feature parity

### 5.1 Week 7: Workshop Module Recreation

| Task | Details |
|------|---------|
| Create Guardian Item Health Workshop | New Workshop module in Beacon workspace |
| Recreate filtering mechanism | Customer/Account Group filters using unified ontology |
| Recreate column configuration | User-configurable columns with same defaults |
| Recreate Action Items list | Object list widget with appropriate filtering |
| Recreate Critical Items list | Object list widget with criticality filter |

**Foundry Workshop Implementation:**
```
Workshop Configuration:
1. Navigate to Ontology → Applications → Create Workshop
2. Name: "Guardian Item Health" (or per Product Team direction)
3. Configure modules:

Module 1: Item Health Overview
├── Object List Widget
│   ├── Object Type: Item
│   ├── Columns: [Match Guardian exactly]
│   ├── Filters:
│   │   ├── Customer/Account Group (dropdown)
│   │   ├── Status filter
│   │   └── Date range
│   └── Actions: [Enable Status Management actions]
├── Filter Panel Widget
│   ├── Customer selector
│   ├── Account Group selector
│   └── Column configuration toggle
```

### 5.2 Week 8: Feature Parity & Testing

| Task | Details |
|------|---------|
| Create DC Item Health Workshop | DC-level statistics and health view |
| Implement Note Taking | Action for add/edit/view notes with syncback |
| Implement Status Management | Actions for modifying/clearing item status |
| Internal testing | Full feature walkthrough by Provider team |
| Bug remediation | Fix identified issues |

**Note Taking Implementation (REQ-F-02):**
```
Notes Syncback Architecture:
1. Create "Add Note" Action on Item object
2. Action writes to Foundry dataset (notes table)
3. Configure Syncback pipeline:
   - Trigger: On Action execution
   - Target: Omni SQL Server (operational source)
   - Frequency: Near-realtime (per REQ-T-06)
4. Test latency to ensure acceptable refresh
```

**Milestone M4: UI Complete**
- Gate: All Workshop features implemented and internally tested
- Validation: Feature parity checklist 100% complete

---

## 6. Phase 5: Validation & UAT

**Duration:** Weeks 8-9  
**Objective:** Parallel run validation and user acceptance testing

### 6.1 Parallel Run Execution (REQ-T-07)

**Configuration:**
```
Parallel Run Setup:
1. Both systems operational:
   - Guardian (Carbon) - Production for existing users
   - Guardian (Beacon) - Validation instance
2. Data sources feeding both:
   - Omni → Guardian (Carbon) + Guardian (Beacon)
   - Snowflake → Guardian (Carbon) + Guardian (Beacon)
3. Comparison methodology:
   - Daily snapshot of key objects
   - Automated comparison scripts
   - Variance reporting
```

**Validation Checkpoints:**
| Object | Metric | Tolerance |
|--------|--------|-----------|
| Item | Row count, Property values | 100% match |
| Customer | Row count, Account mappings | 100% match |
| Product Health | Health scores, Calculations | 99.9% match (allow rounding) |
| Notes | Note content, Timestamps | 100% match |

### 6.2 User Acceptance Testing

**UAT Test Cases:**
| ID | Feature | Test Steps | Expected Result |
|----|---------|-----------|-----------------|
| UAT-01 | Login & Navigation | Log in as Customer Advocate; Navigate to Item Health | Dashboard loads; All widgets visible |
| UAT-02 | Cross-Account Access | Filter to Duke; Filter to WVU | Both health systems data visible |
| UAT-03 | Filtering | Apply Customer filter; Apply Account Group filter | Results filtered correctly |
| UAT-04 | Column Config | Hide/show columns; Reorder columns | Changes persist |
| UAT-05 | Action Items | View Action Items list | List matches Guardian legacy |
| UAT-06 | Critical Items | View Critical Items list | List matches Guardian legacy |
| UAT-07 | DC Health | Navigate to DC Item Health; View statistics | Data matches legacy |
| UAT-08 | Add Note | Add note to Item | Note appears; Syncs to Omni |
| UAT-09 | Edit Note | Edit existing note | Changes saved; Syncs to Omni |
| UAT-10 | Status Change | Modify item status to "In Transit" | Status updated; Visible in list |
| UAT-11 | Status Clear | Clear item status | Status cleared |
| UAT-12 | External Isolation | Log in as external customer; Attempt cross-account | Only own data visible; Others blocked |

**Milestone M5: UAT Complete**
- Gate: All UAT test cases passed; Client sign-off
- Validation: Parallel run shows accurate data

---

## 7. Phase 6: Cutover & Transition

**Duration:** Week 10  
**Objective:** Transition ~200 users to new environment

### 7.1 Cutover Planning

**Recommended Approach: Weekend Cutover**
```
Cutover Timeline (Example):
Friday 5pm:
  - Send user notification: "System unavailable Saturday 6am-12pm"
  - Final Guardian (Carbon) backup

Saturday 6am:
  - Disable Guardian (Carbon) user access
  - Final data sync verification
  - Enable Guardian (Beacon) for internal access

Saturday 9am:
  - Core team validation (Provider + Client SMEs)
  - Smoke test all features

Saturday 12pm:
  - Send user notification: "System available at new URL"
  - Begin hypercare monitoring

Monday 9am:
  - Monitor user issues
  - Rapid response team active
```

### 7.2 Rollback Plan

```
Rollback Criteria:
  - Data accuracy issue affecting >10% of records
  - Critical feature unavailable
  - Security breach detected

Rollback Steps:
  1. Disable Guardian (Beacon) access
  2. Re-enable Guardian (Carbon)
  3. Notify users of rollback
  4. Investigate and remediate
  5. Re-schedule cutover
```

### 7.3 Hypercare Support

**Duration:** [__] business days post-cutover
**Coverage:** Standard business hours
**Response SLAs:**
- Critical (system down): 1 hour
- High (feature unavailable): 4 hours
- Medium (usability issue): 8 hours
- Low (enhancement request): Backlog

**Milestone M6: Go-Live**
- Gate: Users successfully transitioned
- Status: Hypercare period active

---

## 8. Phase 7: Decommissioning & Closure

**Duration:** Week 10+  
**Objective:** Realize cost savings; Complete handover

### 8.1 Decommissioning Recommendation (REQ-T-08)

**Artifacts to Retire:**
| Category | Artifact | Action | Estimated Savings |
|----------|----------|--------|-------------------|
| Pipelines | Guardian ETL pipelines (duplicates) | Disable & archive | ~$X/month compute |
| Pipelines | Duplicate Snowflake syncs | Disable | ~$Y/month |
| Storage | Guardian datasets (Carbon) | Archive & delete | ~Z GB storage |
| Objects | Duplicate Object Types | Remove from Carbon | Cleaner ontology |
| Repositories | guardian-transforms (if consolidated) | Archive | Maintenance reduction |

**Decommissioning Process:**
```
1. Provider prepares Decommissioning Recommendation document
2. Client reviews and approves retirement list
3. Provider disables (not deletes) identified artifacts
4. 30-day cooling period with monitoring
5. Client executes permanent deletion (or Provider with explicit approval)
```

### 8.2 Cost Savings Validation (ASM-02)

**Measurement:**
| Metric | Before (Monthly) | After (Monthly) | Savings |
|--------|-----------------|-----------------|---------|
| Foundry Compute (Carbon) | $X | $0 | -$X |
| Foundry Compute (Beacon) | $Y | $Y' | Δ |
| Foundry Storage (Carbon) | $A | $0 | -$A |
| Foundry Storage (Beacon) | $B | $B' | Δ |
| **Total** | **$TOTAL_BEFORE** | **$TOTAL_AFTER** | **-$SAVINGS** |

### 8.3 Knowledge Transfer & Handover

**Sessions:**
1. Architecture Overview (1 hour)
   - Unified ontology structure
   - Security model walkthrough
   - Pipeline architecture

2. Operations Runbook (2 hours)
   - Monitoring dashboards
   - Common troubleshooting
   - Escalation paths

3. Future Enhancements (1 hour)
   - Insight integration considerations
   - Performance optimization opportunities

**Milestone M7: Project Closure**
- Gate: All deliverables accepted; Knowledge transfer complete
- Outcome: Project formally closed

---

## 9. Risk Register

| ID | Risk | Probability | Impact | Mitigation |
|----|------|-------------|--------|------------|
| R1 | Ontology differences greater than expected | Medium | High | Phase 1 discovery will quantify; Add contingency time |
| R2 | Security model complexity | Medium | High | Design validation with Client infosec in Phase 2 |
| R3 | Data accuracy issues in parallel run | Low | Critical | Comprehensive validation scripts; Rollback plan ready |
| R4 | User adoption challenges | Medium | Medium | Clear communication; Feature parity ensures familiarity |
| R5 | Snowflake integration issues | Low | Medium | Early PoC in Phase 1; Fallback to existing patterns |
| R6 | Product Team UI decision delayed | Medium | Medium | Design for flexibility (standalone Workshop default) |
| R7 | Dependencies on Client access provisioning | High | High | Aggressive follow-up; SOW contingency language |

---

## 10. Success Criteria

| Criteria | Measure | Target |
|----------|---------|--------|
| **Functional Parity** | All UAT test cases passed | 100% |
| **Data Accuracy** | Parallel run variance | ≤0.1% |
| **User Transition** | Internal users on new system | 200/200 |
| **Security** | External isolation verified | 0 breaches |
| **Cost Reduction** | Monthly compute/storage savings | ≥30% |
| **Timeline** | Project completion | By June 30, 2026 |

---

## Appendix A: Detailed Task List

See Workplan spreadsheet for complete task breakdown with:
- Task ID
- Task Name
- Phase
- Predecessor
- Duration
- Assigned Role
- Status

---

## Appendix B: Glossary

| Term | Definition |
|------|------------|
| **OSP** | Object Security Policy - Foundry mechanism for granting object-level access |
| **RLS** | Row-Level Security - Filtering data at the row level based on user context |
| **SSOT** | Single Source of Truth - The authoritative data source for a given entity |
| **Zero-ETL** | Palantir capability to query external data without copying into Foundry |
| **Push-down** | Sending computation to the source system (e.g., Snowflake) rather than executing in Foundry |
| **Syncback** | Foundry mechanism to write data changes back to an operational source system |
| **Carbon** | Workspace name for the current Guardian environment |
| **Beacon** | Workspace name for the Insight/external customer environment |

---

*Document Version: 1.0*  
*Last Updated: January 20, 2026*  
*Next Review: Upon Phase 1 completion*
