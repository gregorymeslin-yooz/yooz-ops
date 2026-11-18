# GME Business Dashboard — clone partagé de Pulse

*Créé le 2026-11-18 · Artifact : https://claude.ai/artifact/3eREuYoSb2TrtZPFBDKUNV · Source : Pulse version 1794856663-9030 (2026-11-14)*

- Copie figée de Pulse, à partager en lecture seule. Pulse reste le dashboard perso de Greg et continue d'évoluer ; le clone ne bouge que si Greg demande d'y reporter une version.
- Pages : Daily Insights, Sales, RevOps, Config. **Marketing et Health retirées** (Greg, 2026-11-18).
- Daily Insights : les signaux Health (« … got worse ») sont retirés ; les signaux Marketing (objectif MQL, volumes MEL/MQL, variations par source) restent, sans lien « View → ».
- Nom « GME Business Dashboard » (titre, sidebar, fil d'Ariane, à la place du message d'accueil « Welcome, Greg ») ; Config réduite à l'onglet Sales (objectif MQL → SAL, SLA premier contact MQL, seuil SAL inactif, équipes), sans barre d'onglets. Les autres réglages (Salesforce URL, portail HubSpot, seuils Health) sont repris de la config de Pulse mais ne sont plus modifiables dans la page.
- Base : `config/main` copiée depuis Pulse le 2026-11-18. Règles : lecture pour tous les lecteurs (view), écriture par le propriétaire seulement. Les changements de Config faits par un lecteur ne sont pas enregistrés ; les relevés quotidiens (`snapshots/`) ne sont écrits que lors des visites de Greg.
- Connecteurs : Salesforce `soqlQuery`, HubSpot `search_crm_objects`, appelés avec les accès de la personne qui ouvre la page.

## Report d'une nouvelle version de Pulse
Relire le code de Pulse, réappliquer les remplacements, republier sur le slug `156bbc2e-fdf5-49c8-b8fe-c9bbae8563fa` :
1. `<title>`, `.brand` (au lieu de GREETING), crumb « Pulse / » → « GME Business Dashboard / », INSIGHTS.userIntent.
2. Config : `CFG.tab = 'sales'`, `CFG_TABS = [['sales','Sales']]`, barre d'onglets masquée sous 2 onglets.
3. `PAGES` sans `marketing` ni `health` ; ajouter `const HAS_PAGE = id => PAGES.some(p => p.id === id);`.
4. Daily Insights : bloc Health de `attentionData` sous `HAS_PAGE('health') &&` ; lien « View → » des signaux rendu seulement si `HAS_PAGE(cible)` ; sous-titre « goals, volumes and sources » et message vide sans « data checks ».
