# Shopify Flow — Technical Evidence Caveat & API Boundaries (Updated Nov 2026)

**Purpose:** Maintain absolute technical accuracy regarding Shopify Flow capabilities, API access surfaces, and `.flow` schemas.

---

## 1. What Official Shopify Documentation Confirms (Verified Nov 2026)

### A. Native Workflow Testing & Run History
- **Pre-Activation Testing Exists:** Shopify Flow natively supports workflow testing using recorded store events, simulated events using real store data, and Sidekick-generated simulated events. Test runs execute logic paths and render previews without mutating live store data. However, external-service actions cannot always be simulated, and incomplete steps cannot be tested.
- **Run History:** Completed workflow runs are retained for 14 days in Shopify Admin. Staff with permissions can view the DAG visual path, step results, execution logs, and step data snapshots.
- **Defensible Framing:** FlowLens does not claim that Shopify lacks testing or run history. Rather, FlowLens addresses **pre-activation configuration risks, schema mismatches, and unhandled external action coverage** before a workflow is published.

### B. Wait Action & Data Freshness Semantics
- **Store Data IS Refreshed:** When a `Wait` action concludes, Shopify Flow automatically refreshes top-level store data (e.g., Order, Customer) before continuing.
- **The True Limitation (Rule L-02):** Outputs from `Get`, `Sum`, or `Count` data actions executed *before* a Wait step are **discarded and unavailable after the Wait step**. If downstream actions or conditions attempt to reference pre-Wait `Get data` variables, the step fails or lacks data. A repeat `Get data` action must be added after the Wait action.

### C. Condition Branching Semantics
- **No-Action Branches Are Often Intentional:** A condition branch terminating without an action (no-op) is explicitly documented by Shopify as standard behavior when a condition is unmet. FlowLens must treat unhandled False branches as **informational notes**, not fatal errors.

### D. The October 28, 2026 Shopify API Announcement
- Shopify announced automation management capabilities through its AI Toolkit:
  - `automationWorkflow` and `automationWorkflows` queries in the unstable GraphQL Admin API return workflow step DAGs, links, custom fields, and validation errors.
  - `automationFlowFileParse` parses an exported `.flow` file (including checksum verification) into a JSON automation configuration without saving the workflow.
- **Access Restrictions:** These APIs require the `write_automations` scope. Shopify explicitly restricts third-party apps from directly using these underlying APIs without direct Shopify approval.
- **Implication:** Third-party programmatic API access remains gated. However, a **merchant-controlled `.flow` file upload and preflight analysis tool** is fully aligned with Shopify's supported architecture.

---

## 2. Access Surface Capability Matrix

| Access Surface | Official Capability | Diagnostic Limitation | Feasibility Finding |
|---|---|---|---|
| **Shopify Admin UI** | DAG execution paths, step-level condition states, 14-day logs. | Sandboxed server-side for authenticated staff. | Inaccessible to third-party apps programmatically. |
| **GraphQL Admin API (Public)** | `flowTriggerReceive` mutation to trigger workflows. | No public query exists for historical `FlowRun` step execution traces. | Blocks live post-execution external monitoring. |
| **Automation Admin API (Unstable / Oct 28)** | `automationWorkflow`, `automationFlowFileParse`. | Gated behind `write_automations`; third-party access restricted. | Validates `.flow` parsing architecture, but requires file-upload mode for third-party tools. |
| **Flow Extensions** | `flow_action` and `flow_trigger`. | No `flow_condition` extension exists. Custom actions only run if conditions pass. | Cannot observe condition evaluations or failed branches. |

---

## 3. Recommended Project Wording

> "Shopify natively provides step-level run history and pre-activation testing (using recorded, store, or Sidekick events). On October 28, 2026, Shopify announced unstable automation APIs (including `automationFlowFileParse`), but restricts direct third-party access. Furthermore, no public API exposes live step-level condition execution traces. FlowLens is therefore designed as an offline, read-only preflight analyzer that inspects merchant-exported `.flow` schemas to detect schema-aware configuration risks, pre-Wait data action output loss, and recursion hazards before activation."
