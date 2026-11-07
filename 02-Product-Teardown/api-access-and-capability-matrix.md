# API Access & Capability Matrix: Shopify Flow Diagnostic Surfaces

**Project:** Shopify Flow + Sidekick AI Teardown & Opportunity Validation  
**Deliverable:** Technical Feasibility Spike — Deliverable 1 (Track B)  
**Status:** Complete & Verified against Official Shopify Documentation (2024–2026)  
**Primary Impact:** Resolves technical boundary between Path A (Live Diagnostics) and Path B (Preflight Validator)  

---

## 📌 Executive Summary

To evaluate whether an independent app can build **FlowLens Live Diagnostics** (explaining why a workflow took a specific branch or why a condition failed), we audited the three distinct technical access surfaces in the Shopify ecosystem:

1. **Shopify Admin UI:** Exposes visual DAG execution paths, step-level condition outcomes (True/False), and step input/output data for 14 days to authenticated store staff.
2. **Public GraphQL Admin API:** **Completely omits** `flowRun` or workflow execution queries. Exposes only the `flowTriggerReceive` mutation to trigger workflows from an external app.
3. **Flow Extension Framework:** Supports custom triggers (`flow_trigger`) and custom actions (`flow_action`), but **does not support custom conditions** (`flow_condition`). Furthermore, custom actions only execute if all upstream conditions evaluate to True; an app cannot receive webhooks for unexecuted or failed branches.
4. **Native `Get workflow run data` Action:** Returns high-level run metadata (Run ID, Start Time, Trigger Event, Workflow URL), but **strictly omits** step execution outcomes, condition evaluations, and intermediate variable payloads.

### Architectural Finding
> **A third-party app cannot inspect live condition evaluation traces or step outcomes via public Shopify APIs.**  
> Native execution traces are rendered server-side exclusively inside the Shopify Admin UI. Therefore, a pure post-execution live diagnostics engine (Path A) is **technically blocked** without requiring merchants to invasively instrument their workflows. This creates an immediate, highly defensible product pivot to **Path B: FlowLens Preflight Validator & Linter**.

---

## 🔬 Detailed Surface-by-Surface Audit

### Surface 1: Shopify Admin UI (Native Interface)
- **Target Audience:** Authenticated merchants and store staff with `View Shopify Flow runs` permission.
- **Retention Window:** Exactly 14 days for completed workflow runs.
- **What is Visible:**
  - Full DAG visual path indicating which branches ran and which were skipped.
  - Step-level execution state: `Started`, `Completed`, `Action failed`, `Waiting`.
  - Detailed Step Data: Shows the exact snapshot of the resource payload (e.g., Order, Customer, Inventory) at the moment of evaluation.
  - Test Run Interface: Displays preview results for recorded real events, simulated store events, and Sidekick-generated test events (without executing live external actions).
- **Merchant Friction Disclosed in Research (R1–R25):**
  - Shows *that* a condition was False, but does **not explain semantic mismatches** (e.g., why string `"USA"` failed against ISO code `"US"`, or why an update trigger re-evaluated stale data).
  - Requires manual pagination and clicking into individual nodes across hundreds of daily runs.

---

### Surface 2: Public GraphQL Admin API
We audited the official Admin GraphQL API reference (`latest`, `2025-01`, `2024-10`) for Flow-related types:

| API Field / Hook | Type | Verified Capability | Key Limitation |
|---|---|---|---|
| `flowTriggerReceive` | Mutation | Starts workflows that begin with an app's custom trigger handle using a JSON payload. | Only pushes inbound events into Flow; cannot inspect execution status. |
| `flowRun` / `workflowRuns` | Query | **NON-EXISTENT** | No top-level or nested query exists in the public schema to query historical runs or step traces. |
| `orders`, `customers`, etc. | Queries | Fetches current store resource data. | Reflects current state, not the historical state at the exact moment the Flow condition was evaluated. |

---

### Surface 3: Flow Extension Framework (`shopify.extension.toml`)
Shopify allows apps to extend Flow via two extension points:

#### 1. Custom Action Extension (`flow_action`)
- **Mechanism:** When a workflow reaches this action, Shopify's Flow engine sends an HTTP `POST` request with a JSON payload to the app's `runtime_url`.
- **Instrumentation Trap:**
  - An app only receives this payload **if all preceding conditions evaluated to True**.
  - If a condition evaluates to False or skips, the branch terminates, and **no HTTP call is made to the app**.
  - To monitor failures, a merchant would have to manually add a custom "Log Failure" action to every False branch—an unacceptable UX burden.

#### 2. Custom Trigger Extension (`flow_trigger`)
- **Mechanism:** Allows an app to define custom events that merchants can select as workflow triggers.
- **Limitation:** Does not provide any visibility into downstream workflow execution.

#### 3. Custom Condition Extension (`flow_condition`)
- **Mechanism:** **UNSUPPORTED BY SHOPIFY.**
- All condition evaluations (strings, numbers, regex, dates, tags) are executed inside Shopify's internal, proprietary DAG engine.

---

### Surface 4: Native `Get workflow run data` Action
Official documentation confirms that this action returns a single `getWorkflowRunData` object containing:

```json
{
  "id": "gid://shopify/FlowRun/123456789",
  "legacyResourceId": 123456789,
  "retryAttemptCount": 0,
  "startedAt": "2026-11-08T00:15:00Z",
  "triggerEvent": {
    "id": "gid://shopify/FlowTriggerEvent/987654321",
    "source": "shopify/order/created",
    "triggeredAt": "2026-11-08T00:14:59Z"
  },
  "url": "https://admin.shopify.com/store/{shop}/apps/flow/runs/123456789",
  "workflow": {
    "id": "gid://shopify/FlowWorkflow/555444333",
    "legacyResourceId": 555444333,
    "name": "High Value Fraud Check",
    "tags": ["fraud", "orders"],
    "url": "https://admin.shopify.com/store/{shop}/apps/flow/workflows/555444333",
    "version": 3
  }
}
```

#### What is Missing:
- ❌ **No step IDs or step names executed**
- ❌ **No condition outcomes (True / False)**
- ❌ **No error messages or reason for run termination**
- ❌ **No access to variable values at runtime**

---

## 🎯 Strategic Architectural Conclusion: Path A vs. Path B

| Evaluation Dimension | Path A: FlowLens Live Diagnostics | Path B: FlowLens Preflight Validator |
|---|---|---|
| **Core Value Prop** | Post-execution debugging of failed runs | Pre-activation linting, logic validation & simulation |
| **API Dependency** | Requires public `FlowRun` step-trace API | Requires workflow definition export (JSON) + Admin API read scopes |
| **Technical Feasibility** | **BLOCKED (0% API coverage)** without invasive manual workflow instrumentation | **FEASIBLE (100% supported)** via standard workflow export/import schemas |
| **User Effort Required** | High: Must modify flows to add custom tracking actions | Low: Upload/sync exported flow JSON before turning it on |
| **Merchant Pain Addressed** | Investigating silent failures after damage occurs | **Preventing** silent failures (ISO codes, missing tags, loops) before activation |

### Recommendation for Decision Gate:
Proceed with **Path B (Preflight Validator & Linter)** as the primary product candidate, while validating through Track A interviews whether preventing silent condition failures pre-activation addresses $\ge 80\%$ of merchant operational anxiety.
