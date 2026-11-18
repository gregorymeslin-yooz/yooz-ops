---
name: yooz-brain
description: Yooz GTM data brain. Use for any question about Yooz leads, funnel, lead statuses, sources and attribution, campaigns, opportunities, or Salesforce/HubSpot fields and exports (NORAM first).
---

# Yooz Brain

*Status: Draft — finalised last (see 03). Golden rules are candidates until the tests pass.*

## Golden rules
1. Stage = Lead `Status`, then the Contact's `LifecycleStatus__c` once converted. Never HubSpot `lifecyclestage`. After conversion, follow the Contact only.
2. Filter NORAM with Sales Territory. The API name and type differ per object: Lead `SalesTerritory__c`, Contact/Account/Opp `Sales_Territory__c`, Campaign multi-select (`INCLUDES`).
3. Query picklists with API values (`Converti`, `Suspect`, `Souscription Yooz`), not labels.
4. Do not trend MEL/MQL dates before 2025-10-01. The 2025-09-20 spike is a migration stamp.
5. Funnels start at MEL. Suspects (MCL) never interacted. Purge/Out count as volumes only, never in rates. `Converti` is not a stage.
6. Opportunity: `StageName` = state, `SalesStagePick__c` = step, `convertCurrency(Montant_recurrent_1an__c)` = amount (state the currency), `Type` = new (`Souscription Yooz`) vs existing (`Vente additionnelle`). Exclude Cancelled opps.
7. Default attribution = the opp's First Touch Lead Source. Never conclude from Primary Campaign Source alone. Treat `Advertising` as a low-quality fallback, and remember that `Created by Yooz application` is not a lead.
8. Engagement in campaigns = Campaign Member Status, not Responded. Exclude `Operational` campaigns and `Test` accounts.
9. Join Salesforce IDs on the same format (15 or 18 characters). [TO CONFIRM: wording]

## Routing
| Question about… | Read |
|---|---|
| Statuses, funnel, MQL | references/lead-journey.md |
| Why a lead has its score, what gives points | references/scoring.md |
| Sources, which campaign generated a lead or opp | references/attribution.md |
| HubSpot vs Salesforce, sync, Probance, BookIt | references/hubspot-salesforce.md |
| A specific field | references/objects/*.md |
| "Since when can I trust…" | references/reliability.md |
| **Any metric, count or rate** | **references/pitfalls.md, always, in addition to the topic file** |

## How to answer
- Name the fields and objects used, and their reliability.
- Show counts (n), and say what was excluded and why.
- Partners: keep them, but say whether they are included. When it matters, show the result with and without partners (`Type_de_Piste__c` / Partner source).
- Default period: 2026 onwards, unless the question says otherwise. Report legacy MQLs (entered before 2025-10-01) separately.
- Flag limits instead of filling gaps.
