# Opportunity hypotheses — Shopify Flow + Sidekick

**Purpose:** Convert product teardown observations into opportunities that can be tested. None of these is a validated customer need yet.

## Opportunity 1 — Intent-aware workflow assistant

**Evidence:** Shopify observed real requests involving editing existing workflows and questions about Flow that did not intend to create a workflow.  
**Hypothesis:** A front door that classifies the user's intent (create, edit, explain, troubleshoot, test) and routes the conversation accordingly may reduce wrong-path interactions.  
**Prototype:** Intent selection is inferred from language, but the assistant exposes a lightweight confirmation when confidence is low.  
**Test:** Give merchants mixed creation, editing and informational tasks; compare task completion and correction rate with the current flow.  
**Metric candidates:** correct intent routing, successful task completion, unnecessary workflow-generation rate, user correction rate.

## Opportunity 2 — Contextual configuration completion

**Evidence:** Shopify reported email workflows as 25% of failures; Help Center documents Review banners for missing required values.  
**Hypothesis:** A contextual assistant that explains the missing field, why it is required and where the merchant can find the value may be more effective than a generic validation message.  
**Prototype:** For each required field, show why it is needed, a safe example, source of the value, and a prompt for the merchant to confirm or supply it. Never silently invent an email address or store-specific value.  
**Test:** Compare the existing configuration experience with guided completion on representative tasks.  
**Metric candidates:** unresolved required fields, time to complete configuration, invalid values, user confidence.

## Opportunity 3 — Test coverage and external-action clarity

**Evidence:** Shopify already supports recorded events, manual simulated events and Sidekick-generated events. Test mode prevents live changes, but incomplete steps cannot be tested and certain external actions cannot be fully simulated.  
**Hypothesis:** A test summary that explicitly labels covered paths, untested actions, assumptions and manual verification steps could improve merchant understanding of residual risk.  
**Prototype:** A preflight report with path coverage (pass/fail), configuration checks, external-service limitations, and a suggested manual verification checklist.  
**Test:** Ask merchants to decide whether to activate workflows after reviewing current test output or an enhanced preflight report.  
**Metric candidates:** risk comprehension, missed limitations, correct activation decisions, time to diagnose failure.

## Opportunity 4 — Change review for conversational edits

**Evidence:** Shopify documents conversational workflow updates with Apply changes / Discard changes. Shopify's engineering post identifies workflow editing as an area missing from synthetic training data.  
**Hypothesis:** Merchants may benefit from a human-readable change summary that highlights which trigger, condition, action or field was changed before applying an edit.  
**Prototype:** Side-by-side before/after diff, natural-language explanation, affected workflow paths, and apply/discard.  
**Test:** Give merchants a request to modify a workflow and measure whether they can correctly predict the behaviour after the change.  
**Metric candidates:** edit acceptance, edit reversal, change comprehension, unintended logic changes.

## Opportunity 5 — Integration readiness and recovery

**Evidence:** Shopify observed third-party integration requests among under-covered production traffic; connectors are app-provided actions and may require the connector to be installed.  
**Hypothesis:** A pre-generation integration readiness check could identify unavailable connectors, missing permissions or unsupported actions before the user invests in configuring the whole workflow.  
**Prototype:** Connector availability check, permission explanation, substitute-action suggestions where semantically valid, and explicit disclosure when no safe substitute exists.  
**Test:** Run workflows requiring installed, uninstalled and unsupported connectors.  
**Metric candidates:** connector-related setup failures, recovery success, time to working flow, unsupported-action detection.

## Prioritisation — provisional

Use a simple RICE-like score only after discovery gives us reach and impact evidence. For now, this is a qualitative sequencing recommendation:

1. **Intent-aware routing** — closest to the specific production gaps Shopify disclosed.
2. **Contextual configuration completion** — connects observed email failures to an existing Review-banner workflow.
3. **Test coverage / external-action clarity** — builds on an existing capability instead of pretending testing does not exist.
4. **Change review** — potentially useful for editing reliability and user confidence.
5. **Integration readiness** — valuable but dependent on connector-specific technical feasibility and partner/app behaviour.

## Guardrails

- Do not auto-populate recipient addresses, financial values, refund settings or other consequential fields without explicit confirmation.
- Do not activate workflows automatically in the prototype.
- Clearly distinguish simulated outputs from actual external-system responses.
- Show what was not tested, not only what passed.
- Keep an audit trail of proposed and applied changes.
- Respect existing Apply/Discard and version-history/restore capabilities; any prototype should improve their discoverability or clarity rather than claiming these controls do not exist.

