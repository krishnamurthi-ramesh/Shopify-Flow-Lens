# Product Requirements Document (PRD): FlowLens Preflight Validator

**Product Name:** FlowLens  
**Release Target:** MVP v1.0 (Preflight Linter & Risk Inspector)  
**Author:** R. Krishnamurthi (AI Product Manager)  
**Status:** Approved for Prototype Build (Deliverable 2)  
**Last Updated:** November 2026  

---

## 1. Executive Summary & Product Vision

Shopify merchants rely on Shopify Flow to automate critical business operations (fraud detection, VIP tagging, fulfillment routing). However, Shopify Engineering disclosed that fine-tuned workflow generation suffered a **35% lower activation rate** in production, driven by complex condition patterns (16%) and missing configuration parameters (25%). Furthermore, our study of 25 merchant discussions revealed that automations frequently fail silently due to semantic data mismatches (e.g., `"USA"` vs `"US"` ISO-2) or unhandled edge cases.

**FlowLens** is a pre-activation workflow linter and diagnostic simulator. It ingests exported Shopify Flow workflow definitions (`.flow` JSON schemas), statically analyzes the trigger-condition-action DAG, detects semantic logic errors and race condition hazards, simulates representative edge-case order payloads, and provides plain-language diagnostic explanations and verified JSON diffs before automations touch live customer orders.

---

## 2. Goals & Non-Goals

### Core Goals:
1. **Zero-Blast-Radius Preflight:** Enable operators to catch silent semantic errors and unhandled branch terminations before turning a workflow on.
2. **High-Precision Linting ($\ge 95\%$ accuracy on seeded rules):** Use deterministic AST parsing to detect known failure patterns (ISO code mismatches, missing delay guards, null Liquid tokens).
3. **Actionable Plain-Language Explanations:** Translate obscure schema checks into merchant-comprehensible operational risks and concrete remediation steps.
4. **Interactive Scenario Simulation:** Generate synthetic order payloads (Passing, Failing, Edge Case) to let merchants visually inspect how their workflow evaluates before deployment.

### Explicit Non-Goals (Out of Scope for MVP):
- ❌ **No Text-to-Workflow Generation:** Sidekick already generates workflows; FlowLens does not compete on text-to-DAG generation.
- ❌ **No Native Live Webhook Interception:** As demonstrated in our API feasibility spike, Shopify does not expose a public step-level execution trace API. FlowLens will not promise live webhook routing or runtime interceptors.
- ❌ **No Automated Store Writes without Human Approval:** FlowLens provides suggested JSON diffs, but never modifies store data directly.

---

## 3. User Personas & Core User Journey

### Primary Persona: Sarah — Head of Ecommerce Operations
- **Store Profile:** $2M GMV scaling apparel brand; 14 active Flow automations; connected to ShipStation and Klaviyo.
- **Pain Point:** Created a workflow to tag international VIPs; used country string `"United States"`; workflow evaluated to False for all US orders; zero alerts were triggered until the warehouse shipped standard parcels.

### The 5-Step End-to-End User Journey:

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│ 1. Import Flow  │ ──> │ 2. AST Parse &  │ ──> │ 3. Interactive  │
│    (Upload JSON │     │    Lint Engine  │     │    DAG & Alerts │
│    or Template) │     │    Execution    │     │    Visualization│
└─────────────────┘     └─────────────────┘     └─────────────────┘
                                                         │
                                                         ▼
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│ 5. Export Clean │ <── │ 4. Plain-Lang   │ <── │ 4. Seeded Edge  │
│    Workflow     │     │    Remediation  │     │    Scenario     │
│    Schema JSON  │     │    & Diff Review│     │    Simulation   │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

---

## 4. Functional Requirements & Feature Specifications

### FR-1: Workflow Schema Ingestion & DAG Parser
- **Requirement:** Ingest Shopify Flow exported workflow JSON or standard pre-built templates.
- **Parsing Logic:** Parse the JSON into an Abstract Syntax Tree (AST) representing:
  - `trigger`: Resource type (`order`, `customer`, `inventory`), event type, payload schema.
  - `conditions`: Field references, comparison operators (`EQUAL_TO`, `CONTAINS`, `GREATER_THAN`), target literal values.
  - `actions`: Target application (Shopify native, Klaviyo, Slack), action type, required inputs.
- **Acceptance Criteria:** Successfully parse valid Flow schema exports and flag malformed JSON with specific line-level syntax errors.

---

### FR-2: Deterministic Linting Engine (The 5 Core Rules)
The linter executes deterministic static checks against known failure categories:

| Rule ID | Rule Name | Description | Severity | Example Violation |
|---|---|---|---|---|
| **L-01** | **Country ISO-2 Compliance** | Shopify addresses store country codes as 2-letter ISO (`US`, `CA`). Flags full country names or 3-letter codes. | **CRITICAL** | `shippingAddress.countryCode == "USA"` |
| **L-02** | **Stale Data After Delay** | Workflows with `Wait` actions (e.g., 2 hours) followed by state checks (e.g., `financialStatus == "PAID"`) without a "Get Order Data" refresh action. | **HIGH** | Wait 3 days $\rightarrow$ Check tag (evaluates stale memory). |
| **L-03** | **Unhandled False Termination** | High-consequence conditions (fraud, VIP routing) where the `False` branch has no fallback or notification. | **MEDIUM** | If high fraud risk $\rightarrow$ Hold order. Else $\rightarrow$ (Terminates silently). |
| **L-04** | **Null Liquid Token Danger** | Liquid placeholders in action payloads referencing optional fields without default fallbacks. | **HIGH** | `{{ order.customer.defaultAddress.phone }}` without `\| default: ''`. |
| **L-05** | **Idempotency Loop Guard** | Workflows triggered by `Order updated` that execute `Update order` actions without an exit condition, risking recursion. | **CRITICAL** | `Order updated` $\rightarrow$ `Add tag` (triggers self in loop). |

---

### FR-3: Plain-Language Diagnostic Explainer (AI Layer)
- **Role of AI:** LLMs are **NOT** used to evaluate condition logic (which causes hallucinations). Instead, deterministic linting outputs are passed to a grounded LLM explainer.
- **Input Payload to LLM:**
  ```json
  {
    "rule_triggered": "L-01: Country ISO-2 Compliance",
    "field": "order.shippingAddress.countryCode",
    "operator": "EQUAL_TO",
    "actual_literal": "USA",
    "expected_format": "ISO-3166-1 alpha-2 (e.g. 'US')",
    "workflow_context": "Tag orders for US Domestic Priority Shipping"
  }
  ```
- **Output Generated:**
  > *"**Critical Risk:** In Shopify Flow, `shippingAddress.countryCode` always yields a 2-letter code like `'US'`. Checking against `'USA'` will evaluate to False on 100% of US orders. This workflow will silently skip tagging all domestic orders."*

---

### FR-4: Synthetic Scenario Generation & Simulation
- **Requirement:** For any uploaded workflow, FlowLens automatically generates 3 synthetic order payloads:
  1. **Nominal Pass Scenario:** Payload where all conditions are met; executes primary action.
  2. **Nominal Fail Scenario:** Payload where a condition is intentionally unmet; traces fallback path.
  3. **Adversarial Edge Case:** Payload testing boundary conditions (e.g., lowercase strings, missing shipping address, zero total price).
- **Interactive Visualization:** Merchants can click through the scenarios and watch the DAG nodes highlight dynamically.

---

### FR-5: Recommended Diff & Export
- **Requirement:** When an issue is identified, FlowLens renders a side-by-side JSON diff:
  ```diff
  - "value": "USA"
  + "value": "US"
  ```
- **Remediation Action:** One-click "Apply Remediation" button that updates the AST and allows the user to download the corrected `.flow` JSON file ready for import into Shopify Admin.

---

## 5. UI/UX Design Specifications (Dribbble-Inspired High Craft)

Following the editorial, dark Oxford Midnight aesthetic (`#0b0f19`):
1. **Top Bar:** Project metadata, Flow name, "Run Preflight Audit" CTA, Health Score Indicator (0–100%).
2. **Left Panel (The Visual DAG):** Interactive node canvas showing Trigger $\rightarrow$ Condition $\rightarrow$ Action nodes with color-coded status rings (Green = Clean, Yellow = Warning, Red = Critical Bug).
3. **Right Panel (The Diagnostic Inspector):**
   - Severity Alert Cards (Critical, Warning, Optimization).
   - "Why this fails" plain-English breakdown.
   - Interactive Scenario Runner with tabs: `Scenario 1: California Order`, `Scenario 2: Toronto Order`, `Scenario 3: Null Phone`.
   - Side-by-side Schema Diff viewer with "Download Fixed Flow" CTA.

---

## 6. Technical Architecture & Guardrails

```
┌─────────────────────────────────────────────────────────────┐
│                 FlowLens Architecture Pipeline              │
│                                                             │
│   [ Uploaded Flow JSON ]                                    │
│             │                                               │
│             ▼                                               │
│   [ Deterministic AST Parser & Schema Validator ]          │
│             │                                               │
│             ├──> [ Rule Engine: L-01 to L-05 ] (Zero LLM)   │
│             │            │                                  │
│             │            ▼ (Lint Findings JSON)             │
│             └──> [ LLM Explanation Synthesis Layer ]        │
│                          │                                  │
│                          ▼                                  │
│   [ Interactive UI: Visual DAG + Risk Alerts + Fix Diff ]   │
└─────────────────────────────────────────────────────────────┘
```

### Safety & Guardrail Rules:
1. **Zero Hallucination Policy:** The LLM can only format and explain errors surfaced by the deterministic AST engine. It cannot invent errors not identified by AST rules.
2. **Data Privacy:** FlowLens processes schemas in-browser or in ephemeral memory. No store customer data or credentials are required or stored.
