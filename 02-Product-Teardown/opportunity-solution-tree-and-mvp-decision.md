# Phase 4 — Opportunity Solution Tree & MVP Decision Gate

**Project:** Project-4-Shopify-Flow-AI  
**Research phase:** Phase 4 — Opportunity selection  
**Evidence cut-off:** 7 November 2026  
**Inputs:** Shopify engineering/Help Center research; Reddit Evidence Logs 01–03; Phase 3 Synthesis 01; the five pre-existing opportunity hypotheses in `02-Product-Teardown/opportunity-hypotheses.md`  
**Status:** Prioritisation recommendation only. No MVP is validated or finally selected.

---

## 1. Decision summary

**Recommendation:** Select **H3 — Test coverage and external-action clarity** as the leading opportunity for targeted validation, with **H4 — Change review for conversational edits** as the second opportunity to investigate. Keep **H5 — Integration readiness and recovery** as a strong adjacent opportunity, subject to technical feasibility checks.

Do **not** commit to building a standalone monitoring product or an AI workflow generator yet. The research signals point toward problems around the edges of workflow creation—testing, interpretation, safe modification and integration readiness—but the current corpus does not establish market prevalence, willingness to pay, or API feasibility.

The next step after this decision packet is **targeted live merchant/operator interviews and task-based usability tests** focused on H3 and H4, plus a technical feasibility spike for H3/H5.

### Why this is the current recommendation

- The supplied 25-discussion synthesis identifies recurring signals around outcome observability/silent failures and Sidekick trust/debugging.
- R25 specifically reports fast initial drafting but difficulty identifying which condition sub-clause caused a False result.
- The earlier research records a distinction between execution failure, expected no-action, unexpected no-action and an action that runs without the intended business outcome.
- Shopify already provides workflow testing and run details. Therefore, the opportunity is not “testing does not exist”; it is whether users can understand coverage, interpret condition outcomes and identify residual risk.
- H4 has a clear signal in the reported “template ceiling”, but Shopify already documents conversational updates and Apply/Discard controls. The opportunity must be framed as better change comprehension and safe extension—not the absence of conversational editing.

---

## 2. Desired product outcome

> **Help Shopify merchants confidently create, adapt and operate automations that achieve their intended business outcomes, while reducing avoidable debugging effort and unintended operational actions.**

This is a strategic outcome statement, not a claim that current Shopify merchants broadly fail at automation.

### Candidate outcome measures

| Measure | What it tells us | Caution |
|---|---|---|
| Time to diagnose an unexpected workflow result | Whether the product reduces troubleshooting effort | Requires a clearly defined start/end point and comparable tasks |
| Correct identification of why a workflow did or did not take an action | Whether explanations improve user understanding | Measure against a known ground-truth trace |
| Correct activation decision after reviewing a test/preflight report | Whether users understand coverage and residual risk | More activations are not automatically better; unsafe activation is a failure |
| Unintended or incorrectly configured external actions | Whether the product improves safety | Must be measured in a controlled test environment, not by deliberately risking live stores |
| Successful completion of a representative workflow task | Whether the end-to-end experience works | Separate structural validity from the intended business outcome |

**North-star metric candidate for validation:** Percentage of representative workflow tasks in which a merchant correctly predicts, validates and explains the workflow's intended outcome before activation. This is a research/experiment metric proposal, not a current baseline.

### Guardrails

- No automatic activation of merchant workflows.
- No silent inference of email recipients, consent, refund values, inventory policy or other consequential configuration.
- Simulated external actions must be labelled as simulated; do not imply that a real third-party system was contacted.
- A “successful” workflow execution must not be presented as proof that the intended business outcome occurred.
- Keep auditability: proposed changes, applied changes, test inputs, assumptions and unresolved warnings.

---

## 3. Opportunity Solution Tree

```text
DESIRED OUTCOME
Increase merchant confidence that Shopify automations will produce the intended business outcome,
while reducing avoidable debugging and unintended operational actions.
│
├── O1. Understand why a workflow took, skipped or failed an action
│   ├── S1. Condition-level execution trace: expected value vs actual value
│   ├── S2. Plain-language explanation of the branch taken and why
│   ├── S3. Distinguish expected no-action from unexpected no-action
│   └── S4. Suggest a possible correction, with evidence and merchant confirmation
│
├── O2. Know what testing did and did not validate before activation
│   ├── S5. Test coverage summary by branch and action
│   ├── S6. Explicit list of external side effects that were not simulated
│   ├── S7. Safe test-data / sandbox guidance and manual verification checklist
│   └── S8. Test-case suggestions for missing branches and edge conditions
│
├── O3. Safely adapt an existing template or workflow
│   ├── S9. Conversational editing of existing logic
│   ├── S10. Human-readable before/after diff by trigger, condition and action
│   ├── S11. Explain affected paths and likely behaviour changes
│   └── S12. Apply / discard / restore with a clear audit trail
│
├── O4. Detect configuration and integration prerequisites before a run fails
│   ├── S13. Connector availability and permission/scope preflight
│   ├── S14. Consent-aware checks for customer-facing communications
│   ├── S15. Liquid / payload / required-field validation
│   └── S16. Connector health and recovery guidance
│
└── O5. Keep delayed and high-volume workflows aligned with current store state
    ├── S17. Re-fetch / freshness checks before delayed actions
    ├── S18. Warnings for potentially stale data assumptions
    ├── S19. Load / rate-limit risk indicators for complex high-frequency flows
    └── S20. Safe backfill / replay planning with idempotency safeguards
```

**Interpretation:** The tree groups the evidence into user opportunities first, then lists possible solutions. The solution ideas are deliberately not treated as requirements. Customer research may invalidate, merge or split opportunities.

---

## 4. Evidence-to-opportunity map

The Phase 3 synthesis supplied with this project reports the following discussion clusters. Counts are copied from that synthesis; they are **not independent market-prevalence estimates**. The corpus may contain overlapping themes, mixed respondent types and self-selected posts. Before using these numbers in a public portfolio case study, audit the R1–R25 coding and confirm whether each discussion was assigned to exactly one primary theme.

| Reported evidence cluster | Count in supplied synthesis | Related opportunities | Relevant new examples in R18–R25 |
|---|---:|---|---|
| Outcome observability & silent failures | 6 | O1, O2, O5 | R24 stale state after delay; R25 opaque False condition result |
| Template ceiling & editing friction | 5 | O3 | R22 difficulty extending a pre-built template |
| Connector & scope fragility | 5 | O4 | R18 marketing consent; R19 ShipStation scope/mapping; R20 Gorgias Liquid payload |
| Historical backfilling barrier | 3 | O2, O5 | No new direct example in R18–R25; cited in earlier R9 synthesis |
| Sidekick trust & debugging | 6 | O1, O3 | R25 fast draft but hard-to-diagnose condition result |
| Other runtime complexity signals | Not separately counted | O4, O5 | R21 multi-location inventory; R23 rate limits; R24 delayed state freshness |

### Evidence quality caveats

1. Reddit discussions are self-selected anecdotes, not representative samples.
2. Some posts are in developer communities; developer/operator evidence should not automatically be attributed to non-technical merchants.
3. At least one response in the earlier batch came from a vendor/app developer promoting their own product. Treat vendor claims as competitive intelligence, not independent proof of customer demand.
4. A single post may support multiple themes. The reported category counts must not be added together or used as statistical weights until coding rules and overlap are audited.
5. Claims such as “workflow completed but silently failed” require precise technical classification. A condition evaluating False may be an expected no-action path, not an execution error.
6. Claims about third-party test side effects, API rate limits, consent enforcement and delayed state refresh require direct documentation or merchant interview confirmation before becoming product requirements.

---

## 5. Score the five existing hypotheses (H1–H5)

### Scoring rubric

This is a **qualitative prioritisation rubric**, not RICE and not a measured market score.

- **Evidence fit:** How directly the supplied corpus and Shopify disclosures support the opportunity.
- **Potential impact:** How consequential the problem could be when it occurs.
- **Portfolio / product differentiation:** Whether a useful product experience could improve on current capabilities.
- **Feasibility / controllability:** Whether an independent product team could plausibly deliver the capability without relying on inaccessible Shopify internals or third-party partner changes.

Ratings are Low / Medium / High. Confidence is separately stated to avoid false precision.

| Hypothesis | Evidence fit | Potential impact | Differentiation | Feasibility / controllability | Current priority | Confidence |
|---|---|---|---|---|---:|---|
| **H1. Intent-aware workflow assistant** | Medium-Low in Reddit corpus; Shopify's disclosed request mix supports intent ambiguity, but R18–R25 offer little direct evidence of wrong-intent routing | Medium | Medium | Medium-High if built as an assistant layer; integration depth still unknown | 5 | Low-Medium |
| **H2. Contextual configuration completion** | Medium; R18 consent, R20 Liquid payloads, R19 connector configuration and Shopify's disclosed email configuration failures are relevant | High for affected customer-facing or fulfillment actions | Medium-High | Medium; requires reliable schema, consent and connector metadata | 3 | Medium |
| **H3. Test coverage and external-action clarity** | High relative to this corpus; R25 diagnostic opacity, R24 delayed-state behaviour, previous R12 test-side-effect signal, plus the wider observability/debugging clusters | High where an incorrect or misunderstood action affects customers or fulfillment | High if explanations are accurate, traceable and actionable | Medium-Low until workflow trace, test and API access are verified | **1** | Medium; validate directly |
| **H4. Change review for conversational edits** | Medium-High; “template ceiling” theme and R22, plus manual correction signal in R25; Shopify already supports conversational updates and Apply/Discard | Medium-High for users with non-trivial workflows | Medium; change diffs and behaviour explanations may differentiate, but basic edit capability exists | Medium-High for a prototype; production integration feasibility unknown | **2** | Medium |
| **H5. Integration readiness and recovery** | High within connector-related anecdotes: R18 consent, R19 scopes/mapping, R20 Liquid/payload syntax | High for workflows connected to marketing, support or fulfillment systems | High if preflight can detect actionable, preventable issues | Low-Medium because connector APIs, permissions and third-party changes constrain coverage | 3 (tie with H2) | Medium-Low |

### What the ranking means

- **H3 is the leading validation candidate, not a selected MVP.** It has the clearest connection to the repeated debugging/observability signal, but feasibility depends on what execution traces and test details a third-party product can access.
- **H4 is the second candidate.** It may be easier to prototype and test with merchants, but we must test against the current Sidekick conversational-edit and Apply/Discard experience rather than assume those features are absent.
- **H2 and H5 are related but distinct.** H2 concerns completing and explaining required configuration; H5 concerns connector availability, permissions, health and recovery. Validate whether merchants experience them as one workflow or separate problems.
- **H1 is not discarded.** It remains a relevant product architecture capability, but the Reddit corpus does not make it the best first wedge.

---

## 6. Leading MVP hypothesis — deliberately provisional

### Candidate: Explainable workflow test and run diagnostics

**User problem hypothesis:** When a workflow does not take the action a merchant expected, existing run/test information may not make it sufficiently easy for that merchant to identify the decisive condition, data value, untested path or external-action limitation.

**Candidate experience:**

1. Select a workflow test/run.
2. See the path actually taken, at trigger/condition/action level.
3. See the relevant input values used by each condition.
4. Get a plain-language explanation of why a branch matched or did not match.
5. Distinguish expected no-action, execution error, configuration issue and downstream outcome uncertainty.
6. See what the test did not cover, especially external actions or unavailable test fixtures.
7. Receive a suggested correction, but require the merchant to review and apply it.

**What it is not:**

- Not a new workflow generator.
- Not a claim that Shopify has no logs or testing.
- Not a guarantee that downstream third-party actions succeeded.
- Not an automatic workflow fixer or activator.

### Key feasibility question

Can a third-party app or companion product legally and technically access enough workflow definitions, test traces, condition values and execution details to provide this experience? This must be investigated before committing to an implementation architecture.

---

## 7. Riskiest assumptions to validate

| Assumption | Why it matters | Cheapest credible validation |
|---|---|---|
| Merchants cannot reliably explain why a workflow did not take an expected action | Core problem validity | 6–8 interviews; ask for the most recent concrete incident and current workaround |
| Existing Shopify run details are insufficient for the target user/task | Differentiation | Observe 5 users interpreting the same sanitised run/test trace in current UI |
| Condition-level explanations improve diagnosis, not just perceived clarity | Outcome value | Task-based prototype test; compare time and correctness against current run details |
| The required trace and condition data are accessible to an independent product | Technical feasibility | Review Shopify APIs, app permissions, partner constraints and available webhook/run data; do not infer from UI alone |
| Merchants will trust suggested corrections | Safety | Test with read-only recommendations and require explicit user approval |
| The problem is frequent and costly enough to prioritise | Business value | Interview for recurrence, time spent, operational impact and existing spend/workarounds; avoid hypothetical willingness-to-pay questions alone |
| H4 change review is not already sufficiently solved by Sidekick | Competitive gap | Compare real existing edit tasks in current Shopify UI against a diff prototype |

---

## 8. Phase 3B — Targeted live validation plan

Do not conduct another broad generic survey. Recruit around the leading opportunities.

### Participant mix (initial qualitative round)

- 4–6 Shopify merchants/operators who actively use Flow and have built or modified workflows.
- 2–3 Shopify developers, agencies or implementation partners who troubleshoot more complex workflows.
- Where feasible, include both users who are satisfied with Flow and users who have encountered problems.

These are recruitment targets, not claims about a representative sample.

### Interview prompts for H3

1. Tell me about the most recent time a workflow did not do what you expected.
2. What did you expect to happen, and what actually happened?
3. How did you discover the difference?
4. Can you show or describe the run details you checked? Please do not share customer-identifying information.
5. How did you determine which condition or action caused the result?
6. How long did diagnosis take, and what workaround did you use?
7. What did you change, and how did you verify the fix?

### Interview prompts for H4

1. Tell me about the last time you modified an existing Flow template or workflow.
2. What change were you trying to make?
3. Which part was difficult: locating the right condition, understanding fields, expressing the logic, or predicting the effect?
4. Did you use Sidekick? What did it change correctly, and what did you have to review manually?
5. Before applying a change, what would you need to see to feel confident?

### Task-based concept test

Give participants a sanitised example workflow and a known test trace. Ask them to:

- identify why a branch did not execute;
- distinguish expected no-action from an unexpected result;
- identify any untested external action;
- decide whether to activate, revise or investigate further.

Compare current Shopify UI information with a low-fidelity diagnostic prototype. Record correctness, time, confidence and unsafe decisions. Do not use live merchant orders or trigger real third-party side effects.

---

## 9. MVP decision gate — what must be true before building

We can commit to an MVP only when all of these are met:

1. **Problem evidence:** Multiple independent target users describe recent, concrete examples of the same problem without being led by the interviewer.
2. **Existing-workflow gap:** Observation confirms where existing Flow logs, test tools or Sidekick editing are insufficient for that task.
3. **Severity:** Users can describe meaningful time, operational or customer impact and their current workaround.
4. **Technical feasibility:** Required data and permissions are confirmed through documentation or a small technical spike.
5. **Prototype value:** In task testing, the proposed experience improves user correctness or diagnosis time without increasing unsafe activation decisions.
6. **Scope discipline:** The MVP solves one narrow job; it does not bundle monitoring, generation, integration management and backfill into a broad platform.

### Decision today

**Proceed with H3 validation first, H4 validation second, and a parallel H3/H5 technical feasibility check. Do not start full implementation yet.**

---

## 10. Portfolio narrative — evidence-led version

A defensible case-study narrative at this stage is:

> “I began by investigating whether Shopify merchants needed faster AI workflow generation. A review of 25 public discussions suggested that initial creation may not be the only or primary friction: users also described difficulty adapting templates, understanding condition outcomes, handling connector requirements and maintaining correct behaviour over time. I therefore reframed the opportunity around confidence in workflow outcomes. The leading hypothesis is explainable testing and run diagnostics, but I have not selected an MVP until merchant interviews, task-based usability tests and a technical access review establish that the problem is frequent, consequential and solvable.”

This wording preserves the distinction between observed reports, synthesis and unvalidated product hypotheses.

