# Salesforce Yooz — pièges de donnée sur les analyses d'ARR et d'acquisition

Notes techniques réutilisables, établies sur donnée réelle pendant l'analyse ROI tradeshows (voir `knowledge/context/chantiers-de-fond.md`, section 6). À relire avant de construire tout reporting de montant ou d'attribution.

## Le champ « 1-Year Recurring Amount » ne s'additionne pas entre les phases d'un même contrat

C'est le piège le plus coûteux identifié. Sur les contrats déployés par paliers de volume, Yooz crée **une Opportunity par palier**, et le champ « 1-Year Recurring Amount » de chacune contient **l'annualisation de ce palier**, pas un incrément. Les additionner produit un montant que le client ne paiera jamais.

Cas de référence, VYTL CONTROLS GROUP, confirmé par la note Sales du contrat :
- l'abonnement tourne à 9 000 documents/mois de mars à août 2026, puis 12 000/mois de septembre à février
- 12 195 USD/mois × 12 = 146 340 → c'est exactement l'Opportunity « Roll-Out 01/02 · Yooz Gold 9000 »
- 16 260 USD/mois × 12 = 195 120 → c'est exactement l'Opportunity « Roll-Out 02/02 · Yooz Gold 12000 »
- les deux notes de contrat sont identiques mot pour mot, et les deux Opportunities sont closes à un jour d'intervalle sur une seule signature

Les trois lectures possibles : somme des deux = 341 460 (faux) · palier de régime = 195 120 (convention ARR standard, recommandé) · récurrent réel des 12 premiers mois = 170 730.

**Test pour reconnaître une série de paliers** : diviser le montant par le palier de volume figurant dans le libellé. Si le tarif par unité est identique à la décimale sur toutes les lignes du compte, c'est le même abonnement à des volumes différents — ne garder que le palier maximum. Vérifié sur quatre comptes : VYTL 16,26 · BILLION MOTORS 13,125 · ALL ROADS TRUCKS 16,8 · FAYOLLE CONSTRUCTION 7,44, chaque fois strictement constant. À l'inverse SERVICE CONTRACTING SOLUTIONS montre 7 lignes avec des tarifs de 12,0 à 14,52 et des paliers dupliqués (1000 ×2, 1500 ×2, 3000 ×2) : ce n'est pas une série de paliers mais probablement plusieurs entités, et là l'addition peut être légitime. En cas de doute, demander la note Sales de l'opportunité — c'est ce qui a tranché sur VYTL.

Impact mesuré sur l'analyse tradeshows : l'ARR net new passe de 1 461 180 à 1 149 426 USD (−21 %) en corrigeant les quatre séries certaines, et à 1 021 872 si SERVICE CONTRACTING en est une aussi.

## Le préfixe « PI » signifie Price Increase

Hausse de prix sur contrat en cours, sans lien avec une action marketing. Confirmé métier. Signature dans l'org : date de création égale à la date de clôture, création le 15 du mois, libellé `<COMPTE> - PI - MM/AAAA`. **À exclure entièrement de toute analyse d'acquisition** — montants comme comptages. Corollaire exploitable : la présence d'un enregistrement PI prouve qu'un compte est déjà sous contrat (vérifié : 46 des 53 comptes à PI post-salon n'ont aucune opportunité gagnée antérieure dans la fenêtre, leur contrat précède donc l'export). Un lot voisin libellé « Yoozpay Repay update », créé en masse le même jour avec création = clôture, a la même nature administrative.

## Taux de change : Salesforce a un taux corporate, ne pas en inventer un

Le champ « 1-Year Recurring Amount (converted) » est déjà converti en USD par Salesforce, au taux **0,77804** pour CAD→USD. Inutile de demander un taux à la Finance ou d'en supposer un : exposer le champ converti dans le report suffit. Devises présentes dans le périmètre NORAM : USD, CAD, et 6 lignes EUR marginales.

## Le champ « Opportunity Type » distingue new business et expansion

Valeurs observées : `Yooz Subscription` et `Additional Sale`. C'est le champ structuré à exposer pour toute analyse net new, plutôt que de déduire l'expansion depuis le libellé de l'opportunité. Attention à son usage réel sur les contrats en phases : la phase 02/02 d'un déploiement neuf peut être typée « Additional Sale » sans que le compte soit un client préexistant — à clarifier avec Sales Ops avant de s'appuyer dessus seul.

## Les Campaign Members d'un même salon sont éclatés sur plusieurs campagnes

Un salon physique porte souvent deux ou trois campagnes : événement principal, session de speaking, dîner, happy hour, Tech Tour. Sur juin 2025 – mai 2026, 50 campagnes correspondaient à ~42 salons réels. Conséquences pratiques :
- **toujours dédoublonner par Opportunity ID** avant de sommer un montant dans un report « Campaigns with … ». Sur le report Partner existant, sommer la colonne telle qu'affichée donne 665 209 USD au lieu de 545 197, soit +22 %
- ne pas rapprocher un comptage de campagnes de factures d'exposition
- privilégier un regroupement par campagne parente

## Le filtre « Member Status = Present » est réel, restrictif et très inégal

Vérifié : 11 524 leads rattachés aux campagnes tradefairs sans filtre de statut, contre 4 226 marqués Present. Mais le ratio varie de **1,1× à 125×** selon la campagne — Intuit Connect a 1 382 leads rattachés pour 11 Present, AICPA Engage 758 pour 610. Ce n'est pas une différence de fréquentation mais de discipline de scan au stand. Donc : toute comparaison de performance entre salons est faussée par la qualité du scan, et filtrer sur Present fait perdre des gains réels (4 opportunités gagnées identifiées hors périmètre Present).

Le champ Member Status n'est pas exposé par défaut dans le report « Campaigns with Campaign Members » tel qu'il était configuré — le demander explicitement.

## Le champ campagne « Demo date » est plus fiable que la date dans le nom

`Demo date` est un champ de campagne, jamais nul, une seule valeur par campagne. Il valide la date extraite du nom (`_AAAA-MM-JJ_`) à ±2 jours sur 46 campagnes sur 50. Trois écarts matériels observés : Acumatica Summit 2026 (+26 j), ABC Convention (−28 j), Peterbilt Dealer CFO (+10 j). Aucun des deux champs n'est autoritaire à 100 % — sur Acumatica c'est le champ qui semble faux, l'événement réel étant fin janvier. Utiliser `Demo date` comme défaut, le nom comme contrôle.

Le périmètre canonique des salons est `Campaign Type = Tradefairs`, pas un filtre sur le nom : il exclut les événements partenaires (BAASS Connect, Cherry Bekaert, Sage Future Partner Event) et inclut des campagnes qu'un filtre par nom rate.

## « Converted Lead Information » : ce que ce report type voit et ne voit pas

Il couvre les Leads, et récupère la plupart des personnes aujourd'hui en Contact — parce que la conversion d'un Lead re-pointe nativement son Campaign Member vers le Contact résultant. Mesuré : 845 de 1 471 membres de type Contact sont retrouvés dans le report. Ne restent réellement invisibles que les personnes déjà Contact avant le salon et jamais passées par un Lead dans la fenêtre, soit 659 personnes (12 % du périmètre).

Sa vraie limite est ailleurs : le champ d'opportunité de conversion ne capte que **l'opportunité créée à l'instant de la conversion**. Toute opportunité créée ensuite sur le même compte est invisible. Mesuré : il voit 17 opportunités gagnées là où le périmètre compte 99, et capte 33 % de l'ARR net new. Sur les contrats en phases, il voit la phase 01 et rate la phase 02. Excellent comme plancher single-touch et comme jeu de validation croisée, insuffisant comme source unique.

## Custom Report Type : contourner la limite de chaînage d'objets

La condition « opportunité créée après *ce* salon » traverse Campaign → CampaignMember → Contact → Account → Opportunity, au-delà de ce qu'un CRT porte en pratique. Contournement natif recommandé : un champ date sur l'Account (ex. `Tradeshow_First_Attendance__c`) alimenté par un Flow à la création d'un Campaign Member de type Tradefairs, puis un report « Accounts with Opportunities » filtré sur ce champ avec une row-level formula `Opportunity.CreatedDate >= Tradeshow_First_Attendance__c`. Deux objets au lieu de cinq, natif et rafraîchissable.

## Autres points de vigilance constatés

Saisie rétroactive : 12 des 38 opportunités gagnées net new ont une date de clôture **antérieure** à leur date de création (les 7 lignes de SERVICE CONTRACTING, les 3 de BILLION MOTORS). La Created Date est alors une date d'enregistrement administratif, pas le début du cycle commercial — la Close Date est un test plus robuste pour une condition « après le salon ».

Doublons d'Account : trois noms de compte existent sous deux AccountId distincts (Acumatica, Hennessy Auto, Hudson Automotive), soit 662 comptes réellement distincts pour 665 identifiants.

Libellés d'opportunité portant un autre nom que le compte : PETFOLK porte une opportunité « WHITLEY PENN - PI - 07/2025 », SONNELL TRUCK CENTER porte « PETERBILT PUERTO RICO - Yooz CDK 500 ». Ne pas se fier au libellé pour identifier le compte.

Les ID d'Account sortent en 18 caractères sur les exports Campaign et en 15 sur les exports Opportunity — tronquer à 15 avant toute jointure (aucune collision constatée sur 665 comptes).
