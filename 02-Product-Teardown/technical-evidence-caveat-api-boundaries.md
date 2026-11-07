# Shopify Flow — Technical Evidence Caveat (current documentation review)

**Purpose:** Keep technical claims in the interview and MVP decision work accurate.

## What current Shopify documentation supports

1. Shopify's Flow monitoring documentation says merchants can inspect workflow runs in the Flow app. Run details include a workflow preview with the path taken, step results, logs and step data. Access to step details depends on staff permissions. Runs are retained for 14 days after completion.
2. The `Get workflow run data` Flow action returns a single run's documented metadata: run GID, legacy resource ID, retry count, start time, trigger-event information, URL and workflow metadata. Its documented returned data does not list per-condition results, step IDs or intermediate step payloads.
3. Shopify's Flow developer documentation lists trigger, condition, action and template task types, and explicitly says app developers cannot create a condition task. Apps can build Flow triggers and actions.
4. Shopify's public Admin GraphQL documentation reviewed for this note does not document a query for `FlowRun` or step-level Flow execution traces. This is a documentation finding, not a proof that every possible internal or partner-only interface is absent.

## What remains an inference / experiment

- Whether a third-party app can access any additional run data through currently available public or partner APIs must be tested against the actual versioned schema and authorised app scopes.
- Do not state categorically that the Admin UI is "exclusively sandboxed" or that no programmatic path exists unless a documented schema/introspection experiment supports it.
- A custom Flow action can receive its own invocation payload when a workflow reaches that action. Because an app cannot create a custom condition task, such instrumentation should not be described as observing all condition evaluations or false branches automatically.
- A preflight linter based on a workflow export is only useful if a supported, merchant-authorised export/import or data-capture path is established. Do not assume an app can read arbitrary workflow definitions.

## Recommended wording for the project

"Shopify documents step-level run diagnostics in the native Flow interface. Its documented Get workflow run data action exposes run-level metadata rather than the full step trace. The public Admin GraphQL references reviewed do not document a FlowRun query or step-level execution-trace resource. Therefore, independent live diagnostics are an unresolved feasibility risk pending version-specific schema introspection and a development-store experiment."

## Official sources

- Monitoring workflow runs: https://help.shopify.com/en/manual/shopify-flow/manage/monitor
- Get workflow run data: https://help.shopify.com/en/manual/shopify-flow/reference/actions/get-workflow-run-data
- About Flow extensions: https://shopify.dev/docs/apps/build/flow
- Flow and GraphQL Admin API: https://help.shopify.com/en/manual/shopify-flow/getting-started/concepts/admin-api
- Current GraphQL Admin API reference: https://shopify.dev/docs/api/admin-graphql/latest
- Flow trigger receive mutation: https://shopify.dev/docs/api/admin-graphql/latest/mutations/flowTriggerReceive
- Preview API data in Flow: https://help.shopify.com/en/manual/shopify-flow/create/preview-data

