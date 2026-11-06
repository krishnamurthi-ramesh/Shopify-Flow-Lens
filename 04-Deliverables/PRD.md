# Product Requirements Document (PRD)
## FlowPilot AI: Trust-First Natural Language Automation for Ecommerce Operations

- **Document Version:** 2.0 (Production Release)
- **Author:** R. Krishnamurthi (AI Product Manager)
- **Target Audience:** VP of Product, Head of Engineering, Merchant Experience Lead
- **Product Type:** Embedded Shopify Application / AI Workflow Automation Studio
- **Status:** Approved for Prototype Build & Merchant Alpha Testing

---

## 1. Executive Summary & Problem Space

### 1.1 The Market Opportunity
Shopify merchants generate billions of operational events monthly. While Shopify Flow provides a powerful visual DAG rules engine, the vast majority of growth merchants ($50k–$2M GMV) abandon automation creation because visual node programming requires technical knowledge of data schemas, nested conditional logic, and Liquid template tags.

When Shopify introduced natural language generation in Sidekick, internal A/B experiments revealed a **35% lower workflow activation rate** for their fine-tuned model compared to baseline prompt workflows. Analysis shows that merchants do not abandon because of poor natural language parsing; **they abandon because generated flows have empty required parameters, offer zero pre-execution simulation, and induce anxiety regarding catastrophic store errors (e.g. accidental customer emails or unauthorized order cancellations).**

### 1.2 The Solution: FlowPilot AI
**FlowPilot AI** is a trust-first natural-language automation studio built as an embedded Shopify App. It bridges the "Activation Chasm" through three core innovations:
1. **Context-Aware Parameter Auto-Fill:** Proactively resolves emails, tags, and thresholds using live store metadata instead of leaving empty validation error fields.
2. **Pre-Execution Historical Dry-Run Simulator:** Tests the draft workflow against the merchant's **last 10 actual store orders**, visually showing exact matching orders and simulated actions before activation.
3. **Blast-Radius Risk Gating:** Scores workflows as Low, Medium, or High Risk, requiring explicit confirmation gates for destructive actions (cancellations, refunds).

---

## 2. User Personas & Jobs-To-Be-Done (JTBD)

| Persona | Profile & Context | Primary Frustration | Core Job-To-Be-Done (JTBD) |
|---|---|---|---|
| **Sarah Lin** | Solo Founder & Ops Lead, $450k GMV Apparel Store | Spends 2.5 hours every evening manually checking delayed orders and tagging VIP repeat buyers. | *"Help me automate order tagging and customer delay notices in plain English, without me having to learn visual node programming or risk emailing customers mistakenly."* |
| **Jake Miller** | Operations Director, $1.8M GMV Supplements Brand | 3PL warehouse charges penalties for unflagged address errors. Terrified of broken Shopify Flow rules. | *"Give me absolute visibility into what an automation will do on real historical orders before I toggle it live, so our warehouse never experiences a fulfillment halt."* |

---

## 3. Product Principles

1. **Verify Before Execution:** A merchant should never have to deploy an automation blindly. Every rule must be dry-runnable on real store data in <500ms.
2. **Zero Red-Error Blank Fields:** If a parameter is unknown, the AI must proactively suggest it based on store history or ask conversationally. Never dump the user onto a node canvas with red error badges.
3. **Conversational Invariance:** Merchants should be able to edit and refine the automation in plain English without context-switching between chat and a node editor.
4. **Blast-Radius Proportionality:** Benign actions (tags, internal notes) should activate with 1 click; destructive actions (cancellations, refunds) must have confirmation guardrails.

---

## 4. Functional Requirements

### FR-1: Natural-Language Intent & TCA Synthesizer
* **Description:** Parses free-form merchant English and extracts the **Trigger, Condition(s), and Action(s)** into a standardized Shopify Flow JSON schema.
* **Acceptance Criteria:**
  - Parsing latency $\le 1.2\text{s}$.
  - Correctly maps to standard Shopify Flow trigger events (`order_created`, `inventory_level_changed`, `customer_risk_analyzed`).
  - Supports compound Boolean logic (AND / OR).

### FR-2: Context-Aware Parameter Auto-Resolver
* **Description:** Eliminates the "blank field trap" by auto-populating parameters using store metadata:
  - If action is `Send Internal Email`, auto-fills the primary store owner's email address from shop settings.
  - If condition references tags, matches against existing store customer/product tags.
  - If threshold is ambiguous (*"high value order"*), calculates store's 80th percentile AOV (Average Order Value) and pre-populates with option to modify.
* **Acceptance Criteria:** Zero empty required fields in generated draft.

### FR-3: Pre-Execution Historical Dry-Run Simulator (Core Differentiator)
* **Description:** Upon flow generation, the system instantly executes the candidate rule against the **last 10 historical orders** fetched via Shopify GraphQL API.
* **Acceptance Criteria:**
  - Simulation turnaround time $\le 450\text{ms}$.
  - Displays a clean visual card: *"Tested on 10 recent orders: 2 matched, 8 skipped"*.
  - Expands into an Order Inspection Table showing:
    - Order #, Customer, Amount, Matched Conditions (✓/✕), and Simulated Action Output.

### FR-4: Blast-Radius & Risk Scoring Gate
* **Description:** Evaluates the destructive potential of the generated actions:
  - **Low Risk (Green):** Adding/removing tags, internal notes, staff Slack alerts. (1-Click Instant Activation)
  - **Medium Risk (Yellow):** Customer-facing emails or SMS updates. (Requires preview of rendered copy)
  - **High Risk (Red):** Order cancellation, payment refund, inventory adjustment. (Requires explicit slide-to-confirm gate)
* **Acceptance Criteria:** High-risk actions cannot be toggled active without explicit modal confirmation.

### FR-5: Bi-Directional Conversational Refinement
* **Description:** Merchants can modify the generated flow in conversational chat (*"Wait, also exclude international orders"* or *"Make the threshold $250"*).
* **Acceptance Criteria:**
  - Modifies existing workflow state incrementally without regenerating from scratch.
  - Automatically re-runs the 10-order historical simulation and updates the match count in real-time.

### FR-6: 1-Click Shopify Flow GraphQL Export & Toggle
* **Description:** Directly compiles the verified workflow into standard Shopify Flow DSL and transmits it via Shopify's GraphQL API (`flowWorkflowCreate`).
* **Acceptance Criteria:**
  - 1-click publishing directly into the merchant's Shopify Flow dashboard.
  - Generates downloadable backup JSON file.

---

## 5. Non-Functional Requirements & Performance SLOs

| Metric | Target SLO | Measurement Mechanism |
|---|---|---|
| **End-to-End Generation Time** | $\le 1.5\text{s}$ P95 | Client prompt submission to visual flow display |
| **Historical Simulation Time** | $\le 500\text{ms}$ P95 | Running 10-order evaluation pass in client-side runtime |
| **Liquid Tag Safety** | Zero unhandled runtime exceptions | Client-side schema linter catches missing Liquid properties |
| **Data Privacy & Security** | Zero customer PII stored | Simulation runs ephemerally in-session; complies with Shopify API terms |

---

## 6. Success Metrics & Experimentation Plan

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                                 NORTH STAR METRIC                                      │
│                                                                                        │
│                      30-Day Workflow Activation Rate                                   │
│              (Target: Increase from 65% baseline to ≥85%)                              │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

### Secondary & Guardrail Metrics:
1. **Time-to-First-Activation:** Reduce median time from prompt entry to active flow from 22 minutes (baseline Flow builder) to **<90 seconds**.
2. **Conversational Edit Success Rate:** $\ge 80\%$ of multi-turn flow adjustments resolved successfully in chat without canvas drop-out.
3. **Flow Reversal / Deactivation Rate (Guardrail):** $<3\%$ of flows deactivated within 48 hours of activation (measuring merchant regret/errors).

---

## 7. Go-To-Market (GTM) & Monetization Strategy

### 7.1 Distribution Wedge: Shopify App Store
* **Listing Title:** *FlowPilot AI — Safe Natural Language Automations & Historical Dry-Run*
* **Search Optimization:** Keywords: `Shopify Flow`, `automation`, `Sidekick`, `order tagging`, `fraud protection`.
* **Wedge:** Positioned as the *"Safety & Simulation Layer for Shopify Flow"*.

### 7.2 Tiered Pricing Architecture
* **Starter (Free Tier):** Up to 3 active AI-generated flows, 50 simulated historical orders/month. Designed for store owners under $100k GMV.
* **Growth ($29/month):** Unlimited active flows, real-time historical simulation, customer email copy generation, risk protection gates.
* **Scale ($79/month):** Multi-store sync, priority webhooks, custom third-party connector support (Klaviyo, Gorgias, ShipStation).

---

## 8. Rollout Plan & Milestones

```
Phase 1: Design & Interactive Sandbox Prototype (Days 1–5)
├── Implement TCA Generator & Parameter Auto-Resolver
├── Build 10-Order Historical Dry-Run Simulator with mock dataset
└── Deploy clean institutional web app on Vercel

Phase 2: Closed Alpha with 10 D2C Shopify Merchants (Days 6–15)
├── Test 25 real-world ecommerce automation prompts
└── Measure simulation accuracy, parameter completion rate, and qualitative trust

Phase 3: Shopify App Store Public Beta (Days 16–30)
├── Integrate Shopify App Bridge and GraphQL API
└── Target 100 merchant installs with >80% flow activation rate
```

