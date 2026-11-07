# M1 — Apparel Operations Lead: Interview Capture Sheet

**Status: PRE-INTERVIEW TEMPLATE — no interview has been conducted or findings established.**

## 1. Research intent

Understand how an apparel operations lead uses Shopify Flow and connected shipping/fulfillment systems to classify orders, apply tags, generate or route shipping labels, and detect mistakes.

**Target-profile hypothesis (H, not a fact):** M1 may have experienced a tag/condition issue that affected shipping-label routing or required manual correction.

Do not tell the participant this hypothesis before hearing their unaided account. Do not assume that a wrong label, silent failure, or operational loss occurred.

## 2. Session details

- Participant ID: M1
- Date / time / timezone:
- Interviewer:
- Participant role and responsibilities:
- Store context (participant-described):
- Order-volume range (optional; self-reported):
- Team involved in order operations:
- Shopify Flow experience: Never used / Tried / Active user / Maintains others' workflows
- Connected systems named by participant:
- Recording permission: Yes / No
- Note-taking permission: Yes / No
- Quote permission: Yes / No / Not asked
- Artifacts available with permission: None / Redacted screenshot / Redacted run log / Workflow export / Other

## 3. Neutral opening script

"Thanks for speaking with me. I'm studying how ecommerce teams manage operational automations and troubleshoot them when an outcome isn't expected. I'm interested in a real recent example, including what worked well. This isn't a test of you or your setup. Please avoid sharing customer-identifying or confidential information. May I take notes?"

## 4. Interview flow and prompts

### A. Role and operational context (5 minutes)

1. "Could you walk me through your role in the store's order and fulfillment operations?"
2. "Which systems do you use between an order being placed and a shipping label being created?"
3. "Which parts of that process are automated today, and which are still manual?"

**Notes (P):**

- Role / responsibilities:
- Systems and handoffs:
- Current automation:
- Manual steps:

### B. Recent-incident reconstruction (12–15 minutes)

Start broad:

4. "Think about the most recent time an order did not move through your normal fulfillment process as expected. What happened?"
5. "What was the first sign that something was wrong?"
6. "Can you take me through what happened, step by step, from the original order event to resolution?"
7. "What did you expect the system to do, and what did it actually do?"
8. "How did you work out where in the process the difference occurred?"
9. "What did you do next, and who else was involved?"
10. "What was the operational consequence, if any?"

Only after the participant has described the incident unaided, probe where relevant:

- Was a tag expected to be added, removed, or used for routing?
- Was the tag present, absent, misspelled, duplicated, or applied at a different time than expected?
- Did the workflow trigger? Did the condition evaluate as expected? Did an action run? Did a connected system accept the action?
- Was a shipping label created, delayed, duplicated, or routed incorrectly?
- What did the Flow run history show? Which status, path, result, log, or step data did they inspect?
- Did a connected app, carrier, 3PL or staff permission contribute?
- Was the data current at the moment the automation evaluated it?

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

### C. Current diagnostics and alternatives (8 minutes)

11. "What information did Shopify Flow show you when you inspected the run?"
12. "Was that enough to identify the cause? What did you still need to check elsewhere?"
13. "Do you use spreadsheets, notifications, app logs, manual checks, agency support, or another tool to catch these issues?"
14. "Have you changed the workflow or your operating process since then?"
15. "What is working well in your current setup that you would not want to lose?"

**Notes (P/O):**

- Native Flow details consulted:
- Other diagnostic surfaces:
- Current workaround:
- Workaround cost / steps (participant-estimated):
- Existing capabilities that solved the problem:
- What remains unresolved:

### D. Frequency, impact and trust (5–7 minutes)

16. "Has this happened before? How many times do you recall, and over what period?"
17. "What happens if this type of issue is not caught quickly?"
18. "How do you decide whether a workflow is safe to leave running?"
19. "Which actions, if any, require a person to review before they happen?"

Capture frequency and impact as self-report. Ask for a concrete example, period and basis; do not turn an estimate into an independently verified metric.

- Frequency (participant wording):
- Time period:
- Detection time:
- Diagnosis time:
- Resolution time:
- Orders / shipments affected (participant estimate):
- Customer / financial / operational consequence:
- Confidence in estimate and basis:

### E. Optional task walkthrough (5–8 minutes)

Ask the participant to demonstrate how they would inspect a **redacted historical run or a test-store example**. Think-aloud prompts:

- "What are you looking for here?"
- "What would you inspect next?"
- "What do you think this status means?"
- "What would make you confident you had found the cause?"

Do not trigger a live shipping, fulfillment, customer notification, refund or external webhook action.

**Observed task results (O):**

- Task attempted:
- Steps taken:
- Hesitation / errors / backtracking:
- Information sought:
- Information available:
- Task completed? Why / why not?

## 5. Researcher synthesis — complete after interview

- Most important job-to-be-done:
- Incident category: Trigger / Condition / Action / Connector / Stale data / Business outcome / Other / No incident recalled
- Strongest evidence:
- Strongest disconfirming evidence:
- Is the issue in native Flow, the connected system, the merchant's configuration, or unresolved?
- Did the participant need explanation, prevention, recovery, alerting, or training?
- Is the pain recurring and consequential, or a one-off edge case?
- What would need technical access to solve it?
- Researcher interpretation (I), clearly separated from P/O/A:
- Follow-up questions:
- Confidence: Low / Medium / High, with reason:

## 6. Do not conclude from this interview alone

This interview cannot establish market prevalence, a segment-wide failure rate, willingness to pay, or that a separate product is required. A successful current workaround or a participant reporting that Flow works well is valid counterevidence.
