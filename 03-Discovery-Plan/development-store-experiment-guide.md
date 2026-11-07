# Development Store Feasibility Experiment Guide: Shopify Flow Diagnostics

**Project:** Shopify Flow + Sidekick AI Teardown & Opportunity Validation  
**Deliverable:** Technical Feasibility Spike — Deliverable 2 (Track B)  
**Status:** Ready to execute on a Shopify Development Store  
**Target:** Validate whether any undocumented API endpoint or custom action workaround enables Path A (Live Diagnostics), or formally lock Path B (Preflight Validator).

---

## 🛠️ Step 0: Development Environment Setup

If you don't already have an active development store:
1. Log in to [Shopify Partners](https://partners.shopify.com/).
2. Navigate to **Stores** $\rightarrow$ **Add store** $\rightarrow$ **Create development store**.
3. Select **Create a store for testing and building** (with test data enabled).
4. Install **Shopify Flow** (free native app from Shopify App Store).
5. Generate an Admin API Access Token via **Settings** $\rightarrow$ **Apps and sales channels** $\rightarrow$ **Develop apps** (enable `read_orders`, `write_orders`, `read_products`).

---

## 🧪 The 7-Step Feasibility Experiment Protocol

### Step 1: Create the Benchmark Workflow in Shopify Flow
Create an automation with a branching DAG structure designed to test condition boundaries:
- **Trigger:** `Order created`
- **Condition 1:** `Order / totalPriceSet / shopMoney / amount` $\ge 100.00$
- **Branch A (True):**
  - **Condition 2:** `Order / shippingAddress / countryCode` == `"US"`
  - **Branch A1 (True):** Action: `Add order tags` $\rightarrow$ `"VIP-DOMESTIC"`
  - **Branch A2 (False):** Action: `Add order tags` $\rightarrow$ `"VIP-INTERNATIONAL"`
- **Branch B (False):**
  - Action: `Add order tags` $\rightarrow$ `"STANDARD-ORDER"`

---

### Step 2: Generate Controlled Test Events
Execute 3 distinct test scenarios using Shopify Flow's native test event generator:

| Test Run | Simulated Payload Attributes | Expected DAG Path | Intended Purpose |
|---|---|---|---|
| **Run 1 (Pass Both)** | Total: `$150.00`, Country: `"US"` | Trigger $\rightarrow$ Cond 1 (True) $\rightarrow$ Cond 2 (True) $\rightarrow$ Tag `VIP-DOMESTIC` | Baseline execution |
| **Run 2 (Fail Cond 2)** | Total: `$150.00`, Country: `"CA"` | Trigger $\rightarrow$ Cond 1 (True) $\rightarrow$ Cond 2 (False) $\rightarrow$ Tag `VIP-INTERNATIONAL` | Verifies false branch execution |
| **Run 3 (Semantic Error)** | Total: `$150.00`, Country: `"USA"` (3-letter string) | Trigger $\rightarrow$ Cond 1 (True) $\rightarrow$ Cond 2 (False) $\rightarrow$ Tag `VIP-INTERNATIONAL` | **Tests silent mismatch failure!** |

---

### Step 3: Inspect Native Flow Run & Test Interfaces
In Shopify Admin, open **Shopify Flow** $\rightarrow$ **Run history**:
1. Check how the native UI displays Run 3 (the semantic string mismatch).
2. **Observation Check:** Does the native UI explain *why* `"USA"` evaluated to False against `"US"`? (Expected: It shows `countryCode == 'US' -> False` with no semantic error hint).
3. Check retention notice: Confirm that the UI flags runs as expiring after 14 days.

---

### Step 4 & 5: Query Public GraphQL Admin API
Run the following GraphQL query against your dev store using cURL, Postman, or the bundled Node.js script:

```graphql
# Test 1: Querying for FlowRun schema or types
query IntrospectFlowTypes {
  __schema {
    types {
      name
      description
    }
  }
}
```

#### Verification Query:
Attempt to query any workflow run or execution history:
```graphql
# Test 2: Attempting to query FlowRun (Expected: Field does not exist)
query CheckFlowRunAccess {
  order(id: "gid://shopify/Order/<YOUR_ORDER_ID>") {
    id
    name
    tags
    # Note: No 'flowRuns' connection exists on Order or Root Query
  }
}
```

---

### Step 6: Record Inaccessible Information Ledger

Document the exact access barriers encountered:

| Data Element | Required for Path A? | Available in Admin UI? | Available in GraphQL API? | Inaccessibility Classification |
|---|---|---|---|---|
| Workflow Run ID | Yes | Yes | No (Only inside Flow action) | **API Blocked** |
| Condition 1 Result (`True`) | Yes | Yes (Visually) | No | **API Blocked** |
| Condition 2 Result (`False`) | Yes | Yes (Visually) | No | **API Blocked** |
| Evaluated Field Snapshot (`countryCode: "USA"`) | Yes | Yes (Step data) | No | **API Blocked** |
| Run Error / Failure Trace | Yes | Partial (Logs) | No | **API Blocked** |

---

### Step 7: Test Custom Flow Action Instrumentation Limit
Test whether an app could add a custom `flow_action` to capture run state:
1. If a custom action is added *after* Condition 2, does it execute on Run 2 or Run 3?
   - **Result:** No. Condition 2 evaluated to False, so the True branch was skipped. The action never fired.
2. What would be required to capture failures?
   - The merchant would have to add an explicit "Log Failure" custom action to every single False branch in every workflow.
   - **Conclusion:** Unviable for merchant adoption.

---

## 💻 Automated Test Script (`scratch/test-flow-api.js`)

You can run this verification script in 10 seconds to confirm API schema limits:

```javascript
// scratch/test-flow-api.js
// Run: node scratch/test-flow-api.js <SHOP_DOMAIN> <ADMIN_API_TOKEN>

const shop = process.argv[2];
const token = process.argv[3];

if (!shop || !token) {
  console.log("Usage: node test-flow-api.js <shop-name>.myshopify.com <access-token>");
  process.exit(1);
}

const query = `
query IntrospectFlow {
  __schema {
    queryType {
      fields {
        name
      }
    }
    mutationType {
      fields {
        name
      }
    }
  }
}
`;

async function testApi() {
  const url = `https://${shop}/admin/api/2025-01/graphql.json`;
  const res = await fetch(url, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-Shopify-Access-Token': token
    },
    body: JSON.stringify({ query })
  });

  const data = await res.json();
  const queryFields = data.data.__schema.queryType.fields.map(f => f.name);
  const mutationFields = data.data.__schema.mutationType.fields.map(f => f.name);

  const flowQueries = queryFields.filter(name => name.toLowerCase().includes('flow') || name.toLowerCase().includes('workflow'));
  const flowMutations = mutationFields.filter(name => name.toLowerCase().includes('flow') || name.toLowerCase().includes('workflow'));

  console.log("=== SHOPIFY FLOW GRAPHQL API AUDIT RESULTS ===");
  console.log("Flow-related Query Endpoints:", flowQueries.length > 0 ? flowQueries : "NONE FOUND (0 queries)");
  console.log("Flow-related Mutation Endpoints:", flowMutations);
  console.log("===============================================");
}

testApi().catch(console.error);
```

