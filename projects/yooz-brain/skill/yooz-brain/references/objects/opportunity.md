# Opportunity

*Status: Drafted · Sources: Greg (voice notes 2026-11-10), Salesforce schema 2026-11-09 · Validator: Chris*

How opps are born: when the SDR manually converts a qualified MQL, Salesforce creates the Contact, the Account and the Opportunity together (contact → SAL).

## Pipeline and amount

### Stage
- API: `StageName` · Object: Opportunity · Master: Salesforce
- Yooz definition: the opp's **state**, not its step. `En cours` = In progress, `Gagnée` = Won, `Perdue` = Lost, `Abandonnée` = Abandoned, `Annulée` = Cancelled.
- Values: Lost = lost to a competitor. Abandoned = the opp was stopped. Cancelled = created by mistake (a clean opp was created next to it).
- Use for: open/won/lost counts. Do not use for: funnel steps.
- Pitfall: Cancelled opps are duplicates. Exclude them from every count and from win rates.

### Sales stage (US)
- API: `SalesStagePick__c` · Master: Salesforce
- Yooz definition: the opp's **step** in NORAM: `Stage10` Qualification, `Stage20` Discovery, `Stage30` Solution Demo, `Stage40` Proposal, `Stage50` Closing, `Stage100` Closed won.
- Use for: pipeline by step, time in stage (with Opportunity History).

### Type
- API: `Type` · Master: Salesforce
- Values: `Souscription Yooz` = new client · `Vente additionnelle` = upsell/cross-sell to an existing client · `Yooz Free` = no longer used.
- Use for: new vs existing client. Usually enough on its own, but cross-check with the Account status. The Key Auto case (a client since 2023) once skewed an attribution example.

### 1-Year Recurring Amount
- API: `Montant_recurrent_1an__c` · Master: Salesforce
- Use for: **the default amount** for pipeline and won revenue. Do not use `Amount`.
- **Always use the converted value, and report NORAM in USD** (UK in GBP, EMEA in EUR). NORAM opps are in USD (10,759), CAD (1,490), EUR (45) and GBP (1) as of 2026-11-10, so the raw field mixes currencies. The EUR/GBP ones are marginal (errors or special cases): keep them, converted.
- In SOQL, use `convertCurrency(Montant_recurrent_1an__c)`. In exports, use the "1-Year Recurring Amount (converted)" column.
- Pitfall: "converted" means the viewer's own currency, not the corporate one. It is USD for Greg and for the connector user (checked: CAD 195,000 → 151,717). An export run by an EMEA user may come out in EUR. State the currency in every answer.

## People

### Contact (primary contact)
- API: `Interlocuteur__c` · Object: Opportunity → Contact · Master: Salesforce
- Yooz definition: **the opp's primary contact**, the person whose Lifecycle Status follows the opp (SAL → SQL → Customer Nurture). Use this field, not the Opportunity Contact Role `IsPrimary` flag.
- Fill rate (check on 2026-11-10, 1,442 NORAM opps created since 2025-01-01): 993 filled (69%), 449 empty (31%). Among the 555 opps that also have a primary Contact Role, 74 (13%) point to a different person.
- Pitfall: when the field is empty, the opp has no reliable primary contact. Say so rather than picking a Contact Role. Other people on the deal (Champion, Decision Maker…) are in Opportunity Contact Role.

## Source and attribution

### First Touch Lead Source (Opportunity)
- API: `LeadSource` · Master: Salesforce
- Yooz definition: the source at opp creation. **Default attribution field** (see attribution.md).
- Pitfall: the Opps export also has the Account's First Touch columns. Do not mix them up.

### Primary Campaign Source
- API: `CampaignId` · Master: Salesforce
- Reliability unknown (first or latest campaign?). Never use it alone.
