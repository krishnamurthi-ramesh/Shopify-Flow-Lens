# Phase 3 onward — Customer validation and product execution roadmap

**Project:** Shopify Flow + Sidekick teardown and independent product opportunity exploration  
**Status:** Ready to execute; no customer problem or proposed feature is validated yet  
**Research cut-off:** 7 November 2026

## 0. Where we are now

The repository contains:

- `01-Research/problem-discovery-and-user-segmentation.md` — evidence ledger, provisional behavioural segments and JTBD framing.
- `02-Product-Teardown/shopify-flow-sidekick-deep-dive.md` — public product and engineering teardown.
- `02-Product-Teardown/opportunity-hypotheses.md` — five opportunity hypotheses and guardrails.
- `03-Discovery-Plan/merchant-interview-guide.md` — qualitative interview guide and note-taking template.

The existing work is a desk-research foundation. It is **not** proof that a particular merchant segment has the problem, that the problem is frequent or severe, or that merchants will pay for a solution.

## 1. Current source-grounded baseline

Use these as the starting facts to verify during the teardown; do not overstate them:

1. Shopify reported that its fine-tuned Flow agent's workflow activation rate was 35% lower than the prompt-based baseline in a 1% traffic deployment. This is a relative comparison, not a 35% abandonment rate.
2. Shopify identified under-covered production requests including workflow editing, email workflows, varied condition patterns, third-party integrations and informational questions about Flow. The engineering article reports email workflows as 25% of failures and diverse condition patterns as 16%.
3. Sidekick can generate workflows from natural-language descriptions. Generated workflows are inactive by default and should be reviewed and tested before activation.
4. Shopify Flow supports workflow testing using saved/recorded events and Sidekick-generated events. Test results can show the logic path and previews. Test mode does not make live store changes.
5. The editor has Review banners for missing required configuration, and unresolved configuration errors prevent activation.
6. Sidekick can make conversational workflow updates; the user can apply or discard the proposed changes.
7. Workflow version history records edits and status changes, supports comparison, and permits restoration as a draft.

Primary sources:

- Shopify Engineering: https://shopify.engineering/fine-tuning-agent-shopify-flow
- Generate workflows with Sidekick: https://help.shopify.com/en/manual/shopify-flow/create/create-workflow
- Test a workflow: https://help.shopify.com/en/manual/shopify-flow/manage/test-workflow
- Workflow editor: https://help.shopify.com/en/manual/shopify-flow/create/workflow-editor
- Workflow version history: https://help.shopify.com/en/manual/shopify-flow/manage/version-history

## 2. Phase 3 — Validate the customer problem

### Objective

Find out which merchant/operator segment has a recurring, important workflow-automation job; how they currently solve it; where Flow, Sidekick or alternatives fall short; and what evidence they need to trust an automation.

### 2.1 Recruit participants

Recruit people who operate or configure Shopify stores. Seek behavioural diversity rather than screening on unsupported GMV cut-offs:

- Owner-operator with few recurring automations.
- Growing operator-led store where operations are shared across a small team.
- More process-mature store using multiple apps, integrations or specialist support.

For each participant, capture their role, store operating context, order/exception complexity in broad self-reported ranges, tools used, automation maturity, and whether they personally configure workflows.

Suggested first research batch: 6–8 interviews for directional qualitative discovery. This is not a statistically representative sample. Continue recruiting if the sample is too homogeneous or if important counterexamples emerge.

Recruit through personal/professional contacts, Shopify merchant communities, ecommerce operator groups, agency/partner networks and relevant founder communities. Be transparent that this is independent student/portfolio research, not Shopify-sponsored research.

### 2.2 Conduct interviews

Use `merchant-interview-guide.md`. Ask about the **last real occurrence**, not hypothetical preferences.

Do:

- Ask participants to walk through a recent exception from detection to resolution.
- Ask them to show their current workflow or workaround where possible.
- Ask what happens when the task is late or wrong.
- Ask how they create, edit, test, monitor and recover automations today.
- Ask what they tried before and why they kept or abandoned it.
- Ask permission before recording or quoting.

Do not:

- Lead with our proposed AI feature or dry-run concept.
- Tell participants that Shopify's product has a problem and ask them to agree.
- treat a self-reported estimate as verified operational data.
- collect customer personal data, API credentials, order details or confidential store information.

### 2.3 Observe tasks where possible

Ask participants to think aloud using their own test store or a safe demonstration environment. Useful tasks:

- Find an existing workflow and explain what it does.
- Change one condition in an existing workflow.
- Resolve a missing configuration warning.
- Run a test with one event expected to pass and one expected not to pass.
- Identify which actions or integrations are not covered by the test.

Never activate or execute a customer-facing, financial, inventory-changing or otherwise consequential workflow as part of research. Use a test store, dummy data or a non-executing prototype.

### 2.4 Capture evidence consistently

For every interview, record:

- Participant ID and role (anonymised).
- Recent task and trigger.
- Actual steps and tools used.
- Frequency (participant estimate, clearly labelled).
- Time/effort (observed or self-reported; label the source).
- Failure mode and consequence.
- Workaround and why it persists.
- Existing Flow/Sidekick capability used or not used.
- Trust/control requirement.
- Exact quote (with permission).
- Researcher interpretation, kept separate from the participant's words.
- Contradictory evidence and confidence level.

### 2.5 Synthesis and decision gate

Cluster observations by job and friction, not by demographic labels alone. Compare:

- Frequency of the job.
- Severity and consequence of failure.
- Current workaround and effort.
- How well existing Shopify capabilities address the job.
- Whether the issue is model quality, product UX, integration coverage, permissions, education or a limitation outside Shopify's control.
- Evidence of urgency and willingness to change/pay (do not infer willingness to pay from enthusiasm).

Proceed to solution design only when the same problem is independently observed across participants, has a meaningful consequence, current alternatives are demonstrably inadequate for that job, and a product intervention could plausibly improve the outcome. If evidence is weak or contradictory, narrow or change the segment/problem instead of defending the original thesis.

**Phase 3 deliverables:** interview notes, anonymised evidence log, behavioural segmentation, revised JTBD, current-state journey, prioritised problem statement, assumptions register and a go / revise / stop decision.

## 3. Phase 4 — Choose one opportunity and define the MVP

Revisit `opportunity-hypotheses.md` only after Phase 3. Score each opportunity using evidence for:

- User problem severity and frequency.
- Evidence that the current product does not adequately solve it.
- Reach within the validated segment.
- Feasibility and dependency on Shopify APIs, partner apps or permissions.
- Safety and reversibility.
- Ability to measure improvement.

Select **one primary job** for the MVP. Avoid combining intent routing, configuration assistance, simulation, change diffs and connector readiness into one oversized first release.

**Deliverables:** opportunity assessment, chosen target user, revised problem statement, MVP/non-goals, PRD, user stories, acceptance criteria, risks and metric tree.

## 4. Phase 5 — Prototype and usability test

Build a clickable prototype before implementing live integrations. Use synthetic store data and clearly label simulated states.

Test whether target users can:

- Understand what the assistant proposes.
- Identify missing or uncertain information.
- Understand what has and has not been tested.
- Predict what the automation will do.
- Review changes and reject them safely.
- Recover from a failed or incorrect configuration.

Capture task completion, critical errors, assistance needed, comprehension and confidence. Do not equate stated preference with usability or willingness to pay.

**Deliverables:** low-fidelity flow, clickable prototype, usability findings, iteration log and updated requirements.

## 5. Phase 6 — Build a bounded technical MVP and evaluate it

Only after the product scope is selected:

- Implement a constrained workflow representation and a small set of supported triggers, conditions and actions.
- Use a mock/test environment first; no live Shopify store actions by default.
- Make the system ask for confirmation when required information is missing or ambiguous.
- Validate generated workflows structurally and semantically.
- Separate tool/model failures from user-input gaps and unsupported integrations.
- Create a fixed evaluation set from interview-derived, anonymised scenarios plus adversarial edge cases.
- Compare a baseline prompt-only approach with the proposed intervention.

Candidate evaluation dimensions:

- Intent classification accuracy.
- Workflow semantic correctness.
- Required-field completeness.
- Invalid/unsupported action rate.
- Test-path coverage and accuracy of limitation disclosure.
- User correction rate and task completion.
- Latency and estimated cost per completed task.
- Unsafe-action prevention.

**Deliverables:** working demo, architecture diagram, evaluation dataset description, results, failure analysis and known limitations.

## 6. Phase 7 — Business case, GTM and launch plan

Build the business case only from validated or explicitly modelled assumptions:

- Target customer and buyer/user distinction.
- Positioning against native Flow, manual work and external automation tools.
- Packaging and pricing hypotheses to test.
- Acquisition channels and onboarding.
- Activation and retention journey.
- Support, permissions, partner and integration dependencies.
- Cost model: inference, tools, support and infrastructure.
- Launch experiment and success/guardrail metrics.

Do not invent TAM, CAC, conversion, retention, revenue uplift or ROI. Show formulas and assumptions, and mark illustrative scenarios clearly.

**Deliverables:** positioning, GTM plan, pricing research, launch experiment, KPI tree, risk register and 30/60/90-day iteration roadmap.

## 7. Portfolio packaging

The final case study should tell one coherent story:

1. What was disclosed by Shopify (with links and precise attribution).
2. What we independently observed in customer discovery.
3. Which problem and segment we selected, and why.
4. Which alternatives and product gaps we assessed.
5. What we chose to build—and what we deliberately did not build.
6. How the prototype performed in usability testing.
7. How the AI system was evaluated, including failures and limitations.
8. How we would launch, measure and iterate the product.

Keep company-reported results, participant evidence, product hypotheses, prototype results and projections in separate categories. State clearly that this is an independent portfolio project, not an official Shopify product or internal Shopify research.

## 8. Immediate next actions

1. Identify 10–15 potential interview participants; aim to book the first 6–8 conversations.
2. Send a neutral recruitment message describing a short research conversation about store operations and workflow automation.
3. Run the first interview using `merchant-interview-guide.md` without pitching our solution.
4. Write up the interview within 24 hours, separating quotes, observed behaviour and interpretation.
5. After the first three interviews, review whether recruitment is reaching the intended behavioural segments; adjust the sample if needed.
6. Complete the batch, synthesise findings and make a go / revise / stop decision before selecting the MVP.

**The next task is participant recruitment and real problem validation—not coding the AI workflow builder yet.**

