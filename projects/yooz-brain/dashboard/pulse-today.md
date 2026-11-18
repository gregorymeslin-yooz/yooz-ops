# Dashboard Pulse — page Today (spec)

*Construite le 2026-11-14 · Complète `dashboard/pulse-dashboard.md` · Artifact : https://claude.ai/artifact/B8BMXpuWWGTyRRjdXFYNAS*

## Rôle
Répondre à une seule question : « qu'est-ce qui a bougé hier et qu'est-ce qui mérite mon attention ? ». Une journée calme = une page courte. Chaque ligne renvoie vers la page détaillée.

## Décisions (Greg, 2026-11-14)
- **Premier item de la sidebar et page par défaut** (icône Today, calendrier bicolore créé pour le dashboard, hors librairie Pulse).
- **Sidebar en deux groupes** : Today seul en haut, puis un libellé de section « Deep dives » au-dessus de Marketing, Sales, RevOps et Health.
- **Message d'accueil à la place de « Pulse »** en haut de la sidebar : une phrase tirée au hasard à chaque ouverture, selon l'heure et le jour (Welcome / Welcome back / Hi / Hello ; Good morning ou Morning ou Rise and shine avant midi ; Good afternoon ou Afternoon jusqu'à 18 h ; Good evening ou Evening ensuite ; Happy Monday, Happy Friday, Happy weekend selon le jour). Prénom « Greg » en dur. Le fil d'Ariane garde « Pulse / … ».
- **Fenêtre = hier, jour complet** ; le lundi : vendredi → dimanche. Pas de filtre.
- **Référence = la même fenêtre décalée de 1 à 4 semaines** (moyenne), jamais la veille.
- **Blocs V1** : Yesterday in numbers · What needs attention · Deals that moved. Pas de bloc « To act on » ni « Month pace » (l'objectif MQL apparaît seulement comme alerte).
- **Page seulement**, pas de digest Teams / e-mail pour l'instant.
- **Règle d'alignement (Greg, 2026-11-14)** : sur toute nouvelle page, les lignes équivalentes de cartes voisines doivent tomber exactement à la même hauteur, chip ou pas, et aucun texte utile ne doit être coupé. Vérifier par mesure (positions des éléments) avant de publier, pas seulement à l'œil.

## Yesterday in numbers (6 cartes Block standard, grille de 6)
Anatomie fixe : en-tête (icône + titre) · ligne valeur de 34 px (chiffre à gauche, chip de variation à droite, aligné en bas) · ligne de pied de 22 px (texte gris, lien à droite si besoin). Le chip n'est jamais dans le pied, pour que « usual X » ne soit jamais coupé.

| Carte | Valeur | Pied | Référence |
|---|---|---|---|
| MELs | personnes entrées en MEL dans la fenêtre (comme Marketing : leads non convertis + contacts, F4/F5) | usual X | moyenne 4 semaines |
| MQLs | idem MQL | usual X | idem |
| New opps | opps `Souscription Yooz` créées, hors `Annulée` (= SAL de RevOps) | usual X | idem |
| Won | opps passées à `Gagnée` dans la fenêtre (historique `StageName`) | montant (récurrent 1 an en USD) · usual X | idem (nombre) |
| Lost / abandoned | passées à `Perdue` ou `Abandonnée` (historique `StageName`) | usual X ; hausse en rouge | idem |
| MQLs past SLA | MQL ouverts qui ont franchi le SLA (48 h, Config) pendant la fenêtre sans activité depuis leur entrée | « N in total » (stock actuel hors SLA, détail au survol) · « View → » Sales | pas de référence |

Chip de variation masqué si la référence est < 3 (trop de bruit).

## What needs attention (tableau Signals, 6 lignes max)
Classement : Health Critical → objectif MQL → Health Serious → volumes → sources → Health Warning.
- **Contrôle Health dégradé** : échecs en hausse d'au moins max(5, 1 %) depuis le dernier relevé. 2 lignes max, le reste regroupé (« N other data checks got worse »).
- **Objectif MQL** : MQL du mois jusqu'à hier < 90 % de l'attendu au prorata des jours ouvrés (≥ 3 jours ouvrés écoulés, objectif Config).
- **Volumes** : MELs, MQLs (référence ≥ 5) ou new opps (≥ 3) à ± 50 % de leur référence.
- **Sources** : MQL par First Touch sur les 7 derniers jours vs la moyenne hebdomadaire des 4 semaines d'avant, ± 30 % et ≥ 10.
- Rien : « Nothing unusual on Friday… ».

## Deals that moved
Opps new business (NORAM) avec un événement dans la fenêtre, une ligne par opp : Won (« Won from Stage 4 ») · Lost / Abandoned (étape actuelle + bucket de motif RevOps) · New (créée, étape actuelle) · Advanced / Moved back (première étape → dernière, les saisies en rafale sont fusionnées). Annulées ignorées. Tri : Won, Lost, Abandoned, New, Advanced, Moved back, puis montant. 10 lignes, « Show all N ». Colonnes : événement · compte, owner, opp · mouvement · source First Touch · montant · Open ↗ (fiche Salesforce).

**Couleurs des chips d'événement** : Won vert · Lost et Abandoned rouge · New bleu (accent 10 %, texte `#0A5DC2`) · Advanced gris neutre · Moved back ambre (couleur Warning `#FEF5E1` / `#8A5A00`).

## Données
Requêtes Salesforce en lecture seule (6 en parallèle, cache 10 min) : 20 COUNT MEL/MQL (5 fenêtres × 2 objets × 2 stages), opps créées par jour sur 5 semaines, historique `StageName` + `SalesStagePick__c` sur 5 semaines (≈ 155 lignes / mois, limite 2 000), détail des opps concernées (`convertCurrency`), MQL ouverts (comme Sales), MQL par source (2 fenêtres), MQL du mois. Health vient du chargement Health existant (relevés `snapshots/`).

## Écarts au design system (à valider)
- Icône Today créée (calendrier, duotone), hors librairie.
- Libellé de section dans la sidebar (« Deep dives ») : 11/500 `#6B6B73`, 20 px au-dessus, 6 px en dessous ; pas de pattern validé dans Pulse.
- Message d'accueil à la place du nom « Pulse » (même style 15/600, coupé avec « … » s'il est trop long).
- Cartes KPI Today : Block standard en grille de 6 (Pulse : 4), valeur 34/500, chip de variation sur la ligne de la valeur au lieu du pied.
- Tableau des deals : nouvelle ligne `.dlrow` 64 px (chip d'événement, deux lignes de texte) ; chips New (bleu) et Moved back (ambre) hors palette de chips validée.

## Questions ouvertes
- [À VALIDER : Greg] Seuils des alertes (± 50 % volumes, ± 30 % sources, 90 % de l'objectif, max(5, 1 %) pour Health).
- [À CONFIRMER] Un dimanche, la page montre samedi (souvent vide) : garder, ou montrer vendredi → samedi ?
- Plus tard : digest du matin (Teams ou e-mail) une fois les seuils calés ; bloc « To act on » si le besoin revient.
