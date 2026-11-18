# Account

*Status: Drafted · Sources: Greg (voice notes 2026-11-10), Salesforce schema 2026-11-09*

### Prospect/Client Status
- API: `Satut__c` (sic) · Object: Account (copied to Contact as `TECH_AccountProspectClientStatusAuto__c`) · Master: Salesforce
- Yooz definition: a low-quality picklist. Read only the prefix: `Prospect…` = still a prospect; `Client…` = client.
- Values to ignore in NORAM: every `…Try…` value (old free trials, barely used in EMEA now), `Leads Itesoft`, `Test`, `Production`, `Dossier Client ou Société Fille`, the EMEA portal values. `Client Litige Paiement` and `Client Suspendu` are still clients.
- Use for: a quick prospect/client check. Do not use for: analyses on their own. Cross-check with opps (won `Souscription Yooz`) and Cancellation Date.
- Pitfall: exclude `Test` accounts from every analysis.

### First Touch / Last Touch Lead Source (Account)
- API: `Origine_de_la_piste__c`, `Last_Touch_Lead_Source__c`. Not `AccountSource`.
