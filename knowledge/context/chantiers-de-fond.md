# Chantiers de fond — contexte Yooz (version condensée, 18/11/2026)

Ce document donne le contexte de fond et les règles réutilisables issues des chantiers passés. Il ne suit pas le statut des projets : le statut est dans `projects/README.md`.

Les principes techniques déjà validés (Active Lists, UTM cachés, CTOR, arrondis HubSpot, Campaign Members, etc.) sont dans `knowledge/hubspot/principes-valides.md`. Ils ne sont pas répétés ici.

---

## 1. Architecture UTM & attribution (HubSpot + Salesforce)

**Situation de départ :** le préfixe forcé dans `utm_campaign` (ex. `utm_organic_social_media`) servait à déduire le Lead Source dans SF. Bascule vers une lecture directe de `utm_source`.

**Deux causes racines trouvées**
- Côté HubSpot : un branchement sur Active Lists causait 77 % d'échecs.
  - Corrigé par des conditions inline `starts with`, avec le préfixe legacy traité avant la normalisation.
  - Le trigger est un OR sur les 3 UTM.
- Côté SF : race condition, le flow évaluait le Lead Source avant la fin de la normalisation HubSpot.
  - Corrigé par un délai de 15–20 min dans le flow, validé avec Antoine Sabathier.

## 2. Reporting email HubSpot (par verticale, MEL/Recycle)

- Report Table « Marketing email analytics » + « Marketing email », avec les champs de comptage en Sum (évite les doublons de lignes).
- Seuils internes historiques plutôt que benchmarks externes.
- Fenêtres de régime stable :
  - MEL depuis oct. 2025 ;
  - Recycle depuis mai 2026.
- La branche Recycle est la plus performante. Enseignements : objets spécifiques, objet aligné sur le corps, CTA à faible friction.

## 3. Intercom « Quick Demo »

- Le branchement se fait selon les horaires d'ouverture : BookIt hors horaires, SDR live pendant.
- Le chemin « just browsing » n'a volontairement pas de CTA.
- Bug corrigé : il manquait une étape « Close conversation », ce qui laissait des conversations non assignées.
- Contexte SDR quand une réservation BookIt arrive sans Lead SF :
  - préfixe dans le titre du RDV, description en langage naturel, lien BookIt dédié par source ;
  - la création de Lead via ZoomInfo/n8n a été écartée, faute de capacité côté CRM.
- Cannibalisation entre bots :
  - Quick Demo passe en priorité #1 ;
  - High/Medium Intent sont désactivés sur les pages concernées ;
  - les URL sont exclues explicitement de Returning Visitors.

## 4. Funnel Community Summit & dashboard partenaires (Salesforce)

- Vérifier empiriquement sur quelques fiches avant de construire une architecture lourde.
- MQL→Opp : report type natif « Leads with Converted Lead Information » plutôt qu'un CRT. Le compromis est une attribution single-touch, accepté.
- Dashboard partenaires, architecture hybride :
  - Campaign with Campaign Members pour le volume et l'acquisition ;
  - reports Lead/Contact avec cross-filters de campagne pour la progression de stage. Les date-stamps de stage existent déjà.
  - Ne pas promettre un dashboard tenant dans un seul report.

## 5. n8n

- Le TCO a écarté le self-hosted à notre volume.
- Trajectoire : pilote sur Cloud Pro sans Salesforce/M365 en production, puis plan Business (SSO) avant toute production.
- Questionnaire IT rempli en restant honnête sur les limites du plan Pro (pas de SSO ni SCIM).

## 6. ROI tradeshows (Finance, Amélie) — règles à réutiliser

**Métriques**
- On parle de leads/comptes **influencés**, pas « générés ».
- Entrée : tout Campaign Member d'un tradeshow de la période.
- Opp comptée au niveau du **compte** : toute opp créée après la date du salon (premier salon si le compte en a plusieurs).
- Montant : champ « 1-year Recurring Amount ».
- Primary Campaign Source sert à la vérification croisée seulement, pas de source de vérité.
- La jointure CM → Account → Opps datées ne tient pas dans un report SF natif : faire 2 exports joints hors SF.

**Opportunités « PI » (Price Increase)**
- Signature :
  - libellé `<COMPTE> - PI - MM/AAAA` ;
  - date de création = date de clôture ;
  - créées le 15 du mois.
- **Toujours les exclure des analyses d'acquisition**, en montants comme en comptages.
- Un PI sur un compte prouve que le compte est déjà client sous contrat.
- Lot voisin « Yoozpay Repay update », de même nature administrative : à trancher avec Sales Ops.

**Nommage des campagnes tradeshow**
- La date est au format `_AAAA-MM-JJ_` dans le nom, ce qui permet de l'extraire par regex.
- Un même salon peut porter 2 ou 3 campagnes (Tech Tour, dîner, speaking…) : 50 campagnes ≈ 42 salons réels.
- Le champ Member Status n'était pas exporté par le report CWCM.

**Net new (cadrage final de Greg)**
- On exclut tout compte :
  - qui porte un PI ;
  - qui avait une opp ouverte avant son salon ;
  - qui n'a gagné que des add-ons ou services sans licence neuve.
- Un service *postérieur* à une licence neuve, c'est de l'onboarding : il ne disqualifie pas le compte. C'est la chronologie qui tranche, pas le libellé.
- Résultat : 1,46 M USD net new (21 comptes) contre 4,63 M toutes affaires confondues.
- Limites :
  - l'export d'opps démarre le 01/06/2025, donc le net new est un plafond ;
  - aucun champ structuré New business / Expansion (piste à voir avec Sales Ops et Christophe).

**Méthode**
- Faire une QA adversariale en recalculant tout depuis les CSV bruts.
- Toujours produire un **tableau de sensibilité** qui montre le chiffre sous chaque définition défendable.
- Chiffrer les affirmations qualitatives. Ici, 72 % du montant venait de comptes déjà clients ou déjà en cycle, et 10 comptes portaient 65 % du total.
