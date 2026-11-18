# Attribution

*Status: Drafted · Sources: Greg (voice notes 2026-11-10), Salesforce schema and counts 2026-11-09/10, inventory · Validator: Chris*

## Default rule
- **An opportunity is attributed to the First Touch Lead Source carried by the opportunity** (`Opportunity.LeadSource`), i.e. the value at opp creation.
- First Touch has not been editable manually since about June–July 2026 (exact date unknown). Before that, some SDRs changed it at conversion, so for opps created earlier, say that the source may have been edited.
- The source alone does not always explain an opp. When the question is "which campaign really generated it", cross-check with Campaign Members and request dates. Gengras is an edge case, not the norm (see Primary Campaign Source).

## First Touch vs Last Touch

**First Touch Lead Source** (`LeadSource` on Lead, Contact, Opportunity and Campaign Member; `Origine_de_la_piste__c` on Account)
- Meaning at Yooz: the **first real interaction**. A purchased or imported lead is created with a placeholder (usually `3rd Party Database`). It should be overwritten at the first real interaction (e.g. a form), then never change again.
- Pitfall: an MEL or higher still in `3rd Party Database` is a data anomaly. Check on 2026-11-10: 0 NORAM leads in MEL, MQL or Recycle carry it.

**Last Touch Lead Source** (`Last_Touch_Lead_Source__c`; `LastTouchLeadSourcePick__c` on Campaign Member)
- Updated at every new interaction (form submission, trade show import…).

**Account**: always use First Touch / Last Touch (`Origine_de_la_piste__c`, `Last_Touch_Lead_Source__c`). Do not use `AccountSource` (believed unused).

## First Touch values that need a definition

| Value (API) | Meaning at Yooz |
|---|---|
| `3rd Party Database` | Leads bought from a database or imported from outside. MCL placeholder. |
| `Outsourced LeadsGen` | Leads delivered by services Yooz pays to generate leads (e.g. content syndication). The largest source of NORAM MEL+ leads (16,817 on 2026-11-10). |
| `Advertising` | In theory, Yooz promoted by a third party. In practice it was **the old fallback when the source was missing**, so quality is poor: 15,233 NORAM MEL+ leads. Say so whenever it weighs in a result. It was replaced as the fallback on First Touch around Aug–Sep 2026 (per Greg, maybe on Last Touch too), but the data shows no drop yet: still ~230 new NORAM leads/month in Oct 2026, and Last Touch still holds 1,978 NORAM `Advertising` leads created in 2026. [TO CONFIRM with Chris: is the new fallback live?] |
| `To be defined` | Current fallback: no system could assign a source. Rarely seen so far (12 leads created in 2026 on First Touch, all territories; none on Last Touch). |
| `Partner` | Comes from a partner. |
| `Tradefair` | Comes from a trade show. |
| `Created by Yooz application` | **Not a lead.** A user added by a client inside the Yooz app (e.g. a new accountant), loaded automatically into Salesforce as a Contact (never a Lead) on the client's account. If that person later fills a form, LeanData deduplicates them and they stay a customer contact. Exclude from acquisition. |
| `Web Referral`, `ITESOFT leads` | No longer used. |

## Other attribution fields
- **Primary Campaign Source** (`Opportunity.CampaignId`): set by Salesforce, but whether it picks the first or the latest campaign is unknown, so reliability is unknown. Never use it alone. In Gengras, it pointed to `Sales_US_ColdPhoning` while a Google Ads demo request came first.
- **Lead Source - Detail** (`Origine_de_la_piste_precision__c`): free text, mostly filled by imports. Use it for context, not for grouping.
- **Request Type**: First Touch (`FirstTouchRequestType__c`) or Last Touch (`Type_de_demande__c`). The highest-intent values are `Request a demo` and `Rendez-vous personnalisé` (label "Personalized meeting"). [TO ADD: full weights from the scoring export]
- **UTM** (`utm_*_lead__c` on Lead): last form only, overwritten each time. On Campaign Member (`Utm*__c`): frozen at member creation. Use Campaign Member UTMs for a given campaign.

## Campaigns
- Engagement is read from **Campaign Member Status**, not from `HasResponded` (Responded is set automatically by Salesforce and Yooz does not use it).
- Exclude campaigns of `Type = 'Operational'` from every analysis: they are Ops technical campaigns.
