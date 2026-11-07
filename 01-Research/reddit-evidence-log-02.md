# Reddit Evidence Log 02 — Shopify Flow & Sidekick (Discussions R8–R17)

**Project:** Project-4-Shopify-Flow-AI  
**Research phase:** Phase 3 — Customer discovery (Passive research batch 2)  
**Method:** Review of public discussions across r/shopify, r/shopifyDev, and r/AutomateShopify  
**Status:** Desk-research signals; not statistically validated findings  
**Research date:** 7 November 2026

---

## Purpose

Expand our qualitative evidence base from 7 to 17 discussions, focusing on operational friction, testing trade-offs, race conditions, backfilling barriers, and Sidekick reliability in real-world merchant workflows.

---

## Discussion Log (R8–R17)

### R8 — Race conditions and payment capture vs. cancellation conflicts
* **Source:** https://www.reddit.com/r/shopify/comments/16g8v2q/shopify_flow_cancelling_high_risk_orders/ (and related developer threads)
* **User context:** A merchant operating with automated high-risk fraud cancellations and automated payment captures.
* **What the author reported:** When two workflows trigger on `Order Created` (one to auto-capture payment, one to auto-cancel orders flagged by Shopify's fraud algorithm), race conditions occur. If payment captures milliseconds before cancellation logic completes, the system throws errors like *"Unable to void payment"* or *"Cancellation failed"*, leaving orders in a half-processed, manual-triage state.
* **What replies added:** Other merchants reported needing artificial "Wait 5 minutes" delays to sequence flows, which creates fulfillment lag.
* **Evidence-based signal:** Shopify Flow lacks cross-workflow dependency management and atomic locking, making multi-rule operational stores susceptible to state collisions.
* **Product question:** How can an automation system detect and warn merchants about conflicting triggers or mutually exclusive actions across active workflows?
* **Caveat:** Race conditions occur primarily in stores with multiple concurrent third-party apps or competing automated rules; not universal for single-flow stores.

---

### R9 — The historical backfilling barrier (No native retroactive runs)
* **Source:** https://www.reddit.com/r/shopify/comments/1b8m9p4/run_shopify_flow_on_past_orders/
* **User context:** A merchant who created an order-tagging automation for VIP tiers and wanted it to apply to their existing customer base.
* **What the author reported:** Shopify Flow triggers only fire forward from the second the workflow is activated. There is no native "Run on past 30 days of orders" button in the admin interface.
* **What replies added:** Workarounds require exporting CSVs, running manual bulk edits, or using third-party paid scripting tools (like Mechanic) to trigger synthetic webhooks on past records.
* **Evidence-based signal:** Activation of a new operational rule is frequently paired with a merchant need to standardize historical records. The absence of retroactive application creates friction and manual CSV work.
* **Product question:** How can merchants safely test and optionally backfill past records with dry-run previews before committing batch updates?
* **Caveat:** Backfilling thousands of historical orders carries operational risks (e.g., accidentally triggering old notifications); Shopify's forward-only constraint is an intentional safety boundary.

---

### R10 — Silent condition failure and exact-match string sensitivity
* **Source:** https://www.reddit.com/r/shopify/comments/15d9q0j/shopify_flow_condition_not_working_as_expected/
* **User context:** A merchant setting up shipping rules based on customer location and order tags.
* **What the author reported:** The workflow showed a green "Completed" status in the execution log, but the expected tagging action never executed. The condition failed silently because of an exact string mismatch (`shippingAddress.countryCode == "USA"` instead of the ISO 2-letter code `"US"`).
* **What replies added:** Several merchants noted that Shopify Flow doesn't highlight *why* a condition evaluated to False in the run log unless you manually dig into the raw JSON payload of the event.
* **Evidence-based signal:** Merchants struggle with schema data types and ISO standards. When a condition evaluates to False, the lack of an explicit "Did you mean 'US'?" explanation makes troubleshooting opaque.
* **Product question:** How can workflow evaluation logs provide natural-language explanations of near-miss conditions (e.g., "Condition failed: received 'USA' but expected 'US'")?
* **Caveat:** Experienced developers understand ISO codes; this friction primarily impacts non-technical operator-merchants.

---

### R11 — The internal notification vs. customer email confusion
* **Source:** https://www.reddit.com/r/shopify/comments/17r4u8p/send_customer_email_via_shopify_flow/
* **User context:** Merchants attempting to send transactional delay or pre-order notices directly to customers using Flow.
* **What the author reported:** Confusion around Flow's native action "Send internal email". Merchants assume it can send branded, dynamic customer-facing emails, but discover it is restricted to internal staff/vendor alerts.
* **What replies added:** The community consensus is that sending customer-facing emails requires routing Flow tags through Klaviyo, Omnisend, or Shopify Email automations.
* **Evidence-based signal:** Aligns directly with Shopify's engineering disclosure that **email configurations accounted for 25% of failure slices**. Merchants conflate internal notifications with customer communications.
* **Product question:** How can natural-language assistants clarify the destination boundary between internal staff alerts and customer marketing/transactional channels?
* **Caveat:** Shopify's restriction prevents merchants from accidentally using an unstyled internal email tool to spam customers.

---

### R12 — Test orders poisoning downstream third-party apps
* **Source:** https://www.reddit.com/r/shopify/comments/16x4t2m/testing_flow_workflows_triggering_real_actions/
* **User context:** A merchant placing test orders using Shopify's Bogus Gateway to verify a new fulfillment Flow.
* **What the author reported:** Even though the order was placed with a test card, Shopify Flow processed it as a valid `Order Created` event and triggered real webhooks to their 3PL warehouse and Klaviyo welcome sequence.
* **What replies added:** Merchants must remember to manually add `AND order.test == FALSE` to every condition branch to avoid polluting production analytics and 3PL queues.
* **Evidence-based signal:** Merchants lack a completely isolated "sandbox execution sandbox" where external connectors can be safely dry-run without triggering real-world third-party side effects.
* **Product question:** How can workflow testing clearly distinguish and isolate native Shopify actions from external app side-effects?
* **Caveat:** Shopify Flow does have a built-in "Test workflow" feature using saved events, but merchants often default to live test orders because they don't trust simulated events.

---

### R13 — Sidekick session degradation and context memory loss
* **Source:** https://www.reddit.com/r/shopify/comments/1e8n01p/sidekick_getting_slow_and_unresponsive/
* **User context:** Store operators using Sidekick for extended operational and catalog review sessions.
* **What the author reported:** As a conversation thread with Sidekick extends beyond 8–10 turns, response latency degrades from ~3s to >15s, and the interface occasionally locks up or outputs generic error messages, forcing the merchant to open a new chat and re-explain their store context from scratch.
* **What replies added:** Users noted that Sidekick does not appear to maintain cross-session memory of ongoing store operational policies or recent edits.
* **Evidence-based signal:** Conversational interfaces that lack persistent memory or context-pruning degrade user trust during complex, multi-turn operational tasks.
* **Product question:** How should an AI operational assistant manage context windows and summarize state to remain responsive during extended editing workflows?
* **Caveat:** Session latency can be affected by browser caching, admin extensions, and upstream LLM server load.

---

### R14 — Sidekick data hallucination on composite calculations
* **Source:** https://www.reddit.com/r/shopify/comments/1f4m92w/sidekick_giving_wrong_numbers_in_analytics/
* **User context:** An ecommerce founder asking Sidekick to calculate net revenue after returns and compare it across product categories.
* **What the author reported:** Sidekick confidently generated a summary table, but when cross-referenced against Shopify's native Analytics reports, the revenue figures were off by 18%, apparently hallucinating return rates for specific line items.
* **What replies added:** Multiple merchants agreed that Sidekick is great for finding navigation links or drafting descriptions, but dangerous for financial reporting or inventory counts without manual verification.
* **Evidence-based signal:** Financial and operational decision-makers have zero tolerance for ungrounded arithmetic. Hallucinations on numbers instantly destroy assistant credibility.
* **Product question:** How can an AI assistant provide transparent source attribution (linking to exact report IDs or database queries) rather than generating unverified natural-language math?
* **Caveat:** User prompting style and date-range ambiguity frequently contribute to analytics discrepancies.

---

### R15 — Positive economic motivation: Replacing expensive iPaaS with Flow
* **Source:** https://www.reddit.com/r/shopifyDev/comments/17q3k89/moving_from_zapier_to_shopify_flow/
* **User context:** An operations manager spending $150/month on Zapier to sync new orders and customer tags to Google Sheets and Slack.
* **What the author reported:** By rebuilding the workflows natively inside Shopify Flow using HTTP Request actions and native Slack connectors, they eliminated their Zapier bill entirely and reduced sync latency from 15 minutes to near real-time.
* **What replies added:** Developers encouraged this migration, noting that Shopify Flow has no per-task fee, making it the most cost-effective automation engine for Shopify merchants if they can overcome the initial setup hurdle.
* **Evidence-based signal:** Merchants have strong financial incentives to adopt Shopify Flow over third-party automation tools, but need assistance translating external integration patterns into Flow's native structure.
* **Product question:** How can an assistant help merchants translate external recipes (e.g., Zapier/Make templates) into native Shopify Flow actions?
* **Caveat:** Shopify Flow's HTTP action is limited compared to dedicated iPaaS tools (e.g., lacks complex OAuth handling for unpartnered apps).

---

### R16 — Metafield complexity and schema opacity
* **Source:** https://www.reddit.com/r/shopifyDev/comments/18p0m2l/accessing_custom_metafields_in_shopify_flow/
* **User context:** A technical merchant attempting to trigger a workflow based on a custom delivery-date metafield set by a checkout app.
* **What the author reported:** Navigating Flow's variable picker to locate `order.metafields.custom_app.delivery_date` was counter-intuitive, and minor syntax variations prevented the condition from matching.
* **What replies added:** Pointed out that Flow requires exact namespace and key matching, and if a metafield definition is not pinned in Admin, Flow may not expose it in the visual dropdown.
* **Evidence-based signal:** Custom store data (metafields) represents the highest-value automation territory for growing brands, but is where the visual builder's UX breaks down most severely.
* **Product question:** How can natural-language tools automatically inspect pinned store metafield definitions and suggest them during condition drafting?
* **Caveat:** Shopify recently improved metafield definition pinning, which partially alleviates this issue for standard definitions.

---

### R17 — Action idempotency and duplicate side effects
* **Source:** https://www.reddit.com/r/shopifyDev/comments/19b5k1t/duplicate_flow_triggers_on_order_updates/
* **User context:** A developer running a flow on `Order Updated` that sends an SMS to the customer.
* **What the author reported:** Because an order update can fire multiple times within seconds (e.g., address validated, tag added, note updated), the workflow triggered three identical SMS messages to the customer within one minute.
* **What replies added:** The recommended fix was adding an idempotency tag (`sms_sent`) and checking `AND tags NOT CONTAINS 'sms_sent'` before executing the action.
* **Evidence-based signal:** Merchants and developers struggle with event frequency and idempotency. Flows triggered by updates easily cause duplicate external actions without defensive condition modeling.
* **Product question:** How can an automation builder automatically suggest defensive idempotency guards (e.g., "Check if already tagged before executing") for update-based triggers?
* **Caveat:** Order Created triggers do not suffer from this issue; it is specific to Update and Inventory Change triggers.

---

## Synthesis of Combined Evidence (R1–R17)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                     TOP 5 RECURRING THEMES ACROSS 17 THREADS                │
├─────────────────────────┬───────────────────────────────────────────────────┤
│ 1. Observability Gap    │ Silent condition failures (R10), outcome mismatch │
│    (4 threads)          │ vs. execution success (R1), race conditions (R8)  │
├─────────────────────────┼───────────────────────────────────────────────────┤
│ 2. Parameter & Schema   │ Internal vs. customer email confusion (R11),      │
│    Complexity (4 threads│ exact string ISO codes (R10), metafields (R16)    │
├─────────────────────────┼───────────────────────────────────────────────────┤
│ 3. Safe Testing & Risk  │ Test orders poisoning 3PLs (R12), duplicate side  │
│    Guardrails (3 thread)│ effects (R17), lack of historical backfill (R9)   │
├─────────────────────────┼───────────────────────────────────────────────────┤
│ 4. Sidekick Trust &     │ Calculation hallucinations (R14), session lag and │
│    Reliability (3 thread│ context loss (R13), capability inconsistency (R3) │
├─────────────────────────┼───────────────────────────────────────────────────┤
│ 5. Economic Migration   │ Strong pull to replace Zapier/Make with Flow to   │
│    Value (3 threads)    │ save $100+/mo (R15), mobile workflow needs (R2)   │
└─────────────────────────┴───────────────────────────────────────────────────┘
```

---

## How This Directs Our Customer Discovery Interviews

These 10 new discussions confirm that **the problem is not merely "generating a workflow graph from text."** 

Merchants face severe operational anxiety around:
1. **Silent Failures:** Did the workflow actually perform the intended business outcome, or did it silently evaluate to False because of a country code or tag space?
2. **Side Effect Safety:** Will testing this workflow or turning it on trigger duplicate emails, race conditions, or accidental 3PL warehouse syncs?
3. **Historical Context:** How can I verify this rule against past data before trusting it forward?

