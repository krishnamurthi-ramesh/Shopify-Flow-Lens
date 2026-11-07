# Product Requirements Document (PRD): FlowLens Preflight Analyzer

**Product Name:** FlowLens  
**Release Target:** Concept Prototype v1.0 (Read-Only Preflight Analyzer)  
**Author:** R. Krishnamurthi (AI Product Manager)  
**Status:** Approved for Concept Prototype (Post-Audit Revision)  
**Last Updated:** November 2026  

---

## 1. Executive Summary & Product Vision

Shopify merchants rely on Shopify Flow to automate critical business operations. However, Shopify Engineering disclosed that fine-tuned workflow generation suffered a **35% lower activation rate** in production, driven by complex condition patterns (16%) and missing configuration parameters (25%). Furthermore, merchant discussions highlight that automations can fail silently due to subtle data formatting mismatches (e.g., `"USA"` vs ISO-2 `"US"`) or unhandled configuration edge cases.

**FlowLens** is a read-only, pre-activation workflow analyzer and linting inspector. It ingests exported Shopify Flow workflow definitions (`.flow` JSON schemas), statically analyzes the trigger-condition-action DAG, detects schema-aware configuration hazards and pre-Wait data dependencies, simulates user-defined or synthetic test scenarios, and presents recommended diffs for human review before automations touch live customer orders.

---

## 2. Goals & Non-Goals

### Core Goals:
1. **Preflight Visibility:** Enable operators to catch static configuration errors and pre-Wait data dependencies before activating a workflow.
2. **Schema-Aware Linting:** Use deterministic static analysis to detect known Shopify Flow constraints (ISO alpha-2 compliance, pre-Wait data action output loss, self-triggering update loops).
3. **Transparent Explanations:** Ground explanations in verified Shopify documentation and specific AST rule triggers, clearly indicating uncertainty when schemas are unverified.
4. **Interactive Scenario Simulation:** Enable visual tracing of representative order payloads against the workflow DAG.

### Explicit Non-Goals (Out of Scope for MVP):
- ❌ **No Text-to-Workflow Generation:** Sidekick already generates workflows; FlowLens does not compete on text-to-DAG authoring.
- ❌ **No Live Production Monitoring:** Shopify's public API does not expose historical step-level execution traces. FlowLens is strictly an offline pre-activation tool.
- ❌ **No Automatic Store Mutation:** FlowLens does not directly write to merchant stores or auto-activate workflows. All changes are exported as reviewable diffs.
- ❌ **No Production Shopify App Integration:** The v1.0 deliverable is a front-end concept prototype demonstrated on representative synthetic fixtures.

---

## 3. User Persona (Illustrative Archetype)

### Sarah — Scaling Ecommerce Operations Lead
- *Note: Fictional illustrative persona derived from merchant operational profiles; not an empirical study participant.*
- **Store Profile:** Scaling D2C brand; 10–20 active Flow automations; connected to shipping and messaging apps.
- **Job-to-be-Done:** When updating a complex order workflow, verify that conditions and data dependencies match Shopify's data model before activation.

---

## 4. Functional Requirements & Revised Linting Ruleset

### FR-1: Schema Ingestion & DAG Representation
- **Requirement:** Ingest exported Shopify Flow JSON definitions. Parse triggers, conditions, actions, and connections into a visual node graph.
- **Handling Incomplete Schemas:** When connector schemas or custom field definitions are unavailable, mark affected nodes as "Unverified Connector Schema" rather than emitting a false pass.

---

### FR-2: The 5 Revised Linting Rules

| Rule ID | Rule Name | Description | Severity | Expected Behavior |
|---|---|---|---|---|
| **L-01** | **Schema-Aware Country Code Check** | Validates fields whose Shopify schema type is strictly ISO-3166-1 alpha-2 (e.g., `shippingAddress.countryCode`). | **WARNING** | Flags 3-letter codes (`"USA"`) or full names (`"United States"`) compared against alpha-2 fields. |
| **L-02** | **Pre-Wait Data Action Output Misuse** | Shopify refreshes core store data after a Wait step, but **discards outputs from `Get`, `Sum`, or `Count` data actions** executed before the Wait. | **HIGH RISK** | Flags any step *after* a `Wait` node that references pre-Wait data variables, recommending a repeat `Get data` step. |
| **L-03** | **Informational No-Action Branch Note** | Shopify officially supports intentional no-op branches when conditions are unmet. | **INFORMATIONAL** | Explains that the False branch terminates without action, letting the merchant verify if this was intentional. |
| **L-04** | **Liquid Syntax & Schema Validation** | Checks Liquid tokens in action payloads for valid delimiters and null safety. | **WARNING** | Flags unclosed Liquid tags or references to non-existent schema properties. |
| **L-05** | **Potential Self-Triggering Cycle Detection** | Heuristic checking for workflows triggered by a resource event (e.g., `Order updated`) that modify the same resource without an idempotency guard. | **WARNING** | Warns of potential recursive triggering and API throttling risks. |

---

### FR-3: Grounded Diagnostic Explainer
- **Architectural Guardrail:** LLM explanations must be strictly bounded by the deterministic AST rule outputs. The system provides:
  1. The triggered rule ID and exact JSON path.
  2. The verified Shopify documentation reference.
  3. A suggested reviewable JSON diff.
- *Explicit Limitation:* Explanations should communicate uncertainty where third-party connector schemas are unverified.

---

### FR-4: Synthetic Scenario Simulation
- Evaluates supplied or generated order payloads against the workflow logic.
- Renders the simulated path taken (nodes evaluated, branches chosen, actions that would fire).
- Discloses external actions (e.g., third-party webhooks) that cannot be simulated offline.

---

## 5. UI/UX Design Specifications (Concept Prototype)

Following the dark slate / Oxford Midnight aesthetic (`#070a11` / `#0b0f19`) seen in advanced AI operations tools (e.g., Revka Agent OS):
1. **Top Bar:** Workflow title, file type, Preflight Status (Issue Counts: e.g., `1 Fatal Defect`, `1 Informational Note`), and Remediation CTA.
2. **Left Canvas (DAG View):** Interactive node canvas displaying Trigger $\rightarrow$ Condition $\rightarrow$ Action nodes with color-coded status borders.
3. **Right Inspector:**
   - Detailed rule findings with documentation citations.
   - Interactive Scenario Runner: Click between nominal pass, nominal fail, and edge-case payloads.
   - Reviewable Schema Diff viewer.
