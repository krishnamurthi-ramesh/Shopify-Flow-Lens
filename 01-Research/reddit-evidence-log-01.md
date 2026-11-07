# Reddit Evidence Log 01 — Shopify Flow and Sidekick

**Project:** Project-4-Shopify-Flow-AI  
**Research phase:** Phase 3 — Customer discovery  
**Method:** Passive review of public Reddit discussions  
**Status:** Initial desk-research signals; not validated customer findings  
**Research date:** 7 November 2026

## Purpose

Capture real-world merchant and developer accounts of Shopify Flow and Sidekick usage. The purpose is to identify problems worth investigating in interviews, not to prove a preselected product idea.

## Evidence standard

- Reddit posts and comments are self-reported anecdotes, not representative samples.
- Votes and comment counts do not establish prevalence or severity.
- Separate the author's experience from replies and from our interpretation.
- Do not infer merchant revenue, order volume, willingness to pay, retention, or time saved unless the author explicitly reports it.
- Treat a thread as one research source, not as multiple independent customers merely because it has multiple replies.

## Discussion log

### R1 — Flow run visibility and alerting

- **Source:** https://www.reddit.com/r/shopify/comments/1qx0sr5/are_there_any_apps_that_monitor_flow_workflows/
- **Date shown on Reddit:** 5 February 2026 (replies through 6 February 2026)
- **User context:** A Shopify Flow user reporting that scheduled workflows sometimes did not run as expected.
- **What the author reported:** They wanted an email alert when a scheduled workflow did not run, because they might otherwise discover the issue days later.
- **What replies added:** Replies mentioned Flow logs and a Flow failure trigger as possible ways to detect failures. Another reply distinguished visible failures from cases where a workflow appeared to run but did not produce the expected outcome.
- **Evidence-based signal:** Users may have difficulty distinguishing execution failure from outcome failure and may not notice problems quickly.
- **Product question:** Can a merchant tell whether a workflow completed the intended business outcome, not merely whether the workflow executed?
- **Caveat:** The thread itself contains a workaround suggestion; do not claim Shopify lacks failure notifications.

### R2 — Operational work away from the desktop

- **Source:** https://www.reddit.com/r/shopify/comments/1tskqb2/anyone_actually_using_sidekick_or_other_ai/
- **Date shown on Reddit:** 31 May 2026 (replies through 1 June 2026)
- **User context:** A seasonal fruit merchant in India describing frequent catalogue turnover and changing stock.
- **What the author reported:** Catalogue changes every 6–8 weeks; stock changes daily or hourly; the operator is often at warehouses rather than at a laptop. They wanted phone-based or messaging-based updates and recurring store audits.
- **What replies added:** One respondent said Sidekick felt slow and required repeated saves for product updates; others discussed custom MCP or agent approaches.
- **Evidence-based signal:** For at least one operationally mobile merchant, desktop-admin interaction and repeated update steps are friction points.
- **Product question:** Which operational tasks genuinely need to be completed away from the desktop, and what safeguards would be needed for messaging-based commands?
- **Caveat:** This is a specific seasonal business context; do not generalise to all Shopify merchants.

### R3 — Sidekick capability consistency and regression

- **Source:** https://www.reddit.com/r/shopify/comments/1v07vyp/did_sidekick_suddenly_get_extremely_dumb/
- **Date shown on Reddit:** 18 July 2026
- **User context:** A merchant who had regularly used Sidekick for abandoned-checkout and cart reporting.
- **What the author reported:** Requests that previously worked began returning capability limitations, leading the user to fall back to manual reporting or Support.
- **Evidence-based signal:** Changes in perceived capability or inconsistent availability can undermine trust in an assistant used for recurring operational tasks.
- **Product question:** How should an AI assistant communicate capability changes, uncertainty, permissions, and fallback paths?
- **Caveat:** A single account cannot establish a product-wide regression or its cause.

### R4 — Complex queries, context and correctness

- **Source:** https://www.reddit.com/r/shopify/comments/1rbtguj/shopify_sidekick/
- **Date shown on Reddit:** February 2026
- **User context:** A merchant/operator discussing analytics, shipping troubleshooting, custom Liquid code and reporting.
- **What the author reported:** Sidekick was useful for some reporting and troubleshooting tasks, but a complex query requiring critical metafields reportedly produced inconsistent output despite repeated auditing.
- **What a reply reported:** Another user described a tax-reporting task that missed a substantial amount and said an attempted storefront change was not aligned with the brand, though it could be undone.
- **Evidence-based signal:** The same assistant can be useful for some tasks while unreliable for others; data completeness and action scope matter.
- **Product question:** How can the product show which data sources and fields informed an answer, and require confirmation before consequential changes?
- **Caveat:** The financial amount and incident are individual claims, not independently verified.

### R5 — Integration/API coverage and workflow creation friction

- **Source:** https://www.reddit.com/r/shopify/comments/1xdf6w5/how_to_connect_shopify_flow_shopify_messaging/
- **Date shown on Reddit:** 23 October 2026
- **User context:** A merchant using Shopify Messaging and Flow, asking about Claude/ChatGPT connectivity.
- **What the author reported:** They said their connector did not expose Flow workflows or Messaging templates, leaving manual work. They also described Sidekick not appearing in their Messaging workflow.
- **Evidence-based signal:** Users may encounter capability boundaries between AI assistants, native Shopify tools and third-party connectors.
- **Product question:** Which entities and actions need to be exposed through supported integrations, and how should product limitations be made discoverable?
- **Caveat:** Treat API coverage as the author's reported experience; validate against current official API documentation before making a technical claim.

### R6 — Positive Flow use cases and existing value

- **Source:** https://www.reddit.com/r/shopify/comments/1vez8da/shopify_flow_stopped_being_plus_only_a_while_back/
- **Date shown on Reddit:** 4 August 2026
- **User context:** Merchants discussing practical Flow use cases.
- **What users reported:** Inventory-based product or collection handling and customer tagging based on order count.
- **Evidence-based signal:** Flow already supports useful operational automations; the product opportunity should not assume the existing builder is universally unusable.
- **Product question:** Which use cases are easy enough for merchants to configure today, and where does complexity begin?
- **Caveat:** Positive examples are important counter-evidence and should be included in the teardown.

### R7 — App developer experience around Sidekick placement

- **Source:** https://www.reddit.com/r/shopifyDev/comments/1wq2otm/is_sidekick_hurting_shopify_embedded_app/
- **Date shown on Reddit:** 25 September 2026
- **User context:** A Shopify app developer discussing the Sidekick prompt's position in Admin.
- **What the author reported:** They felt the prompt competed with their app's own UI and reduced space for app entry points.
- **Evidence-based signal:** AI assistant surfaces can affect the discoverability and usability of third-party app experiences.
- **Product question:** How should Shopify balance a native AI assistant with the discoverability of partner apps?
- **Caveat:** This is developer ecosystem feedback, not direct merchant evidence. Keep it in a separate segment.

## Initial synthesis — hypotheses to test, not conclusions

1. **Observability:** Merchants may need clearer signals for missed outcomes, not just execution errors.
2. **Capability transparency:** Users may need more explicit explanations of what Sidekick can currently access or change, and a reliable fallback when it cannot.
3. **Context and data grounding:** Complex workflows and reports may require visibility into the data fields, apps and context used by the assistant.
4. **Mobile and operational context:** Some merchants work away from a desktop and may value safe, constrained operations from mobile or messaging interfaces.
5. **Integration boundaries:** Gaps between Shopify-native capabilities and third-party assistants may create manual work.
6. **Preserve existing strengths:** Flow has useful native use cases; the opportunity may be improving specific difficult moments rather than replacing Flow wholesale.

## What this evidence does not establish

- It does not validate the proposed $50k–$2M GMV beachhead.
- It does not establish how common any pain point is.
- It does not establish a 10–15 hour weekly time loss.
- It does not establish willingness to pay.
- It does not prove that AI workflow generation is the highest-value solution.
- It does not prove that Shopify Flow lacks testing, failure notifications, or conversational editing.

## Next research actions

1. Review at least 20 additional relevant threads, deliberately including positive Flow experiences and non-AI alternatives.
2. Code each thread by user type, workflow, trigger, reported friction, workaround, consequence, frequency (only if stated), and confidence.
3. Identify repeated patterns across independent authors, not just comments within the same thread.
4. Use neutral interview questions to ask merchants to walk through their most recent real workflow failure or automation setup.
5. Verify all product/API capability claims against current Shopify documentation before incorporating them into the product requirements.

## Preliminary interview prompts derived from these signals

- Tell me about the most recent time an automation did not produce the result you expected. How did you discover it?
- What did the workflow do, what did you expect it to do, and what did you do next?
- Which parts of the setup did you configure yourself, and where did you need help?
- Which Shopify or third-party systems were involved?
- How do you currently test changes before activation?
- What tasks do you need to complete when you are away from a computer?
- What would make you trust an assistant to take an action, and which actions should always require approval?

