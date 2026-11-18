# Reliability — since when can a field be trusted

*Status: In progress · One line per field, the date the data can be used from. Details live in the topic files.*

| Field | API | Use from | Note |
|---|---|---|---|
| MEL / MQL / Recycle entry dates | `FirstEntryDate*__c`, `LastEntryDate*__c` | 2025-10-01 | Live on 2025-09-20; that day is a migration stamp. |
| NORAM status nomenclature (MCL/MEL/MQL/SAL/SQL) | Lead `Status`, Contact `LifecycleStatus__c` | 2025-09-20 | EMEA-style values before. |
| First Mktg Nurture entry date | `FirstNurtureMarketingStatusEntryDate__c` | 2025-04 | EMEA-style status. |
| First SDR Nurture / Lead-Lead entry date | `Date_First_Nurture_Status__c`, `Date_First_Lead_Lead_Status__c` | 2024-04 | EMEA-style status. |
| Last Hubspot Demand Date Yooz | `Last_Hubspot_Demand_Date_Yooz__c` | 2024-02 | |
| Opportunity First Touch Lead Source | `Opportunity.LeadSource` | ~2026-07 (approx.) | Locked against manual edits around June–July 2026. SDRs sometimes edited it before. |
| `Advertising` as a source | `LeadSource` | Low quality | Old fallback; the switch to `To be defined` is not visible in the data yet. |
| 1-Year Recurring Amount | `Montant_recurrent_1an__c` | Use the converted value | Mixed currencies (USD/CAD/EUR). |
| Primary Campaign Source | `Opportunity.CampaignId` | Unknown | Never use alone. |
| Account Prospect/Client Status | `Satut__c` | Low quality | Prefix only. |
| Campaign Member Status | `CampaignMember.Status` | Being cleaned | No global rule. |
