# 01 — Contexte du projet « Cerveau Yooz »

*Mis à jour le 9 novembre 2026. Ce fichier décrit le projet, pas le contenu du cerveau.*

## Pourquoi ce projet

Le cerveau est le **niveau 1 du projet Marketing Intelligence** (NORAM d'abord, puis monde). L'objectif final de Marketing Intelligence : donner aux dirigeants GTM des recommandations concrètes et actionnables (sources qui marchent, budget à réallouer, santé du pipe). Greg les propose à Flo et Chris, qui choisissent ce qui remonte à Kirsten (CRO).

En octobre 2026, les premières analyses sur les exports Salesforce et HubSpot ont montré que l'IA se trompe surtout faute de contexte Yooz, pas faute de données. Exemples :
- confondre First Touch Lead Source et première interaction ;
- attribuer une opp sans voir qu'une Primary Campaign Source Sales efface une demande de démo marketing ;
- confondre client existant et nouveau client ;
- utiliser une date MQL avant qu'elle soit fiable.

Le cerveau doit éliminer ces erreurs de façon durable et partagée.

## Équipe

- **Greg** (Marketing Automation, Ops NORAM) : porteur du projet, rédaction et tests.
- **Chris** (manager de Greg) : co-auteur de l'inventaire, auteur du glossaire des propriétés Lead Salesforce et HubSpot.
- **Flo** (N+2) : sponsor ; veut aussi raisonner en coûts (niveau 2).
- Depuis le départ d'Andrew (oct. 2026), Chris, Flo et Greg portent le périmètre RevOps.

## Ce qui existe déjà

- **Inventaire du cerveau** (artifact partagé) : 169 éléments au 9/11/2026, tous au statut « Prévu ».
  - 146 propriétés réparties sur Lead & Contact, Account, Opportunity, Task & Event, Campaign, Campaign Member et HubSpot Contact.
  - 4 notions de fonctionnement : lead journey de MCL à Client, rôle de Probance, scoring HubSpot, inclusion list de la sync.
  - 2 faits ou pièges.
  - 17 recos de Claude, dont 11 en V1.

  Il sert de **backlog et de suivi de statut**.
- **Glossaire de Chris** : toutes les propriétés Lead Salesforce et HubSpot. C'est la **source principale** pour les fiches Lead & Contact.
- Les analyses NORAM d'octobre 2026 (exports, dashboard V0) : source des pièges réels.

## Périmètre V1

**Dans la V1** : le lead journey de bout en bout, de MCL/Suspect jusqu'à l'opp signée.
- Statuts et transitions, scoring, sources et attribution.
- Campagnes et Campaign Members.
- Activités Sales nécessaires pour suivre un lead.
- Opp au niveau stage, type, source et montant.
- Correspondance entre HubSpot et Salesforce.
- Spécificités NORAM : filtre territoire, Probance réservé aux Suspects.

**Hors V1 (niveau 2)** :
- usage client et consommation (sauf Cancellation Date et Prospect/Client) ;
- table des coûts ;
- analyse avancée du pipe ;
- harmonisation EMEA/WW (on la note quand elle croise la V1, sans la traiter).

## Ce que le cerveau n'est pas

- Pas un nettoyage du CRM : on documente les champs tels qu'ils sont, défauts compris. Le nettoyage n'est pas prioritaire, et l'équipe CRM est saturée.
- Pas une doc exhaustive : un champ qui ne change aucune réponse business n'entre pas.
- Pas un remplaçant du projet BI existant (ne pas le présenter ainsi).

## Hébergement visé

L'output V1 est un **skill Claude** (dossier `yooz-brain/`), déployable au niveau de l'organisation pour que Greg, Chris, Flo et ensuite d'autres l'utilisent avec les connecteurs Salesforce et HubSpot.

Les alternatives écartées pour la V1 (projet Claude seul, mémoire d'organisation) sont à confirmer dans le journal des décisions. Un dépôt .md sur SharePoint peut servir de copie maître éditable.
