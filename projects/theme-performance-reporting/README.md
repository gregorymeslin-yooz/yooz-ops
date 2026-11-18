# Theme Performance Reporting NORAM

**Demandeur :** Kirsten (CRO) · **Démarré :** oct. 2026 · **Statut :** ✅ livré

## Question
Quels sujets marketing NORAM ont :
- attiré (first touch) ;
- fait avancer (MOFU) ;
- converti (last touch).

Avec opportunités, pipeline et ARR, et les différences par ERP et par industrie.

## Plan projet vivant
Définitions, étapes, exports, questions ouvertes, journal de décisions :
https://claude.ai/code/artifact/dab86ba4-470e-4a6e-be59-1106e8b78fdf

Toujours relire ce plan avant de reprendre le chantier.

## Où trouver le reste
- Méthode : [`playbooks/analyse-contenus-vers-pipeline.md`](../../playbooks/analyse-contenus-vers-pipeline.md)
- Référentiel de sujets : [`ref/regles_sujets.csv`](ref/regles_sujets.csv) et [`projects/topics-2027/ref/TOPICS.md`](../topics-2027/ref/TOPICS.md)
- Scripts : plus stockés depuis le nettoyage du 18/11/2026 ; à déposer dans `scripts/` s'ils sont retrouvés.
- Pièges Salesforce : [`knowledge/salesforce/gotchas-arr-acquisition.md`](../../knowledge/salesforce/gotchas-arr-acquisition.md) (paliers ARR, PI, Contact Roles, ID 15 caractères).

## Champs HubSpot identifiés (09/10/2026)
- `sales_territory` (NORAM)
- `firstentrydatemel__c`, `firstentrydatemql__c`, `firstentrydaterecycle__c`
- `hs_analytics_first_url`, `hs_analytics_last_url`
- `hs_analytics_source` (+ `_data_1`, `_data_2`)
- `first_conversion_event_name`
- `erp_en_us`, `verticalpick`, `naics_code`
- `leadstatus`
