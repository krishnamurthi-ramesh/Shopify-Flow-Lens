# M2 — Cross-Border Cosmetics Founder: Interview Capture Sheet

**Status: PRE-INTERVIEW TEMPLATE — no interview has been conducted or findings established.**

## 1. Research intent

Understand how a cross-border cosmetics business handles country-specific order routing, shipping, compliance and customer communications, and how Shopify Flow conditions and connected systems fit into that work.

**Target-profile hypothesis (H, not a fact):** M2 may have encountered a mismatch between country values used by a Shopify workflow and values expected by a connected system. The examples "US" and "USA" are probes to use only after an unaided account suggests a country-code or value-normalisation issue.

Do not assume an ISO-code mismatch occurred, caused an automation failure, or produced a business loss.

## 2. Session details

- Participant ID: M2
- Date / time / timezone:
- Interviewer:
- Founder responsibilities:
- Markets / destinations served (broad regions only):
- Store and order context (participant-described):
- Order-volume range (optional; self-reported):
- Countries / regions most operationally important:
- Shopify Flow experience: Never used / Tried / Active user / Maintains others' workflows
- Connected systems named by participant:
- Recording permission: Yes / No
- Note-taking permission: Yes / No
- Quote permission: Yes / No / Not asked
- Artifacts available with permission: None / Redacted screenshot / Redacted run log / Workflow export / Other

## 3. Neutral opening script

"Thanks for speaking with me. I'm learning how cross-border stores manage order and shipping operations, especially how people handle exceptions and make sure automations behave as expected. I'd like to reconstruct a recent real example. Please avoid sharing customer-identifying, payment or confidential information. May I take notes?"

## 4. Interview flow and prompts

### A. Business and cross-border context (5 minutes)

1. "Could you describe the markets you sell into and your role in day-to-day operations?"
2. "What happens operationally when an order comes from a country with different shipping or handling requirements?"
3. "Which systems are involved from checkout through fulfillment and customer communication?"
4. "Which parts of that process are automated today?"

**Notes (P):**

- Markets / regions:
- Role:
- Systems and handoffs:
- Country-specific processes:
- Current automation:

### B. Recent-incident reconstruction (12–15 minutes)

Start broad and do not mention country-code formats yet:

5. "Think about the most recent cross-border order that did not follow the path you expected. What happened?"
6. "What was the first indication that something was wrong?"
7. "Can you walk me through the order and each system it passed through?"
8. "What did you expect to happen, and what actually happened?"
9. "How did you identify the point where things diverged?"
10. "What did you do to resolve it, and what was the consequence?"

Only if the account points toward a country/value mismatch, probe:

- "What exact field or value was the workflow checking?"
- "Where did that value originate?"
- "What value did the connected app or downstream system receive?"
- "Were the values represented as a country name, two-letter code, three-letter code, or something else?"
- "Did formatting, whitespace, capitalisation, localisation, or a field-path change matter?"
- "Did the trigger occur? Did the condition match? Did the action run? Did the downstream app accept it?"
- "Could you see the values used by the condition in Shopify Flow's run details?"
- "Was any data refreshed or re-fetched before a delayed action?"

Do not suggest the answer "US versus USA" unless the participant has already described that type of issue and needs clarification.

**Incident timeline (P/O/A):**

| Step | What happened | Evidence label | Exact quote / artifact pointer |
|---|---|---|---|
| Trigger / first signal | | | |
| Expected behaviour | | | |
| Actual behaviour | | | |
| Detection | | | |
| Investigation | | | |
| Workaround / resolution | | | |
| Outcome / consequence | | | |

### C. Diagnostics, compliance and downstream systems (8 minutes)

11. "What did the Flow run history show you?"
12. "Could you see the exact values evaluated by the relevant condition?"
13. "Did you need to inspect another app, an API payload, a spreadsheet or a developer log?"
14. "Were shipping rules, product restrictions, tax, marketing consent, or other compliance requirements involved? Which ones were actually relevant to this incident?"
15. "What checks do you perform before an automation sends information or triggers an external action?"
16. "What has worked well in your current process?"

**Notes (P/O):**

- Relevant data field(s):
- Source of field value:
- Expected value / representation:
- Actual value / representation:
- Native Flow information available:
- Downstream system behaviour:
- Compliance or consent considerations (only if relevant):
- Current workaround:
- Remaining gap:

### D. Frequency, impact and trust (5–7 minutes)

17. "How often do cross-border exceptions like this happen?"
18. "What is the consequence when one is missed or takes too long to resolve?"
19. "How do you currently prevent the same problem from recurring?"
20. "Which automated actions are safe to run without review, and which need approval?"

- Frequency (participant wording):
- Time period:
- Detection time:
- Diagnosis time:
- Resolution time:
- Orders / markets affected (participant estimate):
- Operational / customer / financial consequence:
- Confidence in estimate and basis:

### E. Optional task walkthrough (5–8 minutes)

Using redacted historical data or a test store, ask the participant to show how they would inspect a country-specific workflow condition and trace the relevant value through to the action.

Prompts:

- "Where would you look to confirm the value being evaluated?"
- "How would you check whether the condition matched?"
- "How would you tell whether the failure was in Flow or the downstream app?"
- "What would need to see before changing the workflow?"

Do not create live cross-border orders or trigger live carrier, customer-message, tax or fulfillment actions.

**Observed task results (O):**

- Task attempted:
- Steps taken:
- Hesitation / errors / backtracking:
- Data visible:
- Data missing:
- Task completed? Why / why not?

## 5. Researcher synthesis — complete after interview

- Most important job-to-be-done:
- Incident category: Trigger / Condition / Action / Connector / Value normalisation / Stale data / Compliance / Business outcome / Other / No incident recalled
- Strongest evidence:
- Strongest disconfirming evidence:
- Root cause confirmed, suspected, or unknown?
- Was the issue specific to cross-border operations or more general data mapping?
- Did the participant need explanation, prevention, recovery, alerting, configuration assistance, or training?
- What would need technical access to solve it?
- Researcher interpretation (I), clearly separated from P/O/A:
- Follow-up questions:
- Confidence: Low / Medium / High, with reason:

## 6. Do not conclude from this interview alone

This interview cannot establish how common ISO-code mismatches are, whether cross-border merchants are a priority market, willingness to pay, or that a standalone validator is required. A participant who has never experienced this failure is useful counterevidence.
