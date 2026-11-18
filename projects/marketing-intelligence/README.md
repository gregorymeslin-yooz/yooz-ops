# Marketing Intelligence — Briefing de reprise (état au 20/10/2026)

**Parties prenantes :** Andrew, Jessica, Michael · **Statut :** 🟢 cadrage / prototype

Document de passation pour reprendre le projet dans une nouvelle conversation. Rien n'est figé au-delà de ce qui est marqué "décidé".
Mise à jour suivante : [`notes/2026-10-22_vision-update.md`](notes/2026-10-22_vision-update.md) · Règles petits volumes : [`playbooks/regles-decision-petits-volumes.md`](../../playbooks/regles-decision-petits-volumes.md).

## 1. Le besoin
Reconstituer le lead / customer journey complet (tous les touchpoints entre first et last touch), relié aux étapes du funnel et au revenu, filtrable par territoire, persona, industrie, contenu ; à terme au niveau compte. Livrable attendu : des recommandations ("so what"), pas des dashboards de plus. Le projet BI existant n'a rien livré (constat partagé par Andrew et Michael) — ne pas se positionner comme remplaçant.

## 2. Ce que les parties prenantes ont dit
- **Andrew (manager)** : performance marketing = relier dépense → pipeline. Ordre : (1) a-t-on la donnée (UTM, GA4, cookies) ? (2) recoudre des parcours à la main sur 10 → 100 → 1000 users ; (3) warehouse ensuite. La BI a échoué car elle a construit un rapport, pas un entrepôt. Commencer en spreadsheets. Le niveau compte : "probablement", mais partir de l'existant.
- **Jessica** : parcours de bout en bout par campagne et par source ; coût par lead / opp / won par cohorte ; vitesse de suivi SDR par campagne (% de leads touchés sous 5 jours) ; une source de vérité unique ; comparaison NORAM vs EMEA à haut niveau ; personas différents selon les régions. Veut être consultée tôt (points de 15 min).
- **Michael** : voir la séquence et l'ordre des étapes jusqu'à l'opp, avec accès aux personnes derrière chaque chiffre (pas de tableaux croisés opaques). **"Si tu commences quelque part, commence par le contenu"** : contenu → opps/deals, et usage du contenu sur le site (resource center, recherche, scroll sur les pages industrie). Demandes annexes, hors projet principal : (a) contacts unengaged email mais actifs ailleurs (pas de date d'unengaged stockée) ; (b) recommandation de webinar selon la page visitée (popup HubSpot).

## 3. Principes retenus
- Le parcours est le focus ; le coût est une dimension rattachée aux touchpoints/campagnes (canaux payants : ads, syndication, salons).
- Modèle cible : une table d'événements de touchpoints (contact, compte, date, type, canal, campagne, contenu) + entités (comptes, contacts, opps, contact roles, campagnes) + historique des étapes + référentiel de contenus multidimensionnel (format × thème × persona × étape), stocké et versionné, pas réinterprété par l'IA à chaque question.
- Chaque chiffre agrégé doit pouvoir s'ouvrir sur la liste des personnes.
- Historique de référence à partir du 01/01/2025 dans les exports (début d'analyse prévu au 01/01/2026, à redéfinir). Dates MQL avant octobre 2025 non fiables (migration du lead journey).
- n8n = ingestion (exports Probance via FTP, Ads) ; le stockage = source de vérité ; Claude = interface. MCP Salesforce prévu côté Claude.
- Rituel à mettre en place : créneau hebdo d'exploration + "3 recommandations" mensuelles à Michael/Kirsten.

## 4. Découpage en checkpoints (proposition)
- **CP0** : a-t-on la donnée ? Définition du touchpoint, audit UTM / tracking (code HubSpot sur WordPress, GA4, bandeau cookies, ZoomInfo WebSights), glossaire des métriques, questions métier.
- **CP1** : contenus et parcours qui mènent à l'opportunité (remonter le temps depuis les opps NORAM 2026). Intègre le Theme Reporting et le Content Hub.
- **CP2** : passage à l'échelle sur les cohortes MQL (persona, industrie, thème, délais entre étapes).
- **CP3** : niveau compte (comité d'achat) + comparaison de séquences.
- **CP4** : warehouse, une fois le modèle stabilisé ; la vue coût/résultats devient un rapport parmi d'autres.
- Parallèle : audit GA4 / analytics web (demande de Michael) ; rapport Activities pour le suivi SDR (Jessica).

## 5. Prototype en cours (étape suivante)
Échantillon : 10 opps NORAM créées en 2026 (gagnées + avancées, sources variées, dont 3 à 5 deals qu'un commercial connaît pour valider) + 5 à 10 MQL jamais passés en opp comme groupe témoin. Pour chaque opp : contacts (contact roles + autres contacts du compte) → tous les touchpoints (Salesforce exports, HubSpot via MCP, Probance) → une table chronologique → frise par compte + grille de diagnostic (qu'est-ce qu'on retrouve / qu'est-ce qui manque).
Première étape à l'arrivée des exports : contrôle des jointures (taux de correspondance, IDs orphelins, emails manquants).

## 6. Exports Salesforce (format : Details Only, CSV, dates "greater or equal 01/01/2025", jamais de date relative)
Indispensables :
1. **NORAM Opps since 01012025** (Opportunities) — All opps, Created ≥ 01/01/2025, Status Any, Probability All, Sales Territory = NORAM, Type = Yooz Subscription. Colonnes : Opp ID, Opp Name, Account ID/Name, Created Date, Close Date, Sales stage, Type, 1-Year Recurring Amount, Lead Source, Primary Campaign Source, Closed, Won, Owner, dates d'étape si elles existent.
2. **NORAM Opp Contact Roles since 01012025** — Opp ID, Contact ID, Account ID, Role, Primary (créé).
3. **NORAM Contacts since 01012025** (Contacts & Accounts) — 1 Territory = NORAM ; 2 First Touch ≠ Created by Yooz application ; 3 Created ≥ ; 4 MEL last entry ≥ ; 5 MQL last entry ≥ ; 6 Recycle last entry ≥ ; 7 Last Modified ≥. Logique : 1 AND 2 AND (3 OR 4 OR 5 OR 6 OR 7). Colonnes : Contact ID, Account ID/Name, Converted Lead ID, Email, Title, persona, Industry, ERP, Statut, dates first/last MEL/MQL/Recycle, First/Last Touch Lead Source, Last Touch Request Type, Created Date, Account Prospect/Client Status.
4. **NORAM Leads Activated since 01012025** (Leads with converted lead information) — logique de Florian : 1 AND (2 OR … OR 12) AND 13 (status ≠ Suspect/MCL ; dates d'activation et d'entrée en statut ≥ ; NORAM), sans filtre "Converted". Colonnes : Lead ID, Email, Company, Converted, Contact/Account/Opp ID de conversion, Partner Account, Title, persona, Industry, ERP, Lead Status, First Activation Date, dates d'étape, First/Last Touch Lead Source, Last Touch Request Type, Created Date, Owner.
5. **NORAM Campaign Members since 01012025** (Campaigns with Campaign Members) — Member First Associated Date ≥ 01/01/2025 (et non Demo date), Sales Territory of Member = NORAM, aucun filtre Record Type. Colonnes : Campaign ID, Name, Type, Record Type, Parent, Start Date, Actual/Budgeted Cost, Member ID, Related Record ID, Member Type, AccountId, Email, Member Status, First Associated Date, Responded, First Responded Date.

Utiles :

6. **Lead / Contact Last Touch RT History** — ajouter impérativement le Lead ID / Contact ID ; Edit Date, Old Value, New Value.
7. **Activities** (Tasks and Events, NORAM, ≥ 01/01/2025) — Activity ID, Who, What, Type/Subtype, Date, Owner, Status.
8. **Opportunity History** (historique des étapes).

Probance : export par contact (email, campagne, dates d'envoi, d'ouverture et de clic). HubSpot : via le MCP. Ads : rien au niveau individuel.

Remarques : les rapports "Campaign with Lead" et "Campaign with Contact" sont redondants avec Members (44 458 + 16 478 = 60 936). Les exports à 15 caractères suffisent pour des jointures en Python (sensibles à la casse) ; attention à Excel, qui ne respecte pas la casse.
