# Evaluation, Benchmarking & Business Strategy: FlowLens

**Product:** FlowLens Preflight Analyzer for Shopify Flow  
**Deliverable:** Evaluation & Business Strategy (Deliverable 4)  
**Author:** R. Krishnamurthi (AI Product Manager)  
**Status:** Post-Audit Defensible Baseline  
**Date:** November 2026  

---

## 📌 1. Seeded Evaluation Dataset (10 Author-Created Fixtures)

To test the viability of FlowLens's AST linting concepts and diagnostic explainer, we authored a fixture set of **10 synthetic workflow definitions** covering known failure modes identified in Shopify Engineering disclosures, official Flow documentation, and verified community discussions.

| Scenario ID | Category | Workflow Trigger & Intent | Seeded Defect / Edge Case | Expected Detection | AST Rule |
|---|---|---|---|---|---|
| **SC-01** | Data Formatting | Order Created $\rightarrow$ High-value VIP routing | `shippingAddress.countryCode == "USA"` | **Flagged (Hazard)** | Rule L-01 (ISO-2) |
| **SC-02** | Nominal Clean | Order Created $\rightarrow$ Free domestic shipping tag | `shippingAddress.countryCode == "US"` & `amount >= 50.00` | **Passed (Clean)** | None |
| **SC-03** | Data Dependency | Order Created $\rightarrow$ Get customer data $\rightarrow$ Wait 3 days $\rightarrow$ Send email using pre-Wait count | Post-Wait action references pre-Wait `Get data` output | **Flagged (Hazard)** | Rule L-02 (Pre-Wait Output Loss) |
| **SC-04** | Infinite Loop | Order Updated $\rightarrow$ Tag order "PROCESSED" | No idempotency condition check on trigger | **Flagged (Warning)** | Rule L-05 (Recursion Heuristic) |
| **SC-05** | Liquid Syntax | Customer Created $\rightarrow$ Send internal Slack alert | Unclosed Liquid tag `{{ customer.email }` | **Flagged (Warning)** | Rule L-04 (Liquid Syntax) |
| **SC-06** | Nominal Clean | Inventory Level Changed $\rightarrow$ Notify slack if < 5 | Standard integer comparison `< 5` | **Passed (Clean)** | None |
| **SC-07** | Intentional No-Op | Order Created $\rightarrow$ High-risk fraud check | True branch holds order; False branch terminates | **Informational Note** | Rule L-03 (No-Action Branch) |
| **SC-08** | String Case | Order Created $\rightarrow$ Check tag "VIP" | Compares `tags CONTAINS "vip"` (lowercase) | **Flagged (Warning)** | Rule L-01b (Case Sensitivity) |
| **SC-09** | Nominal Clean | Draft Order Created $\rightarrow$ Add B2B tag | Explicit tag comparison with fallback action | **Passed (Clean)** | None |
| **SC-10** | Unverified Schema | Order Created $\rightarrow$ Third-Party App Action | Action references external app payload | **Unverified Notice** | Schema Boundary Notice |

---

## 🔬 2. Benchmark Fixture Performance & Limitations

Across this hand-authored fixture set:

```
                          Ground Truth Positive    Ground Truth Negative
  Predicted Positive (Flagged)       7 (TP)                   0 (FP)
  Predicted Negative (Clean)         0 (FN)                   3 (TN)
```

### Defensible Performance Statements:
- **Fixture Coverage:** **10/10 seeded benchmark cases** were correctly parsed and evaluated according to the authored rule specifications.
- **Fixture Precision:** **No false positives observed in this 10-case fixture set**. Clean nominal workflows (SC-02, SC-06, SC-09) passed without spurious blocking errors.
- **Scope Limitation:** A 10-scenario fixture benchmark demonstrates that the rule logic is internally consistent. It **does not establish statistical reliability, generalizability, or robustness across uncurated merchant workflows in production**.
- **Explanation Grounding:** Diagnostic explanations are generated from deterministic rule outputs, parser paths, and official documentation citations. While deterministic AST outputs are bounded, LLM-generated explanatory text cannot be guaranteed hallucination-free; explanations must communicate uncertainty when external schemas are unverified.

---

## 📈 3. Product Success Metrics (Proposed Hierarchy)

```
                       North Star Metric:
    [ Verified Preflight Activation Confidence Rate (PACR) ]
            │
            ├── Input & Parser Quality Metrics
            │     ├── Valid Schema Ingestion Rate (Target: >= 98%)
            │     ├── Median Preflight Issues Resolved per Upload
            │     └── User Override / Dismissal Rate (Target: < 5%)
            │
            ├── Engagement & Interaction Metrics
            │     ├── % of Users Reviewing Suggested Diffs (Target: >= 70%)
            │     └── Synthetic Scenarios Evaluated per Workflow (Target: >= 2.0)
            │
            └── Operational Impact (Proposed Longitudinal Proxy)
                  └── Post-Activation Execution Exceptions (Self-Reported Delta)
```

---

## 💼 4. Business Model & Pricing Hypotheses

*Note: The following tiers represent unvalidated commercial hypotheses to test during partner and merchant interviews; they do not represent validated willingness to pay or measured revenue projections.*

```
┌────────────────────────────────────────────────────────────────────────┐
│                   Unvalidated Pricing Hypotheses                       │
├───────────────────┬───────────────────┬────────────────────────────────┤
│ FREE UTILITY      │ PRO OPERATOR      │ AGENCY / PARTNER               │
│ $0 / month        │ $29 / month (Hyp) │ $149 / month (Hyp)             │
├───────────────────┼───────────────────┼────────────────────────────────┤
│ • 3 Flows / month │ • Unlimited Flows │ • Multi-Store Management       │
│ • Core AST Linting│ • Synthetic Order │ • Client Pre-Handoff Audits    │
│ • Basic Explainer │   Simulator       │ • Custom Ruleset Authoring     │
│ • Reviewable Diff │ • Diff Exporter   │ • Priority Review SLA          │
└───────────────────┴───────────────────┴────────────────────────────────┘
```

### Commercial Value Hypothesis:
Merchants managing complex stores face high indirect costs when an automation silently misroutes orders or drops VIP tags. A preflight analyzer that catches formatting and dependency hazards before activation could potentially justify a modest SaaS subscription, provided the tool integrates smoothly into existing agency or operator workflows.

---

## 🚀 5. Go-To-Market (GTM) Strategy & Product Roadmap

### Distribution Strategy:
1. **Zero-Friction Web Utility (`flowlens.io`):** A browser-based analyzer where merchants can upload an exported `.flow` file to get an immediate preflight health check without granting store API access.
2. **Shopify Plus Agency Partnerships:** Position FlowLens as a "Pre-Deployment QA Checklist" utility for agencies building custom client automations.
3. **Educational Content:** Publish teardowns of popular native Shopify Flow templates, documenting edge-case caveats (such as pre-Wait data loss and ISO country code requirements).

### Iteration Roadmap:
- **Phase 1 (Proof of Concept):** Front-end interactive prototype with seeded scenarios.
- **Phase 2 (Parser Hardening):** Full client-side `.flow` JSON parser supporting standard Flow trigger and condition schemas.
- **Phase 3 (Ecosystem Integration):** Evaluate alignment with Shopify's evolving automation APIs (`automationFlowFileParse`) as third-party access policies develop.
