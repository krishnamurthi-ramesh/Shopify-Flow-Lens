# Phase 3B — Targeted Validation Plan & Technical Feasibility Spike

**Project:** Shopify Flow + Sidekick AI Teardown & Product Opportunity  
**Current Gate:** Phase 4 Opportunity Prioritization completed (Leading: H3 Diagnostics, Second: H4 Change Diff)  
**Date:** November 2026  

---

## 📌 Executive Purpose

Passive customer discovery across 25 authentic merchant discussions (`R1`–`R25`) revealed that merchant friction does **not** stem from text-to-workflow generation (which Sidekick already handles), but from **silent condition mismatches, opaque execution diagnostics, and brittle multi-app integrations**.

Before committing to product development or writing code, Phase 3B executes two parallel, bounded validation tracks:
1. **Track A (Qualitative Validation):** 6–8 targeted incident-reconstruction interviews testing hypotheses H3 (Diagnostics) and H4 (Change Diff).
2. **Track B (Technical Feasibility Spike):** An architectural investigation into Shopify's public APIs (GraphQL Admin API, `flowTriggerReceive`, `Get workflow run data`) to establish whether third-party apps can access step-level execution traces.

---

## 🧭 Track A: Targeted Incident-Reconstruction Interviews

### 1. Target Participant Composition (6–8 Participants)
We do not screen by arbitrary GMV tiers. Instead, we recruit based on **operational workflow complexity and role**:

| Cohort | Profile | Target Count | Key Behavioral Criteria |
|---|---|---|---|
| **Cohort 1: Operator-Merchants** | Store owners / Ops managers | 4 participants | Regularly manage 3+ active Flow automations; have experienced at least one silent automation failure or missed order. |
| **Cohort 2: Flow Power Users / Agencies** | Shopify Plus Agency Leads / Devs | 2–3 participants | Build custom flows for multiple clients; manage 3rd-party connectors (Klaviyo, ShipStation, Gorgias) and Liquid conditions. |

### 2. The 7 Incident-Reconstruction Questions
Interviews follow a strict retrospective incident-reconstruction technique. Never ask hypothetical preference questions ("Would you pay for an AI diagnostic tool?").

1. **The Last Incident:** *"Think back to the last time a Shopify Flow automation didn't execute as you expected. What was the business consequence (e.g., untagged VIP, missed notification, unfulfilled order)?"*
2. **Detection Mechanism:** *"How did you first find out that something went wrong? Did Shopify alert you, did a customer complain, or did you spot it during manual spot-checks?"*
3. **The Diagnostic Path:** *"Walk me through the exact steps you took inside Shopify Admin to figure out why it failed. What screens did you open?"*
4. **Information Gap:** *"When looking at the Flow Run History, what specific question could you not answer from the native interface (e.g., 'Why did this condition evaluate to False?')?"*
5. **Modification Hesitation (H4):** *"When an existing workflow needs an update, how do you verify that adding a new condition won't break existing order processing?"*
6. **External Connector Friction (H5):** *"What happens when a third-party app action (like sending a Slack alert or Klaviyo event) fails? How does Flow alert you?"*
7. **The Workaround Cost:** *"What is your ongoing workaround to prevent this from happening again (e.g., daily manual audits, secondary apps, duplicate notification emails)?"*

---

## 🔬 Track B: Technical Feasibility Spike (Shopify Flow API Boundaries)

A critical technical risk exists: **Shopify provides native execution history for 14 days, but does the public GraphQL Admin API expose step-level condition evaluation traces to third-party apps?**

```
┌─────────────────────────────────────────────────────────────┐
│                   Shopify Admin Boundary                    │
│                                                             │
│  [ Native Flow Run UI ]                                     │
│     ├── Visual Step DAG                                     │
│     ├── Step Execution Status (Success, Failed, Skipped)    │
│     └── Step Input/Output Payloads (Sandboxed Internally)  │
└──────────────────────────────┬──────────────────────────────┘
                               │
            Public API Access? │ (Technical Feasibility Spike)
                               ▼
┌─────────────────────────────────────────────────────────────┐
│             External App / FlowLens Candidate               │
│                                                             │
│  Option A (Live Diagnostics): Needs Run Trace API           │
│  Option B (Preflight Validator): Needs Exported JSON Schema │
└─────────────────────────────────────────────────────────────┘
```

### Technical Investigation Checklist:

| # | Shopify Capability | Documentation / API Hook | Key Question to Verify | Impact on Product Strategy |
|---|---|---|---|---|
| **T1** | `Get workflow run data` Action | Native Flow Action | What fields does this action return? Does it expose step-level conditions or only top-level run ID and status? | If only metadata, external apps cannot monitor condition states post-run natively. |
| **T2** | GraphQL Admin API (`flowRun`) | `flowTriggerReceive` & Admin API | Is there a public GraphQL query to retrieve `FlowRun` historical step details? | Determines if live diagnostic dashboarding is technically feasible. |
| **T3** | Workflow Export / Import Schema | Flow JSON definition export | Does the exported Flow JSON contain complete condition schemas, field references, and Liquid strings? | Validates whether an offline preflight linter/validator can parse workflows without live API access. |
| **T4** | Webhook Subscriptions | Store event webhooks | Can an external app capture the same trigger event payload to re-simulate Flow condition logic independently? | Enables sidecar simulation if Shopify's native trace is inaccessible. |

---

## ⚖️ Joint Decision Gate: GO / PIVOT / NO-GO Matrix

Following completion of Tracks A and B, we evaluate our product direction against this objective matrix:

```
                                  Technical Feasibility (Track B)
                           Step Trace Accessible    Step Trace Blocked
                         ┌────────────────────────┬────────────────────────┐
                         │                        │                        │
  High Pain Observed     │      1. FULL GO        │       2. PIVOT         │
  (Silent failures       │   Build FlowLens Live  │   Build FlowLens       │
  cause material losses) │   Diagnostics Engine   │   Preflight Validator  │
Qualitative              │                        │   (Schema & Linter)    │
Validation               ├────────────────────────┼────────────────────────┤
(Track A)                │                        │                        │
  Low Pain Observed      │      3. REFRAME        │       4. NO-GO         │
  (Native logs deemed    │   Investigate H4       │   Document findings    │
  sufficient by users)   │   Visual Change Diff   │   in portfolio; do not │
                         │   in Flow Editor       │   build app            │
                         └────────────────────────┴────────────────────────┘
```

### Detailed Gate Outcomes:
1. **FULL GO (Live Diagnostics Engine):**
   - *Condition:* Merchants confirm painful silent condition debugging (Track A) **AND** GraphQL API exposes run execution data (Track B).
   - *Scope:* Build FlowLens to ingest execution traces, evaluate condition logic deterministically, and generate plain-language explanations of why conditions failed.
2. **PIVOT (Preflight Schema & Linting Inspector):**
   - *Condition:* Merchants confirm painful errors (Track A), but Shopify API restricts live step traces (Track B).
   - *Scope:* Pivot from live run monitoring to **Pre-Activation Linting & Simulation**. Users upload/export workflow JSON; FlowLens validates field types (e.g., ISO-2 country codes), checks for missing idempotency guards, and simulates edge-case order payloads before activation.
3. **NO-GO (Portfolio Transparency):**
   - *Condition:* Merchants report that native 14-day logs and Sidekick test modes solve their needs adequately.
   - *Action:* Document the complete research, technical boundary analysis, and disciplined decision not to build a redundant tool. This demonstrates elite PM judgment.

