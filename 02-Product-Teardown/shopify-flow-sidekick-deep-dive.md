# 02 — Existing Product Teardown: Shopify Flow + Sidekick

**Status:** Desk-research teardown of publicly documented capabilities  
**Research cut-off:** 6 November 2026

## 1. Product overview

Shopify Flow is a workflow automation product inside Shopify Admin. Its core building blocks are:

- **Trigger:** an event that starts a workflow.
- **Condition:** criteria that determine whether a path/action should run.
- **Action:** a change in the store, workflow data, or an external app/service.
- **Connector:** an action supplied by an app/service integration.

A workflow begins with one trigger and may contain multiple conditions and actions. The editor presents steps in a top-to-bottom visual layout with branches for true/false conditions. Sidekick adds a natural-language interface for generating and updating workflows.

## 2. Product experience — documented happy path

### A. Creation through Sidekick

1. Merchant opens Sidekick from Shopify Flow.
2. Merchant describes the workflow in natural language.
3. Sidekick generates a workflow and gives an overview of how it operates.
4. Merchant opens the generated workflow.
5. Merchant reviews and tests the workflow.
6. Merchant can ask Sidekick to update it; changes can be applied or discarded.
7. Merchant manually turns on the workflow.

**Important safeguards:** Sidekick-generated workflows are inactive by default. Shopify instructs merchants to review and test them before activation.

### B. Manual creation / editing

1. Select a trigger.
2. Add a condition and define criteria.
3. Add action(s) on the true/false paths.
4. Configure step details in the configuration panel.
5. Name and review the workflow.
6. Test it and turn it on.

### C. Testing before activation

Shopify documents three ways to supply trigger-event data:

- **Record events:** listen for live events occurring in the store; the merchant can also create a live event to capture test data.
- **Create events:** manually select or construct simulated events using existing store data, without making changes to the live store.
- **Generate events:** Sidekick can generate simulated test events; Shopify's example uses one event that passes a condition and another that fails it.

In test mode:

- No live notifications, order/product updates or store-data modifications are made.
- The test highlights the logic path for the selected event.
- The merchant can preview output values for conditions/actions and inspect variable values.
- Incomplete or misconfigured steps cannot be tested.
- A test run stops at the first action that would make a live change.
- Some external-service actions (for example, Send HTTP request) can show a preview but cannot be simulated as a real returned value.

### D. Configuration validation

The workflow editor can display a Review banner for a step with missing required configuration. The merchant opens the step configuration panel to supply the missing details. Unresolved configuration errors prevent activation.

## 3. AI system disclosed by Shopify

### Model and tool-use approach

- Shopify reports fine-tuning Qwen3-32B into a tool-calling agent for Sidekick's Flow generation capability.
- Training examples were built by starting with validated workflows created by merchants, generating plausible natural-language requests, and constructing multi-turn tool-call trajectories.
- The team used a 300-example hand-crafted benchmark.
- The internal Flow JSON DSL was translated to a Python representation for model generation, then transpiled back to the production JSON format.
- The Python representation improved syntactic correctness by 22 points and semantic correctness by 13 points on Shopify's reported evaluation.
- Shopify reported the fine-tuned system as 2.2x faster and 68% cheaper than the frontier-model approach it replaced.

### Production evaluation and learning loop

The 1% rollout revealed a workflow activation rate 35% lower than the prompt-based baseline. Shopify describes this as an offline-to-online gap, not a 35% absolute abandonment rate.

The observed gaps included:

- Editing existing workflows.
- Email configuration.
- Third-party integrations.
- Questions about Flow that did not intend to create a workflow.

Shopify reports that email workflows accounted for 25% of failures and diverse condition patterns for 16%. Workflow editing was absent from the original synthetic dataset.

The team then added:

- An LLM judge grading intent understanding, whether Flow was the right solution, component selection and clarity of next steps.
- Human annotation calibration using hundreds of conversations.
- Workflow tagging by trigger, condition, action and integration dimensions.
- A weekly data loop: ingest conversations, score them, route high-quality examples into training, quarantine low-quality examples, identify gaps and retrain.

The article says the fine-tuned agent now serves the majority of production traffic.

## 4. Product funnel and potential drop-off points

The following is our reconstructed funnel, not a published Shopify analytics funnel.

| Stage | User goal | Existing product support | Potential friction to investigate |
|---|---|---|---|
| 1. Express intent | Explain the operational outcome in plain language. | Sidekick accepts a natural-language request. | User may not know what information the system needs; request may be ambiguous or informational rather than a creation request. |
| 2. Interpret intent | Get the correct workflow or guidance. | Tool-calling agent selects Flow components; LLM judge evaluates whether Flow is appropriate. | Unsupported requests, editing intent, third-party connector coverage. |
| 3. Generate | Receive a valid workflow. | Agent generates workflow; Python-to-JSON transpilation preserves backend representation. | Semantic mismatch, unsupported combinations, incorrect assumptions. |
| 4. Configure | Supply store-specific values and app connections. | Configuration panels and Review banners; templates also require merchant-specific values. | User may not understand which value is missing or where to find it. |
| 5. Inspect | Understand the workflow's logic. | Visual editor, step descriptions and Sidekick overview. | Complexity may make branch behaviour or external side effects hard to reason about. |
| 6. Test | Verify expected and edge-case behaviour. | Recorded, simulated and Sidekick-generated events; previews. | Incomplete steps cannot be tested; some external actions cannot be simulated. |
| 7. Activate | Turn on only when ready. | Inactive default; explicit manual activation. | Merchant confidence and understanding of residual risk. |
| 8. Monitor / improve | Detect failures and refine the workflow. | Flow has run history and users can edit workflows; Sidekick conversational updates are documented. | Need to investigate discoverability, failure explanation, repair and rollback workflows. |

## 5. Product strengths — evidence-led

1. **Low-friction entry point:** merchants can describe an automation goal in natural language rather than starting exclusively with a blank visual editor.
2. **Inspectable visual representation:** the generated result can be opened in the Flow editor and reviewed step by step.
3. **Human-controlled activation:** generated workflows are inactive by default and require explicit activation.
4. **Pre-activation test capabilities:** merchants can test against recorded or simulated events without making live store changes.
5. **Iterative refinement:** Sidekick can update a workflow through conversation, with Apply changes / Discard changes controls.
6. **Production learning loop:** Shopify has disclosed a systematic way to identify gaps in live conversations and improve the model.

## 6. Constraints and friction points — documented vs inferred

| Finding | Evidence status | PM interpretation |
|---|---|---|
| 35% lower activation in the 1% rollout vs prompt baseline. | Shopify-disclosed. | A production adoption signal exposed weaknesses that offline evaluation missed. |
| Editing, email configuration, third-party integrations and non-creation questions were under-covered. | Shopify-disclosed. | Intent coverage and workflow lifecycle coverage matter, not just new-flow generation. |
| Review banners flag incomplete configuration; unresolved errors block activation. | Help Center-disclosed. | Hard validation exists; usability of explanations and resolution remains a research question. |
| External HTTP actions cannot be fully simulated in test mode. | Help Center-disclosed. | A gap may exist between a simulated path and end-to-end behaviour across external systems. |
| Sidekick-generated workflows are inactive by default. | Help Center-disclosed. | Activation requires a user decision; this does not prove that review is understood or effective. |
| Version history supports comparison and restoring earlier versions into a draft. | Help Center-disclosed. | Recovery support exists; discoverability, speed and user comprehension during incidents remain research questions. |
| Merchants may have trouble understanding nested logic or variable namespaces. | Hypothesis from product structure; not established by reviewed sources. | Validate through observation before designing around it. |
| Merchants fear financial or reputational harm from workflow errors. | Hypothesis. | Ask about actual incidents, perceived risk and approval practices. |

## 7. Product strategy interpretation

### What Shopify appears to be optimising

Based on its disclosures, Shopify is not simply optimising text-to-workflow generation. The company is building a skill-specific agent whose quality is measured across intent understanding, component selection and next-step clarity, then improved from production conversations. It also reports optimising latency and cost while retaining workflow activation as a guardrail.

### Strategic trade-offs visible in the product

- **Natural language vs visual control:** conversational creation lowers initial interaction cost; the visual editor provides inspectability and precise editing.
- **Automation vs safety:** workflow generation is fast, but activation remains explicit and test mode avoids live effects.
- **Offline quality vs real-world usefulness:** synthetic benchmarks and formal correctness did not capture all live requests.
- **General model vs owned model:** fine-tuning and a proprietary feedback loop can improve cost, latency and domain performance, but require data pipelines, evaluation, model operations and maintenance.
- **Coverage vs correctness:** adding examples for emails, conditions and editing increases task coverage, but each new action/integration expands the validation surface.

## 8. Competitive alternatives to examine in a later phase

Do not rank these products without workflow-level testing. Compare how each handles one identical merchant job:

- Shopify Flow + Sidekick.
- Shopify Flow templates and manual builder.
- Zapier AI / natural-language automation creation.
- Make's AI-assisted scenario creation.
- A narrowly scoped custom integration or app-native automation.

Comparison criteria: time to first working automation, required expertise, connector availability, error explanations, test/simulation support, approval controls, observability, recovery and total cost for the selected workflow.

## 9. Teardown conclusion

The central product challenge is not merely generating a workflow graph. It is helping the merchant complete the full lifecycle: express the right intent, configure the correct details, understand the generated logic, test important cases, activate safely, and maintain the workflow when the store or connected apps change.

Shopify already supports important parts of this lifecycle. The most defensible opportunity areas to investigate are therefore the boundaries: ambiguous intent, configuration guidance, testing blind spots (especially external integrations), interpretable risk/impact summaries, and post-activation diagnosis and recovery.

These remain product hypotheses until validated with merchants and observed workflows.

## 10. Primary sources

1. Shopify Engineering — [Flow generation through natural language: An agentic modeling approach](https://shopify.engineering/fine-tuning-agent-shopify).
2. Shopify Help Center — [Creating workflows manually or from a template](https://help.shopify.com/en/manual/shopify-flow/create/create-workflow).
3. Shopify Help Center — [Testing a workflow in Shopify Flow](https://help.shopify.com/en/manual/shopify-flow/manage/test-workflow).
4. Shopify Help Center — [Using the workflow editor in Shopify Flow](https://help.shopify.com/en/manual/shopify-flow/create/workflow-editor).
5. Shopify Help Center — [Reviewing the version history of a workflow in Shopify Flow](https://help.shopify.com/en/manual/shopify-flow/manage/version-history).
