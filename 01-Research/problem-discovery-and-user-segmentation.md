# 01 — Problem Discovery, User Segmentation & JTBD

**Status:** Evidence-led first draft; customer discovery not yet conducted  
**Research cut-off:** 6 November 2026

## 1. Executive summary

Shopify Flow is a workflow automation product. Store owners compose workflows from triggers, conditions and actions. Sidekick can generate a workflow from a natural-language description. Shopify's engineering team disclosed that an internally fine-tuned tool-calling agent performed well on offline benchmarks but initially delivered a workflow activation rate 35% lower than the prompt-based agent in a 1% traffic deployment. Shopify attributed the gap to real merchant requests that its synthetic benchmark did not adequately cover, including editing existing workflows, email configuration, third-party integrations and informational questions about Flow.

The evidence does **not** establish that a defined merchant segment loses a particular number of hours, has a specific willingness to pay, or experiences a quantified level of fear or abandonment. Those are discovery questions, not established facts.

## 2. Evidence ledger — disclosed by Shopify

| ID | Disclosed fact | Source | What it supports | What it does not establish |
|---|---|---|---|---|
| E1 | Shopify Flow workflows use triggers, conditions and actions. | Engineering article; Help Center | Core workflow mental model. | That all merchants find this model difficult. |
| E2 | Sidekick can generate workflows from natural-language descriptions. | Engineering article; Help Center | Natural language is an alternative workflow-creation interface. | That natural language alone guarantees successful setup or adoption. |
| E3 | Shopify fine-tuned Qwen3-32B as a tool-calling agent for Flow generation. | Engineering article | Model and system implementation. | That model choice is universally best or independently reproducible from public information. |
| E4 | The fine-tuned approach was reported as 2.2x faster and 68% cheaper than the closed-model approach it replaced. | Engineering article | Shopify's reported latency and cost outcome for its own system. | Absolute cost, unit economics, or comparable costs for another company. |
| E5 | Shopify started from validated production workflows, generated plausible user queries and constructed multi-turn tool-call trajectories to create synthetic training data. | Engineering article | Training-data strategy and cold-start workaround. | That synthetic examples fully represent live merchant intent. |
| E6 | Offline evaluation used 300 handcrafted examples and assessed semantic correctness, syntactic correctness and latency. | Engineering article | Disclosed offline evaluation dimensions and benchmark size. | That these dimensions are sufficient measures of merchant value. |
| E7 | In a 1% traffic deployment, activation rate was 35% lower than the prompt-based agent. | Engineering article | A production signal showing an offline-to-online gap. | A 35% absolute abandonment rate or a 35% decline across all Sidekick users. |
| E8 | The live traffic exposed requests not covered adequately in synthetic data: editing existing workflows, email configuration, third-party integrations, and questions about Flow that did not intend to create a workflow. | Engineering article | Concrete coverage gaps observed in production. | That these are the only sources of failure or that each has equal impact. |
| E9 | Email workflows accounted for 25% of failures; diverse condition patterns accounted for 16%; workflow editing had not been represented in the synthetic data. | Engineering article | Failure-slice analysis reported by Shopify. | Denominators or a complete taxonomy of all failures beyond the article's description. |
| E10 | Shopify introduced an LLM judge that scores intent understanding, whether Flow is the right solution, component selection and clarity of next steps; it was calibrated against hundreds of human annotations. | Engineering article | Multi-dimensional evaluation and human calibration. | The precise judge accuracy or its full production threshold. |
| E11 | Shopify described a weekly loop: ingest production conversations, score them, route high-scoring examples to training, quarantine low-scoring examples, identify gaps and retrain. | Engineering article | Continuous product/model learning loop. | Exact model release cadence, annotation cost or measured business uplift from each iteration. |
| E12 | Shopify Help Center says Sidekick-generated workflows are inactive by default and must be reviewed and tested before being turned on. | Help Center | Human review and explicit activation are part of the documented path. | That users always review carefully or that this alone makes activation safe. |
| E13 | Shopify supports workflow testing using recorded events, manually created simulated events based on real store data, and Sidekick-generated simulated events. | Help Center | Testing already exists before activation. | That every possible production state or external action can be simulated. |
| E14 | Test mode does not make live store changes; incomplete/misconfigured steps cannot be used in a test; certain external-service actions cannot be simulated. | Help Center | Existing testing safeguards and boundaries. | That all risk can be detected before activation. |
| E15 | The workflow editor shows a Review banner for steps with missing required configuration; unresolved configuration errors prevent activation. | Help Center | Existing configuration validation. | That the current review UI is always understandable or sufficient for all merchants. |
| E16 | Sidekick can update the generated workflow through conversation; the merchant can Apply changes or Discard changes. | Help Center | Conversational refinement is documented. | That all edits or all integration-specific scenarios are supported. |
| E17 | Shopify Flow version history records version times, status changes, edit/activation events, users and issues such as missing configuration. Merchants can compare versions and restore an earlier version as a draft for review. | Help Center | Version history and restoration capabilities exist. | That users discover or effectively use these controls during incidents. |

## 3. Correct interpretation of the 35% activation result

**Shopify's wording:** In the 1% traffic test, the fine-tuned model's workflow activation rate was 35% lower than the prompt-based agent.

**Interpretation:** This is a relative comparison against a baseline, not evidence that 35% of generated workflows were abandoned. Shopify also described activation rate as noisy because it reflects merchant behaviour as well as model quality. The company kept activation as a guardrail while optimising a domain-expert-calibrated LLM judge.

**PM implication:** Do not use activation rate in isolation as a pure model-quality metric. Pair it with intent understanding, semantic correctness, configuration validity, workflow test outcomes, user corrections and downstream workflow outcomes.

## 4. User segmentation — provisional, not validated

The original GMV-based segments ($50k–$2M, $5M+, etc.), order-volume thresholds, headcount assumptions and willingness-to-pay claims are not established by the sources reviewed. We should not present them as facts.

Use behavioural and operational segmentation for discovery first:

| Provisional segment | Observable characteristics to investigate | Key unknowns |
|---|---|---|
| S1 — Low-complexity / early-stage merchant | Few recurring exceptions; owner handles operations directly; may use templates or manual admin work. | How frequently do exceptions occur? Is automation urgent enough to adopt? |
| S2 — Growing, operator-led merchant | Repeated order, fulfilment, customer or inventory tasks; small team; operational owner may configure tools. | Which workflows are frequent and costly? Who owns automation? What prevents use of Flow today? |
| S3 — Process-mature merchant | Multiple staff, apps or systems; established workflows; may have technical/agency support. | Is the need workflow creation, integration governance, maintenance or monitoring? |

### Candidate beachhead (hypothesis)

**S2 — growing, operator-led merchants** is a reasonable first segment to investigate because repeated tasks and limited specialist capacity could make simpler automation valuable. This is a research prioritisation hypothesis, not a proven market segment or a quantified TAM claim.

### Segmentation dimensions to collect

- Business operating model: D2C, marketplace-led, wholesale/B2B, omnichannel.
- Operational complexity: order volume bands, fulfilment locations, return/refund process, catalogue complexity.
- Team structure: owner-led, operations manager, specialist operations team, external agency.
- Tool ecosystem: Shopify-native only vs installed apps / 3PL / helpdesk / email platforms.
- Automation maturity: manual process, templates, Flow, external iPaaS, custom integration.
- Risk tolerance: read-only actions vs customer-facing, financial or inventory-changing actions.

Do not ask for sensitive business data unless needed; volume bands can be approximate and optional.

## 5. JTBD framing — candidate statements to validate

These are interview hypotheses, not verbatim customer quotes.

### Functional job

> When a recurring store event requires the same operational decision, help me configure a dependable workflow so the right action happens consistently without me manually checking each case.

### Emotional job

> Help me feel confident that the automation is configured correctly, that I understand what it will do, and that I can intervene if something looks wrong.

### Social job

> Help my team and customers experience the store as responsive and well-operated, even as operational complexity grows.

### Job context and desired outcomes

- Set up a workflow without needing to understand implementation details.
- Know when the requested workflow is unsupported or ambiguous.
- Supply required information without hunting through configuration screens.
- Understand which events and conditions will cause each action.
- Test both expected and edge cases before activation.
- Diagnose failures after activation and safely modify or pause the workflow.

## 6. Current alternatives — questions, not proven failure claims

| Alternative | What to investigate | Evidence required before claiming it fails |
|---|---|---|
| Manual Shopify Admin work | Repeated checking, copy/paste, hand-offs, missed exceptions. | Merchant examples, frequency, time diaries, error cases. |
| Manual Shopify Flow builder | Trigger/condition/action configuration, variable selection, app connector setup. | Usability observation, task completion time, error rates, user quotes. |
| Flow templates | Whether templates cover the merchant's actual process and required configuration. | Template discovery and modification study. |
| Sidekick natural-language generation | Intent classification, missing information, edits, test success, activation. | Observed sessions and funnel data, if accessible. |
| External automation tools / custom integrations | Capability, setup effort, reliability, governance and total cost. | Like-for-like workflow comparison and actual vendor pricing for the relevant plan. |

Do not claim that Zapier/Make necessarily require API knowledge, cost a particular amount, or move data outside Shopify without checking each integration and plan. Do not claim that a normal Flow takes 2–4 hours or that merchants lose 10–15 hours per week without primary research.

## 7. Problem statement — working draft

> Growing Shopify merchants who manage recurring operational exceptions need a reliable way to translate business rules into automations, understand what those automations will do, and correct incomplete or unsupported configurations before activation. Natural-language generation can reduce the initial creation burden, but successful use also depends on intent coverage, configuration completeness, integration support, useful testing and confidence in the resulting workflow.

This statement is intentionally narrower than the original draft. It does not claim a quantified time loss or a specific revenue band.

## 8. Testable hypotheses — revised

| ID | Hypothesis | Proposed validation | Success signal (to set after baseline) |
|---|---|---|---|
| H1 — Intent coverage | Some Sidekick workflow requests fail because the system does not correctly distinguish creation, editing, troubleshooting and informational intents. | Review anonymised examples if available; run task-based usability tests with representative merchant scenarios. | Correct intent classification and fewer unnecessary workflow-generation attempts. |
| H2 — Guided completion | Merchants may complete workflows more often when missing required fields are explained in context and the system asks for the needed value or offers a safe choice. | Compare current Review-banner flow with a guided-completion prototype. | Higher configuration completion and fewer unresolved required fields. |
| H3 — Test confidence | Existing testing is useful, but merchants may need more understandable coverage of edge cases and actions that cannot be simulated. | Observe users testing workflows; test an enhanced test summary prototype. | Improved comprehension of what was and was not tested; fewer activation mistakes in controlled tests. |
| H4 — Conversational refinement | Conversational edits may reduce friction for common changes, but users need a clear diff and reversible changes. | Compare conversation-only edits with conversation + visual diff + apply/discard. | Faster successful modification without higher error rates. |
| H5 — Integration readiness | Third-party app workflows may be difficult when an action is unavailable, uninstalled or incompletely configured. | Audit connector scenarios and conduct tasks involving installed and missing apps. | Fewer blocked workflows and clearer recovery paths. |

Targets such as “20% fewer abandonments” or “double activation” should not be fixed until a baseline, sample, measurement window and primary metric are agreed.

## 9. Discovery questions

1. Tell me about the last repetitive store task you wished could happen automatically.
2. What event starts the task, what decision do you make, and what action follows?
3. How often does this happen, and what is the consequence when it is missed or handled incorrectly?
4. Who performs the task today? Which tools or people are involved?
5. Have you tried Shopify Flow, templates, Sidekick or another automation product? What happened?
6. Where did setup become difficult: choosing a trigger, expressing conditions, configuring an action, connecting an app, testing or monitoring?
7. What would you need to see before trusting the workflow to run?
8. What types of action should always require human approval?
9. How do you notice a workflow has failed, and how do you recover?
10. What would make you abandon an automation attempt?

## 10. Sources

1. Shopify Engineering — [Flow generation through natural language: An agentic modeling approach](https://shopify.engineering/fine-tuning-agent-shopify) (updated 22 April 2026).
2. Shopify Help Center — [Creating workflows manually or from a template](https://help.shopify.com/en/manual/shopify-flow/create/create-workflow).
3. Shopify Help Center — [Testing a workflow in Shopify Flow](https://help.shopify.com/en/manual/shopify-flow/manage/test-workflow).
4. Shopify Help Center — [Using the workflow editor in Shopify Flow](https://help.shopify.com/en/manual/shopify-flow/create/workflow-editor).
5. Shopify Help Center — [Reviewing the version history of a workflow in Shopify Flow](https://help.shopify.com/en/manual/shopify-flow/manage/version-history).

---

**Research integrity note:** Shopify's disclosures are first-party evidence for how Shopify describes its own product and system. They are not independent verification of all performance claims. Merchant segment sizes, customer pain intensity, willingness to pay and product opportunity remain unvalidated until primary research is conducted.
