# Pitfalls — mistakes an AI makes on Yooz data

*Status: Drafted · Sources: Greg (voice notes 2026-11-09/10), Salesforce and HubSpot checks 2026-11-09/10, inventory · Read this file before computing any metric.*

Each pitfall gives the **symptom** (what the AI would wrongly conclude) and the **correction**. Field-level traps stay in their field cards (objects/*.md).

## Funnel and statuses

**F1 — "Converted" is not a stage.**
- Symptom: `Status = 'Converti'` is counted as leads that progressed, or as opportunities.
- Correction: conversion only means the lead was attached to an Account, which can happen at MEL or MQL with no opportunity. Read the Contact's `LifecycleStatus__c` for the stage, and the Opportunity for pipeline.

**F2 — Suspects are not leads.**
- Symptom: rates computed on all NORAM leads look absurdly low (about 1.84M of 2.14M are `Suspect` on 2026-11-09).
- Correction: MCLs never interacted with Yooz. Start acquisition funnels at MEL, unless the question is about database reach or Probance.

**F3 — Purge/Out pollute conversion metrics.**
- Correction: report Purge (NORAM) / Out (EMEA) as volumes only. Exclude them from every rate, opportunity or revenue analysis.

**F4 — Leads and contacts double-counted.**
- Correction: after conversion, count the Contact only. Lead and Contact may both carry the same entry dates.

**F5 — Re-MQLs.**
- Symptom: counts based on `FirstEntryDateMQL__c` miss leads that came back from Recycle.
- Correction: first entry = new MQLs, last entry = most recent MQL. Say which one you used.

**F6 — The 2025-09-20 spike.**
- Symptom: "September 2025 was a record month for MQLs."
- Correction: the spike is a migration stamp (6,263 NORAM MQL first entries on that single day). Start MEL/MQL trends on 2025-10-01. Smaller spikes until mid-October 2025 (323 MEL on 2025-10-13, 136 MQL on 2025-10-15) are probably catch-ups: count them, but flag them as uncertain.

**F7 — Funnels compared across the migration.**
- Symptom: "the MQL→SAL rate improved after September 2025."
- Correction: before 2025-09-20, NORAM used EMEA-style statuses (SDR Nurture ≈ MQL, Marketing Nurture ≈ MEL) with no scoring and different rules. Status volumes can be compared across that date; funnel rates cannot.

**F8 — Legacy MQLs in the current stock.**
- Symptom: "we have 768 MQLs today" (NORAM, 2026-11-10).
- Correction: at the migration, SDR Nurture leads became MQLs without today's scoring. Report MQLs that entered before 2025-10-01 separately as "legacy MQLs" (250 of 768).

**F9 — Account-level funnel read from the wrong contact.**
- Symptom: "the account is only MEL" while its opportunity is at stage 3.
- Correction: only the opp's primary contact (`Opportunity.Interlocuteur__c`) follows the opp (SAL → SQL → Customer Nurture). For an account, read the opportunity, or that contact.

**F10 — Customers mixed into acquisition.**
- Correction: contacts in `MEC`, `MQC`, `SAC`, `SQC` or `Customer Nurture` belong to client accounts. Keep them, but report them apart from new business.

**F11 — Regions compared as if equivalent.**
- Correction: NORAM and EMEA statuses have equivalents, but different transition rules. Say so in any cross-region comparison.

## Scoring

**S1 — Status inferred from the score.**
- Symptom: "these MQLs are under 100, so they are not really MQLs."
- Correction: MQL stays MQL until Recycle or a manual move forward. On 2026-11-10, 285 of 768 NORAM MQLs scored below 100 (174 at 0, which comes from decay or an unfixed bug). Read the status, never the score.

**S2 — Score read as intensity.**
- Correction: 100 is a threshold, not a cap. 125 and 175 are both simply MQL, so never rank MQLs by score. A Suspect's 0 means "not scored", not "inactive".

**S3 — A score explained by adding up the rules.**
- Correction: duplicates, group caps and decay (−50% every 6 months) make sums unreliable. Explain a score from the contact's Request Type properties and dated events. Score history is not available through the connector: date an MQL with its entry dates.

## Attribution

**A1 — Primary Campaign Source used alone.**
- Symptom: "this opp came from the cold-phoning campaign."
- Correction: its logic (first or latest campaign) is unknown. Default to the opp's First Touch Lead Source, and cross-check Campaign Members and request dates when asked which campaign generated it (e.g. Gengras: `Sales_US_ColdPhoning` while a Google Ads demo request came first).

**A2 — `Advertising` read as an ad channel.**
- Correction: it was the fallback value when the source was missing (15,233 NORAM MEL+ leads), so flag it whenever it weighs in a result. `To be defined` is the newer fallback.

**A3 — `Created by Yooz application` counted as acquisition.**
- Correction: these are app users added by clients (Contacts on client accounts), not leads. Exclude them.

**A4 — `3rd Party Database` on an engaged lead.**
- Correction: purchased/imported leads keep this placeholder only while MCL. On MEL or higher it is a data anomaly: report it, do not attribute to it.

**A5 — Opp source trusted on old opps.**
- Correction: First Touch has been locked against manual edits only since about June–July 2026. On older opps, say that SDRs may have edited it.

**A6 — Lead UTMs used for a past campaign.**
- Correction: Lead `utm_*` fields hold the last form only and are overwritten each time. For a given campaign, use the Campaign Member UTMs, which are frozen at member creation.

**A7 — Opp and Account First Touch mixed up.**
- Correction: the Opps export carries both. Use `Opportunity.LeadSource` for the opp.

## Campaigns

**C1 — Responded used as engagement.**
- Correction: `HasResponded` is automatic and unused. Read Campaign Member Status and state which statuses you counted (there is no global rule).

**C2 — Operational campaigns counted.**
- Correction: exclude `Type = 'Operational'` campaigns, which Ops uses for technical work in Salesforce.

**C3 — Campaign territory filtered with `=`.**
- Correction: Campaign `Sales_Territory__c` is a multi-select picklist. Filter with `INCLUDES ('NORAM')`.

**C4 — Campaign names parsed blindly.**
- Correction: the Name Generator convention is reliable for recent names, but older names do not follow it. Confirm with campaign fields (Type, Objective, Target) before grouping.

## Opportunities and revenue

**O1 — Stage read as a step.**
- Correction: `StageName` is the state (In progress, Won, Lost…). The step is `SalesStagePick__c` (Stage 1 to 5).

**O2 — Cancelled opps counted.**
- Correction: Cancelled = created by mistake (a clean duplicate exists). Exclude them from counts and win rates.

**O3 — Currencies summed raw.**
- Correction: NORAM opps mix USD, CAD and EUR. Use the converted 1-Year Recurring Amount and state the currency (USD for NORAM).

**O4 — No primary contact.**
- Correction: about 31% of NORAM opps have no `Interlocuteur__c`. Say the person's journey can't be followed; do not pick a Contact Role instead.

**O5 — New vs existing client guessed.**
- Correction: `Type` `Souscription Yooz` = new client, `Vente additionnelle` = existing client. Cross-check with the Account (the Key Auto case: a client since 2023).

## Sync and data quality

**D1 — Missing in Salesforce = sync bug.**
- Correction: HubSpot contacts sync only through the inclusion list (a form-type Request Type or a Salesforce Account/Lead/Contact ID is required). BookIt meetings without a form may have no Lead until the SDR creates one. Match those by name, email or company before concluding "no demo".

**D2 — One person = one Salesforce record.**
- Correction: Salesforce allows several Leads/Contacts with the same email, while HubSpot has one contact per email. Deduplicate on email when counting people across systems.

**D3 — Picklist labels used in queries.**
- Correction: API values are often French (`Converti`, `Piste`, `Souscription Yooz`, `Rendez-vous personnalisé`, `Comptable`…). Query with API values.

**D4 — Same field, different API name per object.**
- Correction: Sales Territory, Job Department, Country, UTMs and partner fields differ between Lead, Contact, Account and Opportunity. Check objects/*.md before querying.

**D5 — Activities classified from one field.**
- Correction: Task/Event `Type`, `Type_de_T_che__c`, `Subtype__c` and `Subject` are used inconsistently. Read them all before calling something a demo, a call or an email.

**D6 — Partners and test accounts silently included.**
- Correction: always exclude `Test` accounts. Keep partners, but say whether they are included, and show the result with and without them when it matters.

**D7 — Account Prospect/Client Status trusted.**
- Correction: read only the `Prospect…` / `Client…` prefix, and confirm with won opps.

**D8 — Wrong demand date.**
- Correction: use `Last_Hubspot_Demand_Date_Yooz__c`. `Date_Derni_re_Demande__c` includes the newsletter.
