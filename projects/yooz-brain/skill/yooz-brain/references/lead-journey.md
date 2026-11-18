# Lead journey (NORAM)

*Status: Drafted · Sources: Greg (voice notes 2026-11-09/10, chat 2026-11-13), Salesforce schema and counts 2026-11-09/10, Lead glossary 2026-11-09 · Validator: Chris*

## Which status field to read
- A record's stage is the Lead `Status`, or `LifecycleStatus__c` once the lead is converted to a Contact. Never use HubSpot `lifecyclestage`: Yooz does not use it.
- **Once a lead is converted, follow the Contact only.** The Lead stays frozen at `Converti`.
- Query picklists with the API value, not the label (`Converti`, not "Converted"; `Suspect`, not "MCL").
- Statuses are set by a Salesforce flow, triggered when the Behavioral Score changes in HubSpot.

## Stages (NORAM nomenclature, live since 2025-09-20)

| Stage | Lead `Status` | Contact `LifecycleStatus__c` | Meaning at Yooz |
|---|---|---|---|
| MCL — Marketing Captured Lead | `Suspect` | `Suspect` | No interaction with Yooz. Mostly purchased databases (First Touch `3rd Party Database`), plus imported lists with no contact (e.g. a trade show's pre-registrants). Emailed only through Probance. |
| MEL — Marketing Engaged Lead | `MEL` | `MEL` | At least one real interaction: a form submission, or an import after a real exchange with someone from Yooz. An email open or click is not enough. |
| MQL — Marketing Qualified Lead | `MQL` | `MQL` | Behavioral Score ≥ 100. Enters SDR cadences (automated emails and calls). |
| Recycle | `Recycle` | `Recycle` | Set by the SDR, manually (not worth an opportunity) or automatically at the end of an unanswered cadence. The score resets to 0 at that moment. Still receives marketing emails. |
| SAL — Sales Accepted Lead | — | `SAL` | MQL qualified by the SDR and handed to an AE. The opportunity exists and is at Sales stage (US) 1 (`Stage10`, Qualification). |
| SQL — Sales Qualified Lead | — | `SQL` | The opportunity is at Sales stage (US) 2 to 5 (`Stage20` to `Stage50`; Closing is still SQL). Once won (`Stage100`), the contact becomes a customer (`Customer Nurture`). |
| Purge / Out | `Purge` (NORAM) · `Out` (EMEA) | `Purge` | Same meaning, different name per region: unusable records (fake data, person left the company…). |
| Converted | `Converti` | — | Technical state, not a stage (see P1). |

**Customer stages (Contact only).** "C" = Customer. A contact whose account becomes a client gets `Customer Nurture` by default. It moves to `MEC` (Marketing Engaged Customer) as soon as it interacts, then `MQC`, `SAC` and `SQC` follow the same logic as MQL/SAL/SQL. These contacts belong to accounts that already generated revenue (CSM/upsell territory). Keep them, but report them as customers and keep them apart from new-business acquisition.

**Only the opportunity's primary contact follows the opp.** SAL → SQL → Customer Nurture tracks the opp's stage for the primary contact only. Every other contact on the account keeps its own Lifecycle Status, which may be far behind. The primary contact is `Opportunity.Interlocuteur__c` (label "Contact"); see objects/opportunity.md. On upsell opps, the primary contact is in the customer equivalents (`SAC` at stage 1, `SQC` at stages 2 to 5).

**EMEA equivalents.** Same intent, different transition rules, so never compare funnels across regions without saying so. Before 2025-09-20, NORAM also used these EMEA values: SDR Nurture ≈ today's MQL and Marketing Nurture ≈ today's MEL, but there was no scoring and the transition rules were different. Status volumes can be compared across 2025-09-20; funnel conversion rates cannot.

| NORAM | EMEA value (label) |
|---|---|
| MEL | `Nurture Marketing` (Marketing Nurture / Inquiry) |
| MQL | `Nurture` (SDR Nurture) |
| SAL | `Piste` (Lead Lead) |
| Purge | `Out` |

## Transitions

```
MCL ──real interaction──▶ MEL ──score ≥ 100──▶ MQL ──SDR qualifies + manual conversion──▶ SAL (opp created) ──▶ SQL ──won──▶ Customer Nurture
                           ▲                    │
                           └── score back to 100 ◀── Recycle (SDR, score reset to 0)
```

- **MCL → MEL**: the existing record is updated; no new record is created. For trade shows, someone who only picked up a pen at the booth stays MCL, while someone who had a real conversation is at least MEL. Context is sometimes in `Description` (fed by External Notes).
- **→ MQL**: 100 points always means MQL, whether the lead comes from MEL or from Recycle. A demo request gives 100 points at once. [TO ADD: scoring export]
- **MQL → SAL**: the SDR converts the lead manually, which creates a Contact, an Account and an Opportunity at the same time. A qualified MQL is not supposed to stay unconverted.
- **Automatic conversion**: Salesforce converts a lead natively when it is attached to an existing Account. This can happen at any stage (MEL, MQL…) and creates no opportunity.

## Scoring
- Only the Behavioral Score drives status: `BehavioralScore__c` in Salesforce, `noram_behaviour_score_fro` in HubSpot. ≥ 100 = MQL. Rules and points are in references/scoring.md.
- `PersonaGradePick__c` (A–D, job function) and `CompanyScorePick__c` (AlignICP) exist but **do not affect status**. Neither does `TECH_IsAllQualifiedInfo_Auto__c` ("Ready for MQL"), despite its name.
- `Score_HubSpot__c` ("Probability Score"): believed unused. Ignore it. [TO CONFIRM with Chris]

## Entry dates and reliability
| Field | API (Lead) | Use from |
|---|---|---|
| MEL / MQL / Recycle first & last entry date | `FirstEntryDateMEL__c`, `FirstEntryDateMQL__c`, `FirstEntryDateRecycle__c`, `LastEntryDate…__c` | **2025-10-01** for analysis. Officially live on 2025-09-20, but 2025-09-20 itself is a migration stamp (6,263 NORAM MQL and 6,929 MEL first entries on that single day) and fixes continued in the following days. |
| First Activation Date | `Date_statut_piste__c` / `FirstActivationDate__c` | Long-standing, NORAM and EMEA. Before 2025-09-20 it captures entry into the EMEA-style statuses. |

## Pitfalls
Funnel pitfalls (Converted, Suspects, Purge, re-MQLs, migration spike, legacy MQLs…) are in pitfalls.md, section "Funnel and statuses".
