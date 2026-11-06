# Shopify Flow + Sidekick: AI Workflow Automation Teardown & Product Discovery

[![Role](https://img.shields.io/badge/Role-AI_Product_Manager-7c3aed?style=flat-square)]()
[![Type](https://img.shields.io/badge/Type-Evidence--Led_Product_Teardown-008060?style=flat-square)]()
[![Status](https://img.shields.io/badge/Status-Phase_3_Passive_Discovery_Active-blue?style=flat-square)]()

An evidence-led AI Product Management teardown analyzing Shopify's natural-language workflow generation in Shopify Flow and Sidekick. This project investigates why Shopify's internally fine-tuned tool-calling model initially delivered a **35% lower workflow activation rate** than the prompt-based baseline in a 1% production rollout, dissects the underlying product and architectural boundaries, and establishes testable opportunity hypotheses grounded in real merchant evidence.

---

## 📌 Executive Summary

Between 2024 and 2026, Shopify introduced natural-language workflow generation within **Sidekick** and **Shopify Flow**. In an engineering disclosure (*"Flow generation through natural language: An agentic modeling approach"*), Shopify revealed that while its fine-tuned Qwen3-32B tool-calling agent achieved high syntactic accuracy on offline benchmarks, its **workflow activation rate in a 1% traffic deployment was 35% lower** than the closed-model prompt baseline.

Shopify attributed this gap to live merchant requests not adequately represented in their synthetic training distribution:
- **Email configurations** (accounting for 25% of failure slices)
- **Diverse condition patterns** (16% of failures)
- **Editing existing workflows** (absent from initial synthetic data)
- **Third-party integrations & connectors**
- **Informational questions about Flow** (where the user had no intent to create a flow)

Rather than inventing problems or proposing features that Shopify already supports (such as basic pre-activation event testing and conversational Apply/Discard updates), this teardown investigates the **specific failure boundaries** of natural-language automation: intent classification, guided parameter completion, test coverage clarity, change review diffs, and integration readiness.

---

## 📂 Project Repository Structure

```
Project-4-Shopify-Flow-AI/
├── README.md                                      ← You are here: Teardown synthesis & executive brief
├── 01-Research/
│   ├── problem-discovery-and-user-segmentation.md ← Evidence ledger (E1–E17), behavioral segments, JTBD
│   └── reddit-evidence-log-01.md                  ← Initial passive Reddit signals (R1–R7) & observations
├── 02-Product-Teardown/
│   ├── shopify-flow-sidekick-deep-dive.md         ← Documented user journey, funnels, AI architecture
│   └── opportunity-hypotheses.md                  ← 5 testable opportunity hypotheses (H1–H5)
└── 03-Discovery-Plan/
    ├── merchant-interview-guide.md                ← Semi-structured 30-min interview guide & note template
    └── phase-3-validation-roadmap.md              ← Complete Phase 3-to-8 execution roadmap
```

---

## 📊 Summary of Evidence Ledger (Shopify Disclosures)

Our analysis distinguishes verified first-party disclosures from product hypotheses:

| Evidence ID | Disclosed Fact | Source | What It Establishes | What It Does Not Establish |
|---|---|---|---|---|
| **E1** | Shopify Flow uses Triggers, Conditions, and Actions (TCA). | Engineering Blog; Help Center | The foundational DAG mental model. | That all merchants find this model difficult. |
| **E2** | Sidekick generates workflows from natural language. | Engineering Blog; Help Center | Conversational alternative interface. | That natural language alone guarantees adoption. |
| **E3–E4** | Fine-tuned Qwen3-32B was 2.2x faster & 68% cheaper than closed models. | Engineering Blog | Cost & latency outcome for Shopify's stack. | Universal superiority or reproducibility. |
| **E7** | Activation rate was 35% lower than baseline in 1% rollout. | Engineering Blog | An online-to-offline production gap. | A 35% total abandonment rate across all users. |
| **E8–E9** | Email configs (25%) & editing were primary failure slices. | Engineering Blog | Concrete failure slices observed in production. | That these are the only sources of failure. |
| **E12–E14**| Flows are inactive by default; recorded/simulated event testing exists. | Help Center | Testing & human review exist before activation. | That all external actions or risks are detected. |
| **E16–E17**| Conversational editing (Apply/Discard) and version history exist. | Help Center | Iterative refinement and rollback exist. | That users easily discover or understand them. |

---

## 💡 The 5 Testable Opportunity Hypotheses

| Hypothesis ID | Title | Root Friction Addressed | Proposed Validation Approach |
|---|---|---|---|
| **H1** | **Intent-Aware Routing** | Non-creation requests (editing, questions) forced into creation workflows. | Usability tests with mixed creation, editing, and informational tasks. |
| **H2** | **Contextual Configuration Completion** | Missing required values (e.g. emails) causing Review-banner drop-offs. | Compare current Review banner with in-context guided parameter resolution. |
| **H3** | **Test Coverage & External-Action Clarity** | Test mode cannot simulate external HTTP/app actions, leaving residual risk unclear. | Test preflight summary report highlighting covered vs. untested action paths. |
| **H4** | **Change Review for Conversational Edits** | Conversational updates lack visual diffs showing exact before/after logic changes. | Compare conversation-only edits with visual side-by-side diffs. |
| **H5** | **Integration Readiness & Recovery** | Workflows fail when app connectors are uninstalled or missing permissions. | Pre-generation readiness check identifying missing connector dependencies. |

---

## 🧭 Active Phase: Phase 3 — Customer Discovery

Current milestone is **Phase 3A: Passive Discovery via Public Merchant Discussions**:
- [`01-Research/reddit-evidence-log-01.md`](./01-Research/reddit-evidence-log-01.md) — 7 initial Reddit discussions coded across observability, mobile operations, Sidekick consistency, data context, and integration boundaries.
- Target: Review 20 additional discussions across r/shopify, r/AutomateShopify, and r/shopifyDev to validate whether the real problem is creation, outcome observability, or error recovery before building solutions.
