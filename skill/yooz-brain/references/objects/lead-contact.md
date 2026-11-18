# Lead & Contact

*Status: Drafted · Sources: inventory (Lead & Contact items), Lead glossary 2026-11-09, Salesforce schema 2026-11-09/10, Greg (voice notes) · Validator: Chris*

**Scope.** These are the fields Yooz uses day to day. The full Lead glossary is in `sources/glossaire_lead_2026-11-09.tsv` of the project, not in the skill.

**Reading rule.** Read a Lead until it is converted, then read its Contact. Company fields live on the Lead before conversion and on the **Account** after it (the Account is the source of truth). Many fields have a different API name on Lead and on Contact: always use the name for the object you query.

"Sync" below is the glossary's HubSpot sync rule. `SF` = Always use Salesforce · `SF unless blank` = Prefer Salesforce unless blank · `2-way` = Two way.

## Identifiers

| Field | Lead | Contact | Note |
|---|---|---|---|
| Record ID | `Id` | `Id` | 15- or 18-character IDs: always join on the same format |
| Account | — (`ConvertedAccountId` after conversion) | `AccountId` | |
| Created Date | `CreatedDate` | `CreatedDate` | Use the standard field. `Date_de_cr_ation_piste_f__c` and `Date_de_creation_piste__c` are deprecated duplicates |

## Owner and territory

### Sales Territory
- API: Lead `SalesTerritory__c` · Contact `Sales_Territory__c` ("Contact Territory"), plus `TECH_AccountSalesTerritoryAuto__c` (the Account's territory) · Master: Salesforce (sync: SF)
- Yooz definition: **the NORAM filter** (`= 'NORAM'`).
- Pitfall: Contact territory and Account territory differ for about 2,000 NORAM contacts (2026-11-10). [TO CONFIRM: filter Contacts on their own territory or on the Account's?] `SalesTerritoryIsLocked__c` (Lead) allows a territory different from the country.

### Owner
- API: `OwnerId` (Lead and Contact; 18 characters) · Lead also has `Owner_ID__c` (15-character copy, sync: SF) · Master: Salesforce
- Pitfall: when joining to an export, match the ID length (`OwnerId` 18 characters vs `Owner_ID__c` 15).

### Country
- API: Lead `Country` · Contact `MailingCountry` · Master: Salesforce (sync: SF unless blank)
- Do not use it to filter a region: use Sales Territory.

## Lifecycle
Meaning of statuses and transitions: lead-journey.md. Scoring: scoring.md.

### Lead Status / Lifecycle Status
- API: Lead `Status` (HubSpot `leadstatus`, sync: SF unless blank) · Contact `LifecycleStatus__c` · Master: Salesforce
- Values: query with API values (`Suspect`, `MEL`, `MQL`, `Recycle`, `Purge`, `Converti`…). Contact values add `SAL`, `SQL`, `MEC`, `MQC`, `SAC`, `SQC`, `Customer Nurture`.
- Pitfall: on a Contact, SAL/SQL/customer stages follow the opportunity only for its primary contact.

### Status Reason / Lifecycle Status Reason
- API: Lead `leadStatusReasonWhy__c` (sync: SF) · Contact `ContactStatusReasonWhy__c` · Master: Salesforce
- Yooz definition: why a lead was recycled or put out.
- Values: the API value carries the outcome in its prefix. `nurture_*` = kept for later, e.g. `nurture_noresp` "Not Reached", `nurture_prj6M` "Follow-up in 6 months", `nurture_tbn_marketing` "To be nurtured by Marketing". `out_*` = lost, e.g. `out_not_target`, `out_competitor`, `out_fake`, `out_leftCompany`. Contact only: `recycle_churned`, `recycle_lost_opportunity`.
- Use for: reasons for Recycle/Purge. Group on the prefix (`nurture_` vs `out_`), not on labels.

### MEL / MQL / Recycle first & last entry date
- API: `FirstEntryDateMEL__c`, `LastEntryDateMEL__c`, `FirstEntryDateMQL__c`, `LastEntryDateMQL__c`, `FirstEntryDateRecycle__c`, `LastEntryDateRecycle__c` · Same names on Lead and Contact · Master: Salesforce (sync: SF)
- Use from: 2025-10-01 (2025-09-20 is a migration stamp).
- Use for: dating funnel entries. First = new, Last = most recent (re-MQL after Recycle).
- Pitfall: on a converted person, Lead and Contact may both carry a date. Count once, on the Contact.

### EMEA-style entry dates (history before 2025-09-20)
- API: `FirstNurtureMarketingStatusEntryDate__c` / `LastNurtureMarketingStatusEntryDate__c` (Mktg Nurture ≈ MEL, from 2025-04) · `Date_First_Nurture_Status__c` / `Date_Statut_Nurture__c` / `LastSDRNurtureStatusEntryDate__c` (SDR Nurture ≈ MQL, from 2024-04) · `Date_First_Lead_Lead_Status__c` / `Date_Dernier_PassageLeadLead__c` (Lead Lead ≈ SAL, from 2024-04) · Lead only · Master: Salesforce (sync: SF)
- Use for: status volumes before 2025-09-20 only. Never for funnel rates across that date.

### First Activation Date
- API: `Date_statut_piste__c` (date, HubSpot `date_statut_piste__c`, sync: SF unless blank) and `FirstActivationDate__c` (datetime) · Lead only · Master: Salesforce
- Yooz definition: first entry into an engaged status (MEL or MQL in NORAM; Nurture or Lead Lead in EMEA). Long-standing field. `Lead_activation_status_Pick__c` stores which status it was.

### Behavioral Score
- API: `BehavioralScore__c` (Lead and Contact) · HubSpot `noram_behaviour_score_fro` · Master: HubSpot (sync: 2-way)
- See scoring.md. Pitfall: never infer the status from the score.

## Source and attribution
Rules in attribution.md.

| Field | Lead | Contact | Sync | Note |
|---|---|---|---|---|
| First Touch Lead Source | `LeadSource` | `LeadSource` | SF unless blank | First real interaction; placeholder `3rd Party Database` for imports |
| Last Touch Lead Source | `Last_Touch_Lead_Source__c` | `Last_Touch_Lead_Source__c` | SF | Updated at every interaction |
| First Touch Request Type | `FirstTouchRequestType__c` | `FirstTouchRequestType__c` | SF | First Request Type, excluding imports and "Other" |
| Last Touch Request Type | `Type_de_demande__c` | `Type_de_demande__c` | 2-way | HubSpot "Request Type" (`type_de_demande__c`). **Not scored** |
| UTM Campaign / Source / Term | `utm_campaign_lead__c`, `utm_source_lead__c`, `utm_term_lead__c` | `utm_campaign_contact__c`, `utm_source_contact__c`, `utm_term_contact__c` | 2-way | Last form only, overwritten each time |
| UTM Content / Medium | `utm_content_lead__c`, `utm_medium_lead__c` | **do not exist** | 2-way | Use the Lead, or the Campaign Member UTMs |
| Referral Partner | `Apporteur_d_affaires__c` (→ Account) | — (Account `Apporteur_d_affaires__c`) | 2-way | |
| Reseller | `Revendeur_ou_int_grateur__c` (→ Account) | — (Account `Compte_Partenaire__c`) | 2-way | [TO CONFIRM: difference from the other partner fields] |

## Interaction

### Last HubSpot Demand Date Yooz
- API: `Last_Hubspot_Demand_Date_Yooz__c` (Lead and Contact) · HubSpot `lastdemanddateyooz` · Master: HubSpot (sync: 2-way)
- Yooz definition: date of the last real form (newsletter and subscribe/unsubscribe excluded). Filled since 2024-02.
- Pitfall: do not use `Date_Derni_re_Demande__c` ("Last HubSpot Demand Date"): it includes the newsletter.

### Last Activity Date
- API: custom `Date_de_Derni_re_Activit__c` (Lead datetime, Contact date; sync: SF) and standard `LastActivityDate` · Master: Salesforce
- Pitfall: the two are not the same field (CRM team). [TO CONFIRM: which one tracks the last Sales activity?]

## Person

| Field | Lead | Contact | Sync | Note |
|---|---|---|---|---|
| Email | `Email` | `Email` | SF unless blank | Salesforce allows duplicates; HubSpot has one contact per email |
| First / Last Name | `FirstName`, `LastName` | same | 2-way | |
| Job Title | `Title` | `Title` | SF unless blank | Free text |
| Job Function | `Fonction__c` | `Fonction__c` | 2-way | Picklist with French API values (e.g. `Comptable` = Accountant). Drives Persona Score |
| Job Department | `Departement__c` | `D_partement__c` | 2-way | **Different API name** on Contact |
| Seniority | `SeniorityLevel__c` | `SeniorityLevel__c` | 2-way | C-Level / Director / VP · Manager · Non-Manager |

## Company (Lead only; on the Account after conversion)

| Field | Lead API | Account API | Sync | Note |
|---|---|---|---|---|
| Vertical | `VerticalPick__c` | `VerticalPick__c` | 2-way | NORAM target verticals (Automotive Dealer, Construction, Heavy Truck Dealer, Manufacturing…) |
| Macro Industry | `MacroIndustryPick__c` | `MacroIndustryPick__c` | — | Derived from the industry code (NAICS for NORAM) |
| NAICS code | `NAICS_code__c` | `NAICS_code__c` | SF unless blank | |
| ERP / Other ERP | `ERP__c`, `Autre_ERP__c` | `ERP__c`, `Autre_ERP__c` | 2-way | HubSpot spreads ERP over 4 properties (`erp_en`, `erp_en_us`, `erp_sp`, `erp__c`) |
| Company Staff | `Effectif_de_la_societe__c` | same | SF unless blank | Ranges (picklist) |
| Monthly Invoice Volume | `Volume_de_facture__c` | `Volume_de_facture__c` | SF unless blank | Ranges (picklist). Not `Volume_de_facture_trait_es_dans_le_mois__c` (actual monthly usage, level 2) |
| Revenue Range | `Fourchette_de_Revenu__c` | `Fourchette_de_Revenu__c` | SF unless blank | |
| Website | `Website` | `Website` | SF unless blank | Possibly redundant with the email domain |

Pitfall: do not read company fields on a converted Lead. They froze at conversion, and the Account is the source of truth.

## Conversion (Lead side)

| Field | API | Note |
|---|---|---|
| Converted Date | `ConvertedDate` | Not `Date_de_Conversion__c` (deprecated duplicate) |
| Converted Account / Contact / Opportunity | `ConvertedAccountId`, `ConvertedContactId`, `ConvertedOpportunityId` | Opportunity is empty for an automatic conversion to an existing Account |
| Contact → original Lead | Contact `TECH_ConvertedLeadId__c` (text) | Empty if the contact was created directly |
