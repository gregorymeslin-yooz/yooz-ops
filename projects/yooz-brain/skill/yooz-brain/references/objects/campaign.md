# Campaign & Campaign Member

*Status: Drafted · Sources: Greg (voice notes 2026-11-10), Salesforce schema 2026-11-09*

## Campaign

### Campaign Name
- Yooz definition: recent names follow the **Name Generator** convention (rules verified 2026-11-10). [TO CONFIRM: since when it has been in use]
  - Campaign: `[TERRITORY]_[COUNTRY?]_[CAMPAIGN-NAME]_[YYYY-MM-DD]`, e.g. `NORAM_Q4-Launch_2026-11-10`.
  - Assets (email, landing page, thank-you page, popup, workflow): `[TERRITORY]_[COUNTRY?]_[TARGET]_[ASSET]_[OBJECTIVE]_[CONTENT?]_[WEBINAR-TYPE?]_[CAMPAIGN-NAME]_[DATE]`.
  - Tokens: territory `NORAM`, `FR`, `UK`, `SP`, `LATAM`, `APAC`, `EMEA-FR`, `EMEA-EN`, `FR-EMEAFR`, `UK-EMEAEN` · target `BUS`, `EXP`, `PAR`, `BUS&EXP`, `BUS&PAR` · asset `EML`, `LP`, `TYP`, `POPUP`, `WF` · objective `ACQ`, `NUR`, `RET`, plus `-UNEN` (email to unengaged contacts) · content `Communication`, `Event`, `Infographic`, `Lead-Magnet`, `Tradeshow`, `Webinar`, `Whitepaper` (none = Other), `Follow-up-*` (email only) · webinar type `ADV`, `CUS`, `EDU`, `TRN`. The date is the last segment, but its business meaning (launch or event date) is not defined.
- Pitfall: older names (e.g. `Sales_US_ColdPhoning`) and campaigns pasted through "Existing Salesforce Campaign" mode do not follow the convention. A conforming structure does not prove a conforming name. Parse a name only when it matches; otherwise rely on `Type`, `CampaignObjectivePick__c`, `Target__c` and `VerticalPick__c`.

### Type
- API: `Type` · Master: Salesforce
- Pitfall: `Operational` = Ops technical campaigns. Exclude them from every analysis.

### Sales Territory
- API: `Sales_Territory__c` · Type: **multi-select picklist**. Filter with `INCLUDES ('NORAM')`, not `=`.

## Campaign Member

### Member Status
- API: `Status` · Master: Salesforce
- Yooz definition: **the engagement field**. Values mix French and English (Sent/Envoyé, Registered/Inscrit, Attended/Présent…).
- Pitfall: there is no global rule for which statuses mean "participated". It depends on the event, and the values are being cleaned. State the statuses you counted.

### Responded
- API: `HasResponded` · Set automatically by Salesforce
- Do not use: Yooz relies on Member Status instead.
