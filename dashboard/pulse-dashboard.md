# Dashboard Pulse — spec et suivi

*Mis à jour le 2026-11-14 · Artifact : https://claude.ai/artifact/B8BMXpuWWGTyRRjdXFYNAS · Design system : Pulse v1.1.0*

## Architecture
- Une page Artifact (anglais, Pulse) qui interroge en direct Salesforce (`soqlQuery`) et HubSpot (`search_crm_objects`) avec les identifiants de Greg. Cache de 10 min. L'icône Sync du bloc « Data is fresh » (sidebar) force le rechargement ; il n'y a plus de bouton Refresh dans les pages.
- **Lecture seule** : le dashboard n'a accès qu'à des outils de lecture. Rien n'est jamais écrit dans Salesforce ou HubSpot (règle de Greg, 2026-11-13).
- Base de l'artifact : `config/main` (réglages, dont les budgets canaux) et `snapshots/AAAA-MM-JJ` (un relevé par jour, pour le « vs yesterday »).
- Navigation : Marketing (icône Funnel), Sales (Opportunity), RevOps (Analytics), Health (Health) ; en bas, le bloc Data is fresh, un séparateur, puis Config (icône Settings, engrenage bicolore créé pour le dashboard, hors librairie Pulse). **Toutes les pages sont construites.**
- Toutes les définitions viennent du Yooz Brain (`skill/yooz-brain/`). Périmètre NORAM : Lead `SalesTerritory__c`, Contact/Opp `Sales_Territory__c`, HubSpot `sales_territory`.
- Salesforce : `https://getyooz.lightning.force.com` (liens « Open ↗ » vers les vues liste Lead, Contact, Opportunity ; surchargeables par contrôle dans Config).

## Funnel (décision du 2026-11-13)
- Graphique principal = 5 macro-stages MEL → MQL → SAL → SQL → Won (Global funnel Pulse, 5 stages validés). Il est affiché sur RevOps.
- SAL = opp au Stage 1 (`Stage10`), SQL = Stage 2 à 5 (`Stage20`–`Stage50`, Closing compris), Won = `Stage100` → Customer Nurture.
- Granularité dans des cartes de détail : MCL en compteur Marketing, étapes d'opp dans Sales, statuts client à part.
- **Volumes en flux, taux réels sur cohortes** (décision Greg, 2026-11-13). **Changement d'unité assumé et écrit sur le graphique** : personnes (leads + contacts) jusqu'au MQL, opps new business à partir de SAL.

## Marketing
**Filtres** : période (Last 7 days, Last 30 days par défaut, This month, Last month, This quarter, Last quarter, Year to date) · comparaison (Previous period, Same period last year) · **Attribution : First touch (défaut) / Last touch**, qui s'applique aux sources et aux Request Types de toute la page (sauf le pipeline, toujours en First Touch de l'opp).

**Définitions**
- **Entrée dans un stage pendant la période** = `FirstEntryDate{Stage}__c` ou `LastEntryDate{Stage}__c` dans la période. Chaque personne compte une fois ; les re-MQL sont inclus (F5).
- **Qui compte** : leads non convertis (`IsConverted = false`) + contacts, hors `Created by Yooz application` (F4, A3). Vérifié en octobre 2026 : 286 leads non convertis + 65 contacts = 351 MQL.
- **Courbes journalières** : chaque personne est placée au jour de sa dernière entrée dans la période (fuseau Salesforce de Greg).
- **MEL → MQL** = MEL entrés dans la période qui ont atteint MQL depuis le début de la période (`LastEntryDateMQL__c ≥ début`) ÷ MEL entrés.
- **SAL** = contacts avec `FirstEntryDateSAL__c` ou `LastEntryDateSAL__c` dans la période. **MQL → SAL** = MQL entrés qui ont atteint SAL depuis le début de la période ÷ MQL entrés.
- **New vs re-MQL** : first-time = `FirstEntryDateMQL__c` dans la période ; back from Recycle = MQL de la période − first-time.
- **Cohortes MEL → MQL** : 6 derniers mois d'entrée en MEL (`FirstEntryDateMEL__c`), % cumulé ayant une `FirstEntryDateMQL__c` au mois M0 à M+5. Indépendant du filtre de période. Échelle de couleur 0–10 % (les taux réels sont autour de 2–6 %).
- **Fiabilité** : pas de comparaison si la période de comparaison commence avant le 2025-10-01 (le delta disparaît).
- **Objectif MQL** : mois en cours, indépendant du filtre de période. Valeur provisoire 400 (à remplacer dans Config). Jours ouvrés = du jour même à la fin du mois, hors week-ends et jours fériés US (New Year, MLK, Presidents, Memorial, Juneteenth, July 4, Labor, Thanksgiving, Christmas).

**Sections et cartes**
- **Volume** : MELs (aire) · MQLs (barres) · à droite, empilés : MQL target du mois et New vs re-MQLs (les deux cartes de gauche s'étirent à la hauteur de la pile).
- **Trends** : MQLs per day (moyenne/jour, survol) · MEL → MQL cohorts.
- **Sources** : MQL mix by source (6 mois, survol) · Volume vs quality by source (MEL en x, MQL → SAL en y, seuils = volume moyen et taux global, étiquettes placées sans chevauchement, survol = détail) · tableau Performance by Source / Request type / Vertical.
- **Pipeline** : Marketing-sourced pipeline — opps `Souscription Yooz` créées dans la période, hors `Annulée`, par First Touch de l'opp. Colonnes : Opps · Avg deal · **Pipeline** (gras) · Won (texte vert si > 0, gris si 0) · Won %. Montants en USD (lignes lues avec `convertCurrency`, puis additionnées), en « $XXXk » sous 1 M et « $X.XXM » au-delà. Bouton « i » au survol : sources gardées et exclues.

**Sources marketing gardées** (pipeline) : Advertising, Email Marketing, Outsourced LeadsGen, Web Paid Search, Paid Social Media, Organic Social Media, Web Organic Search, Web Direct Traffic, Web ChatBot, Web Referral, AI Referrals, Tradefair, Partner, CPA Referral, Press relations. **Exclues** : Phoning - Sales Prospecting et Phoning - SDR Prospecting (outbound), Incoming Call / Email (direct sales), F2F / Meeting, Customer Referral, Personal Acquaintance, Request For Proposal (sales), 3rd Party Database (placeholder), ITESOFT leads (legacy), To be defined (fallback), Created by Yooz application (pas un lead). Source vide = Unknown, affichée. [À VALIDER : Greg]

**Couleurs des sources** (fixes, palette Pulse, sans bleu) : Advertising `#a855c7` · Email Marketing `#eb6834` · Outsourced LeadsGen `#1baf7a` · Web Paid Search `#eda100` · Paid Social Media `#e87ba4` · Partner `#008300` · Tradefair `#00a3b4` · Web Direct Traffic `#e34948` · Unknown (source vide) `#A1A1AA` · autres sources `#D4D4D8`. Les Request Types et verticales n'ont pas de couleur (règle Pulse : couleurs catégorielles réservées aux sources). Libellés anglais tirés des picklists Salesforce ; la verticale `Unknown` est fusionnée avec « vide ».

## Sales
**Filtre** : période uniquement (mêmes préréglages, Last 30 days par défaut). Conversions, vitesse, motifs de recycle et Won suivent la période ; la file de MQL et le pipeline ouvert sont en temps réel. Périmètre : NORAM, new business (`Type = 'Souscription Yooz'`).

**Réglages (Config)** : SLA premier contact MQL = 48 h, SAL inactif = 14 jours, objectif MQL → SAL = 15 %. Valeurs provisoires, à confirmer.

**Définitions**
- **MQL ouverts** : leads non convertis au statut `MQL` + contacts au statut `MQL`, entrés en MQL depuis le 2025-10-01 (les MQL legacy, F8, sont exclus). Propriétaire : `Owner` ; une file (queue) = Unassigned.
- **MQL non travaillé** (hors SLA) : entré en MQL depuis plus de 48 h et sans activité (`LastActivityDate`) depuis le jour d'entrée. [À CONFIRMER : `LastActivityDate` est-il le bon champ ?]
- **SAL inactif** : opp ouverte au `Stage10` dont la dernière activité (ou la création) date de 14 jours ou plus.
- **Stage conversion** : MQL → SAL = comme Marketing, objectif affiché à côté (« / 15 % ») ; SAL → SQL = opps créées dans la période qui ont atteint Stage 2 ou plus (ou gagnées) ÷ opps créées ; SQL → Won = gagnées ÷ celles qui ont atteint SQL (même cohorte d'opps).
- **Stage velocity** (transitions faites dans la période, médiane et 10ᵉ–90ᵉ percentile, plafonné à 120 j) : MQL → SAL = `LastEntryDateMQL__c` → `FirstEntryDateSAL__c` (contacts) ; SAL → SQL = création de l'opp → première sortie du `Stage10` (historique `OpportunityFieldHistory`) ; SQL → Won = première sortie du `Stage10` → `CloseDate` des opps gagnées ; MQL → Won = `LastEntryDateMQL__c` du contact principal → `CloseDate`.
- **Motifs de recycle** : MQL entrés dans la période puis recyclés (`LastEntryDateRecycle__c ≥ début`), par `leadStatusReasonWhy__c` / `ContactStatusReasonWhy__c`, regroupés en buckets [À VALIDER : Greg] :
  - BANT — Budget : `nurture_nobudg`, `out_price` · Authority : `nurture_not_decision_maker` · Need : `nurture_noprj`, `out_already_eqp`, `out_out_of_scope`, `nurture_uses_ERP` · Timing : `nurture_prj6M`, `nurture_prj1Y`, `nurture_in_research_phase`, `nurture_tbn_marketing`.
  - Hors BANT — Not reached : `nurture_noresp`, `nurture_call_blocked`, `nurture_first_call_needed`, `nurture_not_reached_yet`, `nurture_to_call_back`, `nurture_AE_not_reached` · Not a fit : `out_not_target`, `out_competitor`, `out_fake`, `out_leftCompany`, `nurture_emea_red_list` · Lost or churned : `recycle_lost_opportunity`, `recycle_churned`, `Churned` (valeur legacy) · Other : `nurture_reactivated` et toute valeur inconnue · No reason given : vide ou `none`.
- **Équipes** : calculées automatiquement depuis Salesforce (`User.Title`, `Department`, `UserRole`) pour les utilisateurs actifs NORAM qui possèdent au moins un lead, compte ou opp NORAM : département Channel/Alliances Partnerships → Channel ; département NORAM SDR ou titre SDR/BDR → SDR ; titre Account Executive → AE ; NORAM CSM → CSM ; le reste et les inactifs → Other. Correction manuelle possible dans Config → Sales (seules les corrections sont stockées dans `config/main.team`). Time in stage et Owners n'affichent que AE et SDR.

**Sections et cartes**
- **Conversion & SLAs** : Stage conversion (strip 3 cellules) · Sales handoff (MQLs not worked, Stale SALs, jauges en couleur de statut).
- **Queue & speed** : Time in stage · open MQLs (SDR par personne, puis AEs, Channel, Other owners agrégés, Unassigned ; buckets 0–2d à 30d+) · Stage velocity.
- **MQL acceptance** : Why MQLs are recycled sur toute la largeur (barres par bucket, colonnes BANT / Other outcomes séparées par un trait, survol = status reasons ; bouton « Show status reasons » qui déplie le détail dans la carte).
- **Open pipeline** : Open pipeline by stage (Stage 1 à 5 + No stage) : Opps, Pipeline, Avg age, No activity 14d+ (en rouge si > 0).
- **Owners** : sélecteur de période dans l'en-tête (lié au filtre de page et au CSM book) ; AEs puis SDRs (filtre All / AEs / SDRs), colonne Team : Open MQLs, Past SLA (rouge si > 0), Open opps, Pipeline, Won (vert). Sans équipes définies : tous les propriétaires + lien « Set up teams ».

- **Expansion · upsell and cross-sell** (CSMs) :
  - Périmètre : opps NORAM `Type = 'Vente additionnelle'`, **hors Price Increase** (opps « … - PI - MM/AAAA » créées par Système YOOZ, `OwnerId` `00520000001FUfMAAW`) et toute opp dont le nom contient « Price Increase » — jamais affichées. Crédit = owner de l'opp.
  - **Recurring vs services** : un upsell avec `Montant_recurrent_1an__c` = 0 (ou vide) est une prestation de services (customization, configuration, SSO, training, heures) ; son prix éventuel est dans `Amount` (one-time). Pas de champ dédié (Greg, 2026-11-13). Moyenne et médiane sur les upsells récurrents seulement ; CSM book : Won = récurrents, colonne Services à part.
  - 4 cartes KPI (Block standard, hauteur naturelle à la demande de Greg, au lieu du panel 216) : Upsells won ($, répartition CSMs / AEs / autres) · Upsell win rate (gagnées ÷ Gagnée + Perdue + Abandonnée, Annulée exclue, barre won / lost / abandoned) · Average upsell won (médiane, plus gros) · Open upsell pipeline (`En cours`, temps réel ; montant en Proposal ou Closing, opps dont la close date est passée).
  - Upsells closed by month (03·C, 6 derniers mois, indépendant du filtre) : gagnées en plein, perdues ou abandonnées hachurées, win rate en titre.
  - Open upsells by stage : Opps, Pipeline, No activity 14d+, Close date passed.
  - CSM book (sélecteur de période dans l'en-tête, lié au filtre de page) : par CSM (bucket CSM) : Clients, Open upsells, Pipeline, No activity 14d+, Won, Won amount, Won / 100 clients ; AEs et Other owners agrégés.
  - **Clients par CSM (suffisant pour la V1, décision Greg)** = comptes NORAM `Satut__c LIKE 'Client%'` dont le CSM est owner.

## RevOps (construit le 2026-11-14)
**Rôle** : la page « pourquoi ». Pas un agrégat de Marketing et Sales : elle décompose les chiffres, compare chaque étape à sa propre référence et pointe les pistes à creuser. Méthodes reprises du RevOps (bowtie Winning by Design, contrainte principale, mix vs taux, waterfall de pipeline, glissement, âge du pipeline).

**Filtre** : période (mêmes préréglages, **Last quarter par défaut**) · comparaison (Previous period, Same period last year). Périmètre fixe : NORAM, new business (`Souscription Yooz`, `Annulée` exclue), montants = 1-year recurring converti en USD, source = First Touch (lead ou opp). Historique utilisable : depuis le 2025-10-01 (13 mois, suffisant pour la V1, Greg).

**Données** : une seule lecture de toutes les opps new business NORAM utiles (ouvertes, ou créées / fermées depuis le début de la plus ancienne fenêtre, ≈ 1 900 lignes, paginées par `Id`), l'historique `SalesStagePick__c` depuis le 2025-07-01 (date d'entrée en SQL) et l'historique `CloseDate` de la période et de la comparaison (glissement). Comptages MEL/MQL comme Marketing.

**Définitions**
- **Opp fermée** : `Gagnée` (date = `CloseDate`), `Perdue` ou `Abandonnée` (date = `Date_Abandon_ou_Perte__c`, sinon `CloseDate`).
- **A atteint SQL** : `SalesStagePick__c` en `Stage20`–`Stage100`, ou `Gagnée`. **Entrée en SQL** = première ligne d'historique vers Stage 2+ (pour une opp créée avant le 2025-07-01 : seulement si elle quitte `Stage10`).
- **Global funnel (02·E2)**, valeurs « new this period » :
  - MEL / MQL = entrées dans la période (comme Marketing). Répartition : moved forward = a atteint le stage suivant depuis le début de la période (par date), plus les statuts plus avancés aujourd'hui ; still = statut actuel égal au stage ; recycled = `Recycle` ; disqualified = le reste (`Purge`, `Suspect`…).
  - Portes MQL : from MEL = first-time MQL avec une date MEL ; instant MQL = first-time sans `FirstEntryDateMEL__c` ; re-MQL = MQL − first-time.
  - SAL = opps créées dans la période : moved to SQL / still SAL (ouverte au Stage 1) / abandoned (recyclé) / lost.
  - SQL = opps entrées en SQL dans la période : won / still open / abandoned / lost.
  - Won = opps gagnées dans la période (montant sous le chiffre). Barre pleine.
  - Connecteurs = segment « moved forward » ÷ valeur du stage, delta en pts vs la comparaison.
  - Recycled pool = leads non convertis + contacts au statut `Recycle` **aujourd'hui** ; In = recyclés du MQL + abandons SAL + abandons SQL ; Back to MQL = re-MQL ; taux de retour = re-MQL ÷ pool.
- **Biggest constraint** : pour chaque étape, taux de la période vs taux sur 12 mois glissants (bornés au 2025-10-01). MEL → MQL et MQL → SAL en flux ; SAL → SQL et SQL → Won sur les opps **fermées** dans la fenêtre (pas de biais de maturité). Bookings at stake = entrées de la période × écart de taux × taux aval actuels × deal moyen gagné sur 12 mois. Classement par montant.
- **SAL → Won cohorts (04·A)** : opps créées par mois (8 derniers mois), % cumulé gagné de M0 à M+5, échelle 0–25 %.
- **Bookings bridge** : bookings = opps créées × win yield (gagnées ÷ créées, même période) × deal moyen, avec un effet mix de sources (taux de la comparaison appliqués au mix actuel). Décomposition log-moyenne (LMDI) : les 4 effets s'additionnent exactement. Win yield mélange les cohortes : tendance, pas un vrai win rate (écrit sur la carte).
- **Pipeline dynamics** : Pipeline created ($, vs comparaison) · Slipped out (deals dont la close date était dans la période et a été repoussée après, pendant la période ; % des deals « dus » = glissés + fermés) · Aged pipeline (ouvertes plus vieilles que 2× le cycle médian des deals gagnés sur 12 mois, temps réel) · Overdue close dates (ouvertes avec close date passée). Pipeline flow : ouvert au début → + créé − gagné − perdu − abandonné = ouvert à la fin (montants d'aujourd'hui, l'historique des montants n'existe pas). Open pipeline by age : 6 tranches, celles au-delà de 2× le cycle médian en rouge hachuré.
- **Where and why deals die** : opps perdues ou abandonnées dans la période, étape au moment de la fermeture × bucket de motif (`Raisons_PerteAbandon__c`, multi-select rempli à 100 % ; une opp peut compter dans plusieurs buckets). Filtre All / Lost / Abandoned. Buckets [À VALIDER : Greg] :
  - Ghosted : Prospect ne répond plus · No real project : Pas de réel projet, Projet pas faisable · Paused : Projet en pause, Projet abandonné, Trop tard, Réorganisation interne, Budget supprimé/réalloué, Le porteur du projet a quitté l'entreprise, Faillite, Compliance - Difficultes economique · Status quo : Garde ses procédures manuelles, Gains pas suffisants, Solution développée en interne · Price : Prix - Moins cher, Prix - Modèle tarifaire, Compliance - Prix trop élevé · Product gap : toutes les « Fonctionnalités - … » et « Features - … », Compliance - Manque de fonctionnalités, Intégration, Implémentation, Interface utilisateur, OCR, SOC-2, Support Couverture horaire, Périmètre international non couvert · Not a fit : cherchait AR / on-premises / outsourcing / GED, Yooz trop complet, Non concerné par la réforme · Decided above : Choix dicté par un partenaire, Décision Groupe, Recommandation · Not a real deal : Interne - Doublon, Interne - Partenaire, Erreur sur la devise, Doublon opportunité gagnée, Résiliation annoncée, Résiliation administrative · Undisclosed : Pas divulguées, Autre, vide.
- **Competitors** (12 derniers mois, indépendant du filtre) : deals fermés où le concurrent est dans `CompetitorsPick__c` ou est celui contre qui on a perdu (`Concurrent__c`) ; Won, Lost to them, Win rate (chip vs tous les deals fermés), Avg deal, étape la plus fréquente à la perte. « Other » et « No competitor recorded » épinglés en bas. Libellés lus dans les picklists (ex. Beanworks = Quadient, DocuPhase = onPhase).
- **Segment economics** (Source / Vertical / ERP) : MQLs entrés (pas pour ERP), opps créées, win rate / deal moyen / cycle médian / bookings sur les deals fermés de la période, bookings par MQL ; pour Source, Spend (budgets Config) et Cost / won.
- **Signals** (en tête de page, 6 maximum, classés par bookings en jeu) : biggest lever ; win rate d'une source ou d'une verticale qui s'écarte de ≥ 8 pts de son taux sur 12 mois (≥ 8 deals fermés, ≥ 20 sur 12 mois) ; opps créées par source en variation de ≥ 40 % (≥ 10) ; MQL par source en variation de ≥ 30 % (≥ 30) ; ≥ 40 % des deals perdus morts au Stage 1 (≥ 10 deals) ; ≥ 20 % des deals dus qui glissent (≥ 5) ; concurrent battu moins d'une fois sur deux par rapport à la moyenne (≥ 6 deals) ; ≥ 30 % du pipeline ouvert vieilli. Chaque ligne a un lien « View → » vers la carte concernée. Seuils provisoires, à ajuster à l'usage.

**Sections et cartes** : Signals · Revenue engine (Global funnel pleine largeur ; Biggest constraint | SAL → Won cohorts) · What changed (Bookings bridge | What drove it ; Change by source) · Pipeline dynamics (4 KPI ; Pipeline flow | Open pipeline by age) · Where deals die (Where and why deals die ; Competitors) · Segment economics.

**Coûts** : budgets saisis à la main dans Config → RevOps (Outsourced LeadsGen, Tradefair, et en attendant Supermetrics : Web Paid Search, Paid Social Media, Advertising), par mois, en USD, proratisés au jour sur la période. Prochaines étapes : connecteur Supermetrics pour les Ads, budget salons 2027 plus granulaire.

## Config
Sous-onglets (Tabs 06·A, sans badge) : **General** (connexions) · **Marketing** (objectif MQL mensuel) · **Sales** (objectif MQL → SAL, SLA, SAL inactif, équipes AE / SDR / CSM / Channel / Other calculées depuis Salesforce, corrigeables par menu, « Edited · reset ») · **RevOps** (budgets canaux par mois, 12 mois glissants, stockés dans `config/main.budgets`) · **Health** (seuils, contrôles).

## Tri des tableaux
Tous les tableaux de données se trient en cliquant sur l'en-tête (09·H) : 1er clic = du plus grand au plus petit (ou A→Z pour le texte), 2e clic = inverse. Les lignes Unknown / Unassigned / agrégées restent en bas, les vides en dernier. Pas de tri sur les tableaux groupés (détail des status reasons, équipes dans Config) ni sur les contrôles de Config.

## Health — les 14 contrôles
Score d'une dimension = 1 − Σ enregistrements en échec ÷ Σ populations des contrôles de la dimension. Un contrôle est en échec au-dessus de sa tolérance (0 par défaut). Sévérité, tolérance et lien de correction se règlent dans Config.

| Dimension | Contrôle | Règle | Population | Source Brain | Valeur 2026-11-13 |
|---|---|---|---|---|---|
| Completeness | Missing stage entry date | Lead/Contact MEL, MQL, Recycle sans `FirstEntryDate*__c` correspondant | Leads + contacts MEL/MQL/Recycle | lead-journey, reliability | 2 |
| Completeness | Engaged leads with no vertical | Lead MEL/MQL/Recycle, `VerticalPick__c` vide | Leads engagés | lead-contact | 5,303 |
| Completeness | Recycled with no reason | `leadStatusReasonWhy__c` / `ContactStatusReasonWhy__c` vide | Recycle leads + contacts | lead-contact | 0 |
| Completeness | Open opps with no primary contact | `StageName = 'En cours'`, `Interlocuteur__c` vide | Opps ouvertes | opportunity, O4 | 3 / 460 |
| Completeness | Open opps with no sales stage | `SalesStagePick__c` vide | Opps ouvertes | opportunity, O1 | 14 / 460 |
| Consistency | Primary contact out of step | Stage10 ≠ SAL/SAC, Stage20–50 ≠ SQL/SQC | Opps ouvertes Stage10–50 avec contact | F9 + décision Greg | ≈ 49 / 446 |
| Consistency | EMEA status on NORAM records | Statut `Out`, `Nurture`, `Nurture Marketing`, `Piste` | Leads + contacts NORAM | F11, lead-journey | 12 |
| Consistency | **Won opps not at Stage 100** (ajouté le 2026-11-14) | `Gagnée`, `SalesStagePick__c` ≠ `Stage100` ou vide | Opps new business gagnées créées depuis le 2025-10-01 | opportunity + décision Greg | 18 / 156 |
| Consistency | Purchased source on engaged leads | Lead MEL/MQL/Recycle en `3rd Party Database` | Leads engagés | A4 | 0 |
| Sync | Contacts missing in Salesforce | Critères inclusion list (groupe 1), sans ID Lead/Contact SF, créé avant hier | Contacts HubSpot éligibles | hubspot-salesforce, D1 | 168 |
| Sync | Score ≥ 100 but not MQL | HubSpot score ≥ 100, `leadstatus` Suspect/MEL/Recycle | Contacts HubSpot score ≥ 100 | scoring, S1 | 54 |
| Attribution | Engaged records with no source | `LeadSource` vide, leads MEL/MQL/Recycle + contacts MEL→SQL | idem | attribution | 9,316 (contacts) |
| Attribution | Fallback source on new MELs | `Advertising` ou `To be defined`, MEL entré sur 90 j | Leads MEL 90 j | A2 | 667 / 2,365 |
| Attribution | Open opps with no source | `Opportunity.LeadSource` vide | Opps ouvertes | attribution (règle par défaut) | 148 / 460 |

Fraîcheur (bloc de la sidebar) : Salesforce = dernière modification d'un Lead ; HubSpot = `salesforcelastsynctime` le plus récent. Au-delà de 60 min : « Sync may be delayed ».

## Constats à remonter dans le Brain
1. **Contacts engagés en `3rd Party Database`** : détail et théories dans `claude/questions_brain.md` (flag F-CONTACT-3PD), à voir avec Chris.
2. 9,316 contacts NORAM MEL→SQL sans First Touch (les leads engagés : 0).
3. 28 % des nouveaux MEL (90 j) sont encore en `Advertising` : le nouveau fallback n'est pas visible (cohérent avec A2).
4. 32 % des opps NORAM ouvertes n'ont pas de First Touch.
5. 15,750 contacts NORAM sans `LifecycleStatus__c` (pas encore de contrôle).
6. Les opps d'upsell portent des contacts SAC/SQC : la correspondance stage ↔ statut doit accepter les deux familles.
7. **Montants agrégés en EUR** : en SOQL, `SUM(Montant_recurrent_1an__c)` (et même `SUM(convertCurrency(...))`) renvoie la devise de l'organisation, l'**EUR** (vérifié avec `FORMAT()` : « EUR 822,168.70 »), pas l'USD. Pour un total en USD, lire les lignes avec `convertCurrency()` et additionner. Complète le piège O3.
8. **Étapes d'opp sautées** : l'historique `SalesStagePick__c` montre souvent Stage10 → 20 → 30 → 40 en quelques secondes (saisie en rafale). La vitesse SAL → SQL mesurée sera donc proche de 0 et ne reflète pas le vrai cycle.
9. **Opps zombies** : 12 opps new business NORAM encore `En cours` créées en 2022–2024 (et 30 de 2025), souvent sans étape ni montant. Elles gonflent l'âge moyen du pipeline. Candidat à un contrôle Health.
10. **Open MQLs par équipe (2026-11-13)** : 675 MQL ouverts NORAM → SDR 49 %, Channel 28 % (Jessica Hopkins 77, John Parisi 58), AE 17 %, Other 5 %. Les MQL Channel : leads partenaires ? À confirmer.
11. **Upsells « PI - MM/AAAA »** : 555 des 1 079 upsells NORAM gagnés en 2026 sont des opps créées par Système YOOZ (« PI », probablement Price Increase). À sortir des stats CSM.
12. **Upsells de services** : sur 30 jours, 22 des 42 upsells NORAM gagnés ont un récurrent 1 an à 0 (prestations, $19,3k en `Amount`). Une opp mixte « GSH - … VENDOR STATEMENTS end offer + Price Increase » est exclue par le filtre « Price Increase » alors qu'elle contient un add-on.
13. **Opps new business NORAM créées depuis le 2025-10-01 (relevé 2026-11-13)** : 1 316 opps → 156 gagnées, 136 perdues, 764 abandonnées, 255 ouvertes, 5 annulées. **72 % des fermées sont « Abandonnée »**, dont 48 % au Stage 1. Motif de perte (`Raisons_PerteAbandon__c`) rempli à 100 %.
14. **Win rate par First Touch de l'opp** (gagnées ÷ fermées, même relevé) : Customer Referral 67 %, Incoming Call 41 %, Partner 39 %, Web Direct 13 %, Email 12 %, Web Paid Search 10 %, Tradefair 8 %, Outsourced LeadsGen 4 %, Advertising 3 %, Paid Social 0 / 58.
15. **Concurrents** : Ramp = 17 des 136 deals perdus, premier concurrent identifié (35 « Other », 19 « pas obtenu »). `CompetitorsPick__c` rempli sur ≈ 217 opps (36 gagnées, 93 perdues).
16. **5 opps gagnées en `3rd Party Database`** : contredit l'idée d'un placeholder pur. À regarder plus tard (Greg).
17. **18 opps gagnées pas au `Stage100`** : ajouté à Health (Won opps not at Stage 100).
18. **ERP sur l'opp** : `ERP__c` (formule texte) contient des valeurs comme `NetSuite`, `Intacct`, `Autre` (affiché « Other »).
19. **Instant MQL** : au T3 2026, 335 leads first-time MQL n'ont pas de `FirstEntryDateMEL__c` (entrée directe en MQL, ex. demo request).

## Écarts au design system (fallbacks à valider)
- En-tête de page « Pulse / … » au format breadcrumb (pas d'en-tête de page validé), sidebar 240 px avec le nom « Pulse » en 15/600, survol des items en `#F4F4F5`.
- Sidebar : bloc 06·D Freshness déplacé dans la nav (padding 16 au lieu de 20), avec un bouton icône Sync 28 px (icône duotone 16 px) ; icône Settings créée (hors librairie).
- Champs de Config composés à partir des tokens (28 px, `#F4F4F5`, r10) ; sévérité via le segmented control.
- Bannière d'alerte affichée seulement s'il y a un contrôle Critical (seule variante validée).
- État « Sync may be delayed » du bloc Freshness (O9 ouvert).
- Icônes sans équivalent : Config = Settings (créée), tuiles Consistency = Alert, RevOps = Analytics.
- Pas de Search dans la sidebar en V1 ; pas de barre de filtres sur Health (vue instantanée, NORAM fixe).
- Marketing :
  - Titres de section hors carte (demande de Greg) en `card.title` 15/600 `#18181B`, 16 px au-dessus du contenu, 32 px entre sections.
  - Ligne Volume : MELs et MQLs étirés à la hauteur de la pile MQL target + New vs re-MQLs (au lieu des 216 px fixes).
  - « MQLs per day » affiche la moyenne par jour ; survols ajoutés (O12) au format tooltip 09·K, point creux de 03·A sur la courbe, contour `#18181B` de 02·E2 sur les segments et points.
  - Cohortes : échelle 0–10 % (Pulse : 25 %), lignes de 6 mois glissants.
  - Scatter : axe Y adaptatif (arrondi au-dessus du meilleur taux), placement automatique des étiquettes ; étiquette masquée si aucune place (détail au survol).
  - Pipeline : popover d'info (« i » de 09·F) multi-lignes au survol, padding 16 — pas de pattern validé pour un texte d'aide ; colonne Won en texte vert (demande de Greg).
  - Pas de « Custom range… » dans le sélecteur de dates (V1) ; pas d'annotations ; colonnes du tableau de performance réduites à MEL → SAL ; swatch `#D4D4D8` pour les sources hors palette.
- Sales :
  - Strip Stage conversion à 3 cellules ; Sales handoff sans changement d'anatomie (règle du 2ᵉ indicateur raccourcie en « inactive 14+ days » pour tenir à côté de « View → »).
  - Time in stage limité aux MQL (pas de SAL), 5 propriétaires + « N other owners » + Unassigned ; cartes Queue & speed en hauteur minimale 380 px au lieu de 380 fixes.
  - Velocity : bas/haut = 10ᵉ/90ᵉ percentile (O14 ouvert), note explicative sous l'axe, survol par ligne.
  - Tableaux Open pipeline et Owners : chiffres en rouge pour les retards (« No activity », « Past SLA ») et en vert pour Won.
  - Sales handoff étiré à la hauteur de Stage conversion (spec : hauteur naturelle), demande de Greg.
  - Tooltip multi-lignes (liste des status reasons d'un bucket) ; 09·K est sur une ligne.
- Config : Tabs avec un onglet Marketing/Sales/RevOps/Health alors que 06·A dit de ne pas répéter les sections de la sidebar (demande de Greg). Grille de budgets (12 mois × 5 sources) composée de champs `.field` en 12 px.
- RevOps — **nouveaux composants (créés, hors librairie, à valider)** :
  - **Signals** : tableau sur la base de 06·B (ligne 64 px, icône 40 px, deux lignes de texte) sans pill de sévérité ; Now / Baseline / Change (chip de tendance) / « View → ».
  - **Biggest constraint** : liste classée de 4 étapes, barres horizontales sur le modèle des buckets de recycle (Sales), titre en `metric.m` = l'étape qui pèse le plus.
  - **Waterfall** (Bookings bridge, Pipeline flow) : total de départ `#D4D4D8`, hausses accent 30 %, baisses hachurées (hatch.secondary), total de fin accent, liaisons pointillées `#A1A1AA`, libellés et valeurs sous le graphe (aucun texte dans la zone de tracé, R1). Pipeline flow en valeurs neutres `#52525B` (gagner n'est pas « mauvais »).
  - **What drove it** : petit tableau Driver / Before / Now / Effect (lignes 48 px, sous-titre gris).
  - **Where and why deals die** : carte de chaleur étape × motif sur la rampe volume (04·B/04·C) ; colonne « Undisclosed » en gris neutre (équivalent Unknown) ; ligne de totaux.
  - **Open pipeline by age** : barres horizontales ; tranches trop vieilles en libellé rouge et barre hachurée.
- RevOps — **écarts au Global funnel 02·E2** :
  - 5ᵉ stage = **Won** (au lieu d'Opportunity) : barre pleine « closed won », montant gagné en sous-titre.
  - SAL et SQL sont des opps : « recycled » = **abandoned** et « disqualified » = **lost** dans les survols (légende inchangée).
  - Pool : légende « right now » au lieu de « at end of period » (le stock Recycle n'est pas historisé) ; il inclut tout le stock Recycle (≈ 63 000), d'où un taux de retour très faible.
  - Les portes MQL (from MEL / instant / re-MQL) ne respectent pas exactement « from MEL = MEL moved forward » (les first-time MQL avec date MEL ne sont pas tous des MEL de la période).
  - Note de bas de carte « People, then deals » (footnote optionnelle de la spec).

## Questions ouvertes
- Idée Health (plus tard) : opps `Souscription Yooz` dont l'owner est un CSM → souvent un upsell mal typé.
- V2/V3 : onglet CSM dédié (santé client, usage, churn).
- [À CONFIRMER] Utilisateur d'intégration HubSpot dans Salesforce (pour une fraîcheur SF plus juste).
- [À VALIDER] Liste des sources marketing gardées dans le pipeline.
- [À CONFIRMER] SLA MQL (48 h), seuil SAL inactif (14 j), objectif MQL → SAL (15 %).
- [À CONFIRMER] Champ d'activité Sales : `LastActivityDate` (tâches et événements loggés) suffit-il comme preuve qu'un MQL a été travaillé ?
- Seuils de score (95/95/98/95) = valeurs de référence Pulse, à fixer dans Config.
- Objectif MQL mensuel : 400 provisoire.
- Objectif de bookings NORAM : pas encore (Greg, 2026-11-14). À ajouter dans Config quand il existe, pour la couverture du pipeline.
- [À VALIDER] Buckets de motifs de perte RevOps et seuils des Signals.
- [À VALIDER] Nouveaux composants RevOps (Signals, Biggest constraint, Waterfall, What drove it, carte de chaleur des pertes) à ajouter au design system Pulse.
- Supermetrics : brancher les coûts Ads (Spend, Cost / won) quand le connecteur sera disponible.

## Réglé
- 2026-11-13 · URL Salesforce = `https://getyooz.lightning.force.com`.
- 2026-11-13 · `Stage50` (Closing) = SQL ; Won = `Stage100`.
- 2026-11-13 · Attribution Marketing : First touch par défaut, Last touch au choix.
- 2026-11-13 · Marketing organisé en 4 sections : Volume, Trends, Sources, Pipeline.
- 2026-11-13 · Config en sous-onglets ; équipes AE / SDR / CSM / Channel / Other calculées depuis les titres Salesforce, corrigeables à la main.
- 2026-11-13 · Anneau MQL acceptance retiré : l'objectif MQL → SAL s'affiche dans Stage conversion.
- 2026-11-13 · Section Expansion (CSMs) dans Sales : additional sales uniquement, Price Increase exclues, crédit = owner de l'opp.
- 2026-11-13 · Sales organisé en 5 sections : Conversion & SLAs, Queue & speed, MQL acceptance, Open pipeline, Owners.
- 2026-11-14 · Funnel RevOps : volumes en flux, taux sur cohortes ; changement d'unité (personnes → opps) assumé et écrit ; 13 mois d'historique suffisent.
- 2026-11-14 · RevOps organisé en 6 sections : Signals, Revenue engine, What changed, Pipeline dynamics, Where deals die, Segment economics.
- 2026-11-14 · Budgets canaux saisis à la main dans Config → RevOps en attendant Supermetrics.
- 2026-11-14 · Contrôle Health « Won opps not at Stage 100 » ajouté.
