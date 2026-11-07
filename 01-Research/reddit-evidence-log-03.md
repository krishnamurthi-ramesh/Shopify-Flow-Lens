# Reddit Evidence Log 03 — Shopify Flow & Sidekick (Discussions R18–R25)

**Project:** Project-4-Shopify-Flow-AI  
**Research phase:** Phase 3 — Customer discovery (Passive research batch 3)  
**Method:** Review of public discussions across r/shopify, r/shopifyDev, and r/AutomateShopify  
**Status:** Desk-research signals; not statistically validated findings  
**Research date:** 7 November 2026

---

## Purpose

Complete our initial 25-discussion qualitative target (R1–R25) by examining app connectors (Klaviyo, ShipStation, Gorgias), template ceilings, rate limits, and time-based delay constraints.

---

## Discussion Log (R18–R25)

### R18 — Klaviyo marketing consent blocks in automated Flow emails
* **Source:** https://www.reddit.com/r/shopify/comments/18k5x2a/klaviyo_flow_email_consent_error/
* **User context:** A merchant attempting to trigger custom abandoned-cart and post-purchase follow-up emails via Flow.
* **What the author reported:** Workflows failed to execute customer-facing emails, throwing errors because the recipient customer had not opted into email marketing consent.
* **What replies added:** Flow automations respect strict compliance policies. Merchants cannot simply "send an email" to an order's email address if it is promotional; they must distinguish transactional notifications from marketing flows.
* **Evidence-based signal:** Merchants do not understand the legal/consent boundary between transactional store alerts and marketing flows. Automated flow generators that draft email actions without checking consent flags generate broken automations.
* **Product question:** How can an AI assistant automatically inspect customer marketing consent requirements before generating an outbound customer email step?
* **Caveat:** Consent laws (GDPR, CAN-SPAM) are external regulatory requirements; Shopify's enforcement is legally necessary.

---

### R19 — ShipStation connector scope breaks and item quantity mismatches
* **Source:** https://www.reddit.com/r/shopify/comments/191l3qm/shipstation_flow_connector_scope_error/
* **User context:** An operations team using Flow to route orders to ShipStation based on shipping method and weight.
* **What the author reported:** After a Shopify admin update, the connector broke with `invalid_scope: The access scope is invalid`. Orders began queuing without syncing to the warehouse. Additionally, line items occasionally mapped with quantity "0" for bundle products.
* **What replies added:** The workaround was uninstalling and re-authorizing the ShipStation app, but in the meantime, 20+ orders were missed and had to be manually entered.
* **Evidence-based signal:** External third-party app connectors are fragile. When an app's OAuth scopes expire or API changes, Flow workflows fail silently or halt downstream fulfillment.
* **Product question:** How can Flow detect connector health and warn merchants *before* orders fail to sync?
* **Caveat:** Third-party app outages or scope changes are partly outside Shopify's direct control.

---

### R20 — Gorgias customer support ticket payload syntax crashes
* **Source:** https://www.reddit.com/r/shopifyDev/comments/17y7u8a/gorgias_flow_action_liquid_syntax_error/
* **User context:** A store automating customer return tickets: when a return request is submitted, Flow creates a ticket in Gorgias.
* **What the author reported:** Attempting to pass dynamic return item details (`{{ returnLineItems }}`) into the ticket body resulted in Liquid parsing syntax errors, causing the action to fail.
* **What replies added:** Liquid arrays in Flow require specific `for` loops or property indexing; raw object tags fail to serialize.
* **Evidence-based signal:** Liquid templating inside connector action payloads is a major technical hurdle for non-developers.
* **Product question:** How can natural-language flow builders validate and auto-format Liquid syntax in connector action payloads?
* **Caveat:** Experienced developers can debug Liquid, but non-technical merchants are blocked.

---

### R21 — Sold-out collection hiding and multi-location inventory quirks
* **Source:** https://www.reddit.com/r/shopify/comments/1ap0v4l/automatically_hide_out_of_stock_products_flow/
* **User context:** A high-SKU merchant using Flow to unpublish products when inventory drops to zero.
* **What the author reported:** The rule worked for simple products, but failed on multi-variant and multi-location inventory (e.g., if one warehouse is at 0 but another has 2 units, or when a draft product is created).
* **What replies added:** Flow conditions require checking `inventoryTotalQuantity` across all locations or looping through variants, which makes the condition tree unexpectedly complex.
* **Evidence-based signal:** Standard inventory tasks appear deceptively simple, but store multi-location structures quickly overwhelm basic conditional logic.
* **Product question:** How can an assistant recognize multi-location inventory architecture and configure compound checks automatically?
* **Caveat:** Single-location stores do not experience this friction.

---

### R22 — The "Template Ceiling": Where pre-built templates break down
* **Source:** https://www.reddit.com/r/shopify/comments/1bq7k2m/shopify_flow_templates_vs_custom/
* **User context:** An apparel merchant sharing their experience with Shopify Flow's pre-built template library.
* **What the author reported:** Templates are great to get started (e.g. "Tag orders over $100"), but the moment you need a slight variation (e.g. "Only if the customer has ordered before AND has no discount code"), modifying the template requires understanding schema hierarchies, leading to frustration.
* **What replies added:** Many merchants abandon Flow when they hit this "template ceiling" because transitioning from a template to custom branching feels like a leap into programming.
* **Evidence-based signal:** The primary friction is not creating standard flows (templates solve that); it is **adapting, extending, and editing existing logic**.
* **Product question:** How can conversational tools make *editing* an existing flow as intuitive as creating a new one?
* **Caveat:** Some merchants hire Shopify Experts once they outgrow templates.

---

### R23 — Shopify API rate-limiting during high-volume automated flows
* **Source:** https://www.reddit.com/r/shopifyDev/comments/1c3k80a/shopify_flow_api_rate_limits_during_flash_sale/
* **User context:** A high-volume merchant running a Black Friday / flash sale with multiple active Flow automations.
* **What the author reported:** Workflows fetching deep variant data and customer histories hit Shopify API cost points, resulting in delayed order tag assignments (lagging by 40+ minutes) and race conditions on inventory locks.
* **What replies added:** Developers emphasized the need to batch queries and avoid complex nested data lookups on high-frequency triggers (`Order Created` during flash sales).
* **Evidence-based signal:** High-frequency events can degrade Flow performance; automations must be designed with API efficiency and rate limits in mind.
* **Product question:** Can an assistant estimate the operational load and API cost points of a workflow before deployment?
* **Caveat:** Primarily impacts merchants during extreme traffic spikes (>10 orders/second).

---

### R24 — Time-based delay & looping limitations
* **Source:** https://www.reddit.com/r/shopify/comments/1d7u14k/shopify_flow_wait_step_and_checking_status_later/
* **User context:** A merchant wanting an automation: *"Wait 3 days after order creation, then check if fulfillment status is still Unfulfilled, and if so, email our warehouse."*
* **What the author reported:** In Flow, using a "Wait" step evaluates the order data *as it was at trigger time* unless an explicit re-fetch query is executed, causing the flow to email the warehouse even if the order was fulfilled on day 2.
* **What replies added:** Merchants must use specific re-fetch actions or rely on external tools like Mechanic/MESA that re-query store state dynamically.
* **Evidence-based signal:** Temporal logic (checking state changes after a delay) violates merchants' intuitive mental models of how "Wait" works.
* **Product question:** How can time-delayed automations automatically incorporate state re-verification to prevent stale action execution?
* **Caveat:** Shopify has made incremental updates to Wait step re-queries, but merchant confusion persists.

---

### R25 — Sidekick fast drafting vs. opaque condition failure
* **Source:** https://www.reddit.com/r/shopify/comments/1eb6m10/using_sidekick_for_flow_automations/
* **User context:** A merchant testing Sidekick to draft a multi-condition order triage workflow.
* **What the author reported:** Sidekick drafted the initial workflow in seconds, but when tested, the condition evaluated to False. The preview did not explain *which* specific sub-clause failed or what data value caused the mismatch.
* **What replies added:** Merchants end up having to manually inspect the JSON event payload to figure out why the condition failed.
* **Evidence-based signal:** AI-generated flows require **interpretable condition diagnostics** during testing. Drafting is fast, but debugging is slow.
* **Product question:** How can testing tools provide visual, natural-language explanations of exact condition evaluation outcomes?
* **Caveat:** Sidekick's drafting capability was widely praised as a major improvement over starting from a blank canvas.

---

## 🎯 Final Synthesis of 25 Discussions (R1–R25)

With 25 discussions analyzed across 3 distinct subreddits, the qualitative picture is clear:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    THE 5 REAL PAIN POINTS OF SHOPIFY AUTOMATION             │
├────────────────────────────┬────────────────────────────────────────────────┤
│ 1. Outcome Observability   │ Workflows appear active/green, but silently    │
│    & Silent Failures       │ fail on string mismatches (R10), stale data    │
│    (6 discussions)         │ after delays (R24), or race conditions (R8)    │
├────────────────────────────┼────────────────────────────────────────────────┤
│ 2. The "Template Ceiling"  │ Easy to use pre-built templates, but modifying │
│    & Editing Friction      │ them or adding compound conditions is where    │
│    (5 discussions)         │ merchants hit a wall and abandon (R22, R25)    │
├────────────────────────────┼────────────────────────────────────────────────┤
│ 3. Connector & Scope       │ Broken scopes (R19), Liquid syntax crashes in  │
│    Fragility               │ payloads (R20), consent blocks (R18), test     │
│    (5 discussions)         │ orders poisoning 3PLs (R12)                    │
├────────────────────────────┼────────────────────────────────────────────────┤
│ 4. Historical Backfilling  │ Inability to run a new or edited rule on past  │
│    Barrier (3 discussions) │ orders without manual CSV exports (R9)         │
├────────────────────────────┼────────────────────────────────────────────────┤
│ 5. Sidekick Trust &        │ Great at initial drafting, but opaque on test  │
│    Debugging (6 discussion)│ failures (R25), math hallucinations (R14),     │
│                            │ and context memory loss in long chats (R13)    │
└────────────────────────────┴────────────────────────────────────────────────┘
```

### Strategic Takeaway for Our Opportunity Selection:
The real opportunity is **NOT** trying to build a better text-to-JSON generator than Shopify. 

The real opportunity is solving what happens **at the edges of creation**:
1. **Explainable Testing & Condition Diagnostics:** Showing exactly *which* condition sub-clause matched or failed on real data, with natural-language suggestions (e.g. "Did you mean 'US'?").
2. **Connector & Consent Pre-Flight Verification:** Checking app scopes and customer consent before generating broken email/webhook actions.
3. **Conversational Editing with Clear Diffs:** Helping merchants break through the "Template Ceiling" by editing existing workflows conversationally with visual before/after diffs.
