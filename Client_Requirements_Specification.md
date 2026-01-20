# Client Requirements Specification
**Project:** Guardian to Beacon (Insight) Consolidation  
**Client:** Owens & Minor  
**Platform:** Palantir Foundry  
**Source Material:** Meeting Transcript (Jan 2026) & Meeting Notes  
**Date:** January 20, 2026

---

## 1. Project Overview & Business Goal
**Goal:** Consolidate the internal-facing "Guardian" application (currently in the Carbon workspace) into the external-facing "Beacon" (Insight) application workspace. The primary drivers are cost reduction (elimination of redundant storage/compute) and unified platform management.

**Target Date:** Project completion is mandated **before July 1, 2026**.

---

## 2. Functional Requirements

### 2.1. User Experience & Parity
*   **REQ-F-01 (Internal User Support):** The system MUST support the workflows of ~200 existing internal Guardian users (Customer Advocates, Sales).
*   **REQ-F-02 (Feature Parity):** Critical Guardian features MUST be available in the new environment, including:
    *   **Item Health App:** Filtering by Customer/Account Groups, Column Configuration, Action Items lists, Critical Items lists.
    *   **DC Level App:** Viewing DC Item Health, DC Level Statistics.
    *   **Note Taking:** Ability to add, edit, and view notes on Items/products. Notes often require near-real-time updates back to the operational source.
    *   **Status Management:** Modifying or clearing item statuses (e.g., "In Transit").
*   **REQ-F-03 (Navigation):** The Product Team MUST determine whether Guardian functionality appears as a tab within Insight, a separate login view, or a unified interface. (Technical team must support flexible implementation of this decision).

### 2.2. Security & Access Control
*   **REQ-F-04 (Dual Security Models):** The system MUST support two distinct security profiles within the same ontology/application space:
    *   **Internal Users (Guardian):** MUST have permission to view data across multiple accounts/health systems (e.g., working with Duke, WVU Medicine simultaneously).
    *   **External Users (Insight/Beacon):** MUST be restricted via strict Object Security Policies (OSP) or Row-Level Security to see *only* their specific Health System's data.
*   **REQ-F-05 (User Groups):** Maintain/migrate existing user groups (e.g., "Customer Advocate Users") to manage internal permissions.

---

## 3. Technical Requirements

### 3.1. Ontology & Data Structure
*   **REQ-T-01 (Single Source of Truth):** There MUST be a single set of shared Data Objects (e.g., `Item`, `Customer`, `Product Health`) serving both Internal and External applications.
*   **REQ-T-02 (Deduplication):** Duplicate objects currently existing in both "Owens & Minor" (Guardian) and "Beacon" spaces MUST be merged.
    *   *Current State:* Objects like `Substitution`, `Beacon Customer`, and `Beacon Enriched Product Health` are materialized copies or reference copies.
    *   *Target State:* A single object backing both views to eliminate storage redundancy.
*   **REQ-T-03 (Action/Function Migration):** Existing Logic (e.g., `gargantuan_function`, `type-fit-functions`) MUST be consolidated. Currently, code is copied/pasted between repositories; this MUST be refactored into a single shared repository or library.

### 3.2. Pipelines & Data Engineering
*   **REQ-T-04 (Compute Optimization):** The solution MUST aim to reduce Palantir Foundry compute costs.
*   **REQ-T-05 (ETL Strategy):**
    *   **Snowflake First:** Where data exists in Snowflake (Sales history, Product history), pipelines SHOULD ingest directly from Snowflake rather than re-processing raw files in Foundry, leveraging Palantir's "Zero-ETL" or direct connection capabilities where possible.
    *   **Push-down Processing:** Heavy transformations (Joins, Aggregations) SHOULD be offloaded to Snowflake or Databricks (if available) via "Compute Modules" or SQL pushdown, rather than running on Foundry Spark compute.
*   **REQ-T-06 (Data Freshness - Operational):** Certain operational data points (e.g., Logistical notes, Warehouse statuses) that require near real-time transparency MUST maintain their current refresh frequency (likely direct connection to Omni/SQL Server or frequent CDC).

### 3.3. Legacy Decommissioning
*   **REQ-T-07 (Parallel Run):** The new architecture MUST support a parallel run phase where Guardian (Legacy) and Insight (New Internal View) run simultaneously to validate data accuracy without downtime.
*   **REQ-T-08 (Retirement):** Upon validation, the Guardian workflows, duplicate pipelines, and duplicate storage in the Carbon workspace MUST be decommissioned to realize cost savings.

---

## 4. Constraints & Assumptions

*   **CON-01 (Platform):** Solution is limited to the existing single Palantir Foundry instance (Organization).
*   **CON-02 (Data Sources):** Primary sources are **Omni** (SQL Server, replicated overnight) and **Snowflake** (EDW).
*   **ASM-01 (Product Direction):** It is assumed the Client Product Owners (Mary) will provide the final decision on UI cohesion (Same look vs. Different look) prior to frontend implementation.
*   **ASM-02 (Cost Metric):** Success will be measured partially by the difference in Compute Cost ($/month) between the current dual-state and the future unified state.

---

## 5. Artifacts to be Migrated (Preliminary Audit)
*Based on the transcript walkthrough, the following specific artifacts require assessment:*
*   **Workshops:** `Guardian Item Health`, `DC Item Health`.
*   **Inputs:** `Omni` (SQL Server), `Snowflake`.
*   **Repositories:** `guardian-transforms`, `guardian-type-fit-functions`.
*   **Pipelines:** "Logic" folder in `Guardian Item Health` ontology.
