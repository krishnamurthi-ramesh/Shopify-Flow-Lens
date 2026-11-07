# Merchant & Developer Participant Recruitment Shortlist (Track A)

**Project:** Shopify Flow + Sidekick AI Teardown & Opportunity Validation  
**Deliverable:** Customer Discovery Protocol — Deliverable 3 (Track A)  
**Status:** Active Recruitment & Scheduling Phase  
**Research Stance:** Neutral, exploratory, incident-reconstruction focus (Zero solution pitching)  

---

## 🎯 Recruitment Targets & Composition Matrix

We recruit for **automation operational friction**, not arbitrary GMV tiers:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        Target: 8 Total Interviews                      │
├──────────────────────────────────────┬─────────────────────────────────┤
│    4–6 Merchant Operators / Ops      │   2–3 Shopify Devs / Agencies   │
│  (Direct exposure to order/tag bugs) │  (Multi-store architecture/app) │
└──────────────────────────────────────┴─────────────────────────────────┘
```

| ID | Segment Profile | Workflow Context | Desired Screening Signal | Status |
|---|---|---|---|---|
| **M1** | Scaling Apparel Ops Manager | 12 active flows; ShipStation + Klaviyo | Experienced silent tag failure leading to wrong shipping label | Shortlisted |
| **M2** | Cross-Border Cosmetics Founder | 5 active flows; Multi-currency, ISO codes | Hit string mismatch bug on international shipping conditions | Shortlisted |
| **M3** | High-Volume B2B/Wholesale Lead | 20+ flows; Draft orders, net-30 terms | Struggled with race conditions on customer tag updates | Shortlisted |
| **M4** | D2C Subscription Brand Ops | 8 active flows; Recharge + Flow | Experienced delayed email triggers & stale order status | Shortlisted |
| **M5** | Custom Merchandise Operator | 4 active flows; High return/exchange rate | Spends 30+ mins weekly manually cross-checking Flow run history | Shortlisted |
| **D1** | Shopify Plus Agency Tech Lead | Configures Flow for 15+ merchant stores | Manages "Template Ceiling" complaints; writes custom apps | Shortlisted |
| **D2** | Independent Shopify App Dev | Built custom `flow_action` integrations | Deep knowledge of Flow extension framework & API boundaries | Shortlisted |
| **D3** | Ecommerce Ops Consultant | Audits store automations & Zapier migrations | Experienced with merchants migrating between Zapier and Flow | Shortlisted |

---

## 🔍 Sourcing Channels & Exact Search Queries

### 1. LinkedIn Boolean Search Strings
```text
("Shopify" OR "Shopify Plus") AND ("Operations Manager" OR "Head of Ecommerce" OR "Store Operations") AND ("automation" OR "Shopify Flow")
```
```text
("Shopify Agency" OR "Shopify Partner") AND ("Solutions Architect" OR "Shopify Developer") AND ("Shopify Flow" OR "Klaviyo")
```

### 2. Community & Reddit Targeting (Discussions R1–R25 Authors)
Direct outreach to verified posters from our passive research log:
- **Observability cluster (R1, R13):** Operators asking for monitoring tools for silent failures.
- **Data type/ISO mismatch cluster (R8, R14):** Users debugging why country/state strings evaluated to False.
- **Third-party connector cluster (R18, R19, R21):** Users debugging Klaviyo consent and ShipStation scope drops.

---

## ✉️ Direct Outreach Message Templates

### Template A: For Store Owners & Operations Managers
> **Subject:** Quick question on Shopify Flow troubleshooting (AI PM research)  
>  
> Hi [Name],  
>  
> I came across your profile while researching operational workflows on Shopify. I’m an AI Product Management researcher conducting an independent study on how growing brands maintain and debug automations as store volume scales.  
>  
> I’m not selling an app or offering consulting. I'm investigating why Shopify automations sometimes silently take unexpected paths or fail condition checks, and how operators currently diagnose them in the Admin.  
>  
> Would you be open to a 25-minute chat to reconstruct a recent automation headache you encountered? I’d be glad to share an executive summary of our technical findings across 25+ merchant teardowns in return.  
>  
> Best regards,  
> [Your Name]

---

### Template B: For Shopify Agency Leads & App Developers
> **Subject:** Shopify Flow extension limits & diagnostic traces (independent research)  
>  
> Hi [Name],  
>  
> I noticed your expertise building advanced Shopify Plus solutions. I’m currently conducting an architectural teardown of Shopify Flow’s runtime engine, specifically around how merchants handle condition failures and 3rd-party connector drops (Klaviyo, Gorgias, 3PLs).  
>  
> We’re investigating the exact boundary between Shopify's native Admin UI and public GraphQL API capabilities regarding execution observability.  
>  
> Would you have 20 minutes this week to share how your agency handles client complaints when a workflow silently skips or misroutes? Zero sales pitch—strictly an engineering and PM teardown conversation.  
>  
> Best regards,  
> [Your Name]

---

## 📋 Participant Screening Scorecard

Before booking, verify that each respondent meets these 3 criteria:
1. **Active Automation Operator:** Personally creates, modifies, or investigates Shopify Flow workflows (not just a high-level executive who delegates).
2. **Recent Incident Experience:** Can recall a specific instance in the last 60 days where an automation did not produce the expected business outcome.
3. **No Commercial Conflict:** Not an employee of Shopify or a direct competitor promoting a specific proprietary observability tool.
