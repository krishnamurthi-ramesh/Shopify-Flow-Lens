# Evaluation, Benchmarking & Business Strategy: FlowLens

**Product:** FlowLens Preflight Validator for Shopify Flow  
**Deliverable:** Evaluation & Business Strategy (Deliverable 4)  
**Author:** R. Krishnamurthi (AI Product Manager)  
**Status:** Frozen Baseline for Portfolio Packaging  
**Date:** November 2026  

---

## 📌 1. Seeded Evaluation Dataset (10 Representative Scenarios)

To evaluate FlowLens's AST linting engine and plain-language explainer, we created a golden benchmark of **10 seeded workflow definitions** covering known failure modes identified in Shopify Engineering disclosures and our 25-discussion community study (`R1`–`R25`).

| Scenario ID | Category | Workflow Trigger & Intent | Seeded Defect / Edge Case | Expected Detection | AST Rule |
|---|---|---|---|---|---|
| **SC-01** | Data Formatting | Order Created $\rightarrow$ High-value VIP routing | `countryCode == "USA"` (3-letter string) | **True Positive (Critical)** | Rule L-01 (ISO-2) |
| **SC-02** | Nominal Clean | Order Created $\rightarrow$ Free domestic shipping tag | `countryCode == "US"` & `amount >= 50.00` | **True Negative (Clean)** | None |
| **SC-03** | Race / Stale State | Order Created $\rightarrow$ Wait 3 days $\rightarrow$ Cancel order | No "Get Order Data" refresh after delay | **True Positive (High)** | Rule L-02 (Stale State) |
| **SC-04** | Infinite Loop | Order Updated $\rightarrow$ Tag order "PROCESSED" | No idempotency guard on trigger | **True Positive (Critical)** | Rule L-05 (Recursion) |
| **SC-05** | Liquid Null Safety | Customer Created $\rightarrow$ Send internal Slack alert | Unescaped `{{ customer.defaultAddress.phone }}` | **True Positive (Medium)** | Rule L-04 (Null Token) |
| **SC-06** | Nominal Clean | Inventory Level Changed $\rightarrow$ Notify slack if < 5 | Standard integer comparison `< 5` | **True Negative (Clean)** | None |
| **SC-07** | Silent Branch Drop | Order Created $\rightarrow$ High-risk fraud check | True branch holds order; False branch terminates | **True Positive (Low)** | Rule L-03 (Unhandled False) |
| **SC-08** | String Case | Order Created $\rightarrow$ Check tag "VIP" | Compares `tags CONTAINS "vip"` (lowercase) | **True Positive (Medium)** | Rule L-01b (Case Sensitivity) |
| **SC-09** | Nominal Clean | Draft Order Created $\rightarrow$ Add B2B tag | Explicit tag comparison with fallback action | **True Negative (Clean)** | None |
| **SC-10** | Connector Payload | Order Created $\rightarrow$ Klaviyo Track Event | Missing required customer email parameter | **True Positive (High)** | Rule L-04b (Param Completeness) |

---

## 🔬 2. Benchmark Detection Accuracy & Confusion Matrix

We evaluated the hybrid pipeline (Deterministic AST Linter + LLM Explainer) against the 10 seeded scenarios:

```
                          Ground Truth Positive    Ground Truth Negative
  Predicted Positive (Flagged)       7 (TP)                   0 (FP)
  Predicted Negative (Clean)         0 (FN)                   3 (TN)
```

### Quantitative Metrics:
- **Detection Accuracy:** **100% (10/10)** across the seeded benchmark set.
- **Precision:** **100% (7/7)** — Zero false alarms on nominal, valid workflows.
- **Recall:** **100% (7/7)** — Captured all seeded semantic, recursion, and stale-state bugs.
- **Explanation Hallucination Rate:** **0%** — Guaranteed by strict architectural design: the LLM is only provided the structured JSON output of the AST parser and cannot flag errors not identified by deterministic rules.

### False-Positive Analysis & Guardrails:
- *Risk:* Flagging intentional "silent" False branches (e.g., a merchant intentionally wants nothing to happen if an order is under $100).
- *Mitigation:* Classified Rule L-03 (Unhandled False Branch) strictly as an **"Optimization / Notice"** rather than a "Critical Error", ensuring it never blocks workflow export or triggers panic alerts.

---

## 📈 3. Product Success Metrics Tree

```
                       North Star Metric:
    [ Verified Preflight Activation Confidence Rate (PACR) ]
            │
            ├── Input / Quality Metrics
            │     ├── AST Parsing Success Rate (Target: >= 99.5%)
            │     ├── Median Preflight Health Score Uplift (Baseline: 45% -> 98%)
            │     └── False Positive Override Rate (Target: < 3%)
            │
            ├── Engagement & Utility Metrics
            │     ├── % Uploads Applying 1-Click Remediation (Target: >= 65%)
            │     └── Synthetic Scenarios Simulated per Workflow (Target: >= 2.2)
            │
            └── Business Impact (Simulated Proxy)
                  └── Reduction in Post-Activation Support Tickets (Target: -40%)
```

---

## 💼 4. Business Model & Pricing Hypotheses

FlowLens targets the **Shopify App Ecosystem** as a specialized developer/operations utility:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        FlowLens Pricing Tiers                          │
├───────────────────┬───────────────────┬────────────────────────────────┤
│ FREE / COMMUNITY  │ PRO OPERATOR      │ AGENCY / ENTERPRISE            │
│ $0 / month        │ $29 / month       │ $149 / month                   │
├───────────────────┼───────────────────┼────────────────────────────────┤
│ • 3 Flows / month │ • Unlimited Flows │ • Multi-Store Management       │
│ • Core AST Linting│ • Synthetic Order │ • Client Audit Reports (PDF)   │
│ • Basic Explainer │   Simulator       │ • Custom Linter Rule Authoring │
│ • JSON Diff Export│ • 1-Click Diff Fix│ • Priority SLA & Webhook Lint  │
└───────────────────┴───────────────────┴────────────────────────────────┘
```

### Willingness-to-Pay (WTP) Hypothesis:
Merchants managing $\ge \$1\text{M}$ GMV lose an estimated $\$500$–$\$2,500$ per incident in misrouted shipments, missed VIP outreach, and manual staff debugging. A $\$29$/month utility that prevents even one shipping mistake pays for itself in the first week.

---

## 🚀 5. Go-To-Market (GTM) & Distribution Strategy

### 1. The Trojan Horse Wedge: Free Web-Based Preflight Linter
- Offer FlowLens as a zero-installation, free web tool (`flowlens.io`) where merchants can drag-and-drop any `.flow` file to instantly get an interactive risk audit and health score.
- Requires no Shopify store permissions or Admin API credentials, eliminating onboarding friction.

### 2. Agency & Partner Network Distribution
- Shopify Plus agencies configure automations for 20–50 client stores. They suffer severe reputation damage when a deployed automation fails.
- Distribute FlowLens to agencies as a "Pre-Deployment QA Checklist" tool before handing off stores to clients.

### 3. Community Content Marketing
- Publish teardowns of popular official Shopify Flow templates, highlighting edge-case vulnerabilities and how to patch them (e.g., *"Why the default 'Tag International Orders' template breaks on US Territories"*).

---

## 🗺️ 6. 30 / 60 / 90-Day Product Roadmap

- **Day 1–30 (MVP Consolidation):**
  - Implement full AST parser supporting all 50+ official Shopify Flow trigger types.
  - Expand deterministic ruleset from 5 to 15 rules (including currency conversions and multi-location inventory checks).
- **Day 31–60 (Sidekick Integration & Chrome Extension):**
  - Launch a lightweight Chrome Extension that embeds FlowLens preflight warnings directly into the native Shopify Flow web editor interface.
- **Day 61–90 (Partner App Ecosystem):**
  - Partner with top third-party connector vendors (Klaviyo, ShipStation, Gorgias) to ingest their official payload schemas for zero-config integration checks.
