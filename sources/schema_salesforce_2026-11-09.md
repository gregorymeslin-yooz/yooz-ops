# Source — Schéma Salesforce et comptages (9/11/2026)

*Extrait par Claude via le connecteur Salesforce (getObjectSchema + SOQL), le 9/11/2026. Niveau de confiance 3 (schéma réel) : fait foi pour les noms API, types et valeurs de picklist, pas pour le sens métier.*

## Lead — Status (`Status`)

Valeur API ≠ libellé. Toute requête SOQL doit utiliser la **valeur API**.

| Valeur API | Libellé | Leads NORAM au 9/11/2026 |
|---|---|---|
| `Suspect` | Suspect/MCL | 1 840 290 |
| `Converti` | Converted | 137 780 |
| `Purge` | Purge | 92 867 |
| `Recycle` | Recycle | 50 545 |
| `MEL` | MEL | 20 103 |
| `MQL` | MQL | 765 |
| `Out` | Out | 7 |
| `Nurture Marketing` | Marketing Nurture (Inquiry) | 0 |
| `Nurture` | SDR Nurture (MQL) | 0 |
| `Piste` | Lead Lead (SAL) | 0 |
| `Portal Sign-Up` | Portal Sign-Up | 0 |

Total Leads NORAM (`SalesTerritory__c = 'NORAM'`) : 2 142 357.
Constat : les statuts Nurture / SDR Nurture / Lead Lead ne sont pas utilisés en NORAM (vocabulaire EMEA).

## Contact — Lifecycle Status (`LifecycleStatus__c`)

Picklist **différente** du Lead : `Suspect`=MCL, MEL, MQL, SAL, SQL, Recycle, Purge, Customer Nurture, MEC, MQC, SAC, SQC.

Contacts NORAM (filtre `Account.Sales_Territory__c = 'NORAM'`) :
MEC 89 566 · MEL 72 287 · Customer Nurture 25 326 · Suspect 24 304 · (vide) 15 409 · Recycle 13 282 · SAL 1 117 · Purge 830 · MQC 781 · SQL 367 · SQC 272 · SAC 178 · MQL 150 · valeurs hors picklist : Nurture 9, Piste 3, Nurture Marketing 1.

Lifecycle Status Reason (`ContactStatusReasonWhy__c`) a aussi des valeurs propres au Contact : `recycle_churned`, `recycle_lost_opportunity`, `nurture_reactivated`, `nurture_first_call_needed`…

## Dates d'entrée en statut (Lead, NORAM) — première valeur non vide

| Champ | API | Première date |
|---|---|---|
| MQL first entry date | `FirstEntryDateMQL__c` | 20/09/2025 |
| MEL first entry date | `FirstEntryDateMEL__c` | 20/09/2025 |
| Recycle first entry date | `FirstEntryDateRecycle__c` | 20/09/2025 |
| First Mktg Nurture Status Entry Date | `FirstNurtureMarketingStatusEntryDate__c` | 28/04/2025 |
| First SDR Nurture Status Entry Date | `Date_First_Nurture_Status__c` | 23/04/2024 |
| First Activation Date (datetime) | `FirstActivationDate__c` | 30/10/2019 (historique repris ?) |
| Last Hubspot Demand Date Yooz | `Last_Hubspot_Demand_Date_Yooz__c` | 28/02/2024 |

→ Confirme l'inventaire (MQL fiable depuis le **20/09/2025**). Le fichier 02 dit « octobre 2025 » : à corriger.

## Sales Territory — nom API différent selon l'objet

| Objet | API | Type |
|---|---|---|
| Lead | `SalesTerritory__c` | picklist |
| Account | `Sales_Territory__c` | picklist |
| Opportunity | `Sales_Territory__c` | **texte** |
| Campaign | `Sales_Territory__c` | **multi-picklist** |
| Campaign Member | `Sales_Territory_of_Account__c` | texte (territoire du compte) |
| Contact | `Sales_Territory__c` (« Contact Territory ») + `TECH_AccountSalesTerritoryAuto__c` | picklist / texte — corrigé le 10/11/2026 |

Valeurs : EMEA French Speaking, EMEA Non-French Speaking, FR, NORAM, UK, SP, LATAM (APAC cité dans l'aide mais absent de la picklist).
Lead : `SalesTerritoryIsLocked__c` permet un territoire différent du pays.

## Sources et attribution

- **First Touch Lead Source** : `LeadSource` sur Lead, Contact, Opportunity, Campaign Member. Sur Account : `Origine_de_la_piste__c` (libellé « First Touch Lead Source ») **et** `AccountSource` existent tous les deux.
- **Last Touch Lead Source** : `Last_Touch_Lead_Source__c` (Lead, Opp, Account) ; `LastTouchLeadSourcePick__c` (Campaign Member).
- Valeurs (26) : 3rd Party Database, Advertising, AI Referrals, CPA Referral (`Prescription_Experts`), Created by Yooz application, Customer Referral, Email Marketing, F2F / Meeting, Incoming Call / Email, ITESOFT leads, Organic Social Media, Outsourced LeadsGen, Paid Social Media, Partner, Personal Acquaintance, Phoning - Sales Prospecting, Phoning - SDR Prospecting, Press relations, Request For Proposal, To be defined, Tradefair, Web ChatBot, Web Direct Traffic, Web Organic Search, Web Paid Search, Web Referral.
- **Lead Source - Detail** : `Origine_de_la_piste_precision__c` (textarea).
- **Primary Campaign Source** (Opp) : `CampaignId`.
- **Request Type** : Lead `FirstTouchRequestType__c` (premier type hors import) et `Type_de_demande__c` (libellé « Last Touch Request Type »). Sur l'Opp : `Type_de_demande__c` = « Request type (origin) » et `FirstTouchRequestTypePick__c`.
- **HubSpot** : `Source_Hubspot__c` (= hs_analytics_source, Original Source), `Latest_Source_Hubspot__c` (= hs_latest_source), `External_Source__c` (sources tierces type Popl).
- **UTM** (Lead) : `utm_campaign_lead__c`, `utm_source_lead__c`, `utm_medium_lead__c`, `utm_content_lead__c`, `utm_term_lead__c` — last touch, écrasés à chaque formulaire. Campaign Member : `UtmCampaign__c`, `UtmSource__c`, `UtmMedium__c`, `UtmContent__c`, `UtmTerm__c` — copiés à la création du membre.
- **Deux dates de dernière demande** : `Date_Derni_re_Demande__c` (inclut newsletter et (dés)abonnements) vs `Last_Hubspot_Demand_Date_Yooz__c` (les exclut, rempli depuis 2024). `First_Conversion_Date__c` = première soumission de formulaire HubSpot (« First Demand Date »).

## Scoring (Lead)

- `BehavioralScore__c` « US Behavioral Score » : 0–100, calculé dans HubSpot (formulaires, visites, demandes d'import), sync two-way (HubSpot `noram_behaviour_score_fro`).
- `PersonaGradePick__c` « US Persona Score » : A/B/C/D, calculé dans HubSpot selon la fonction.
- `CompanyScorePick__c` « US Company Score » : A/B/C/To be defined, fourni par AlignICP.
- `Score_HubSpot__c` « Probability Score » (aussi sur l'Opp : score du contact à la conversion).
- `TECH_IsAllQualifiedInfo_Auto__c` « Ready for MQL ».
- `EngagedLeadPick__c` « Hubspot - Engaged Lead » : Engaged si ≥1 ouverture sur les 11 derniers emails ou ≥1 clic sur les 15 derniers.
- `IsProbanceEngaged__c` « Probance - Engaged Lead ».

## Opportunity

- **Stage** `StageName` : `En cours`=In progress, `Gagnée`=Won, `Perdue`=Lost, `Abandonnée`=Abandoned, `Annulée`=Cancelled.
- **Sales stage (US)** `SalesStagePick__c` : Stage 1 Qualification → Stage 5 Closing, Stage final Closed won (détail de « In progress »). Forecast stage `ForecastStagePick__c` dérivé.
- **Type** `Type` : `Souscription Yooz`=Yooz Subscription, `Vente additionnelle`=Additional Sale, Yooz Free. `AdditionalSaleTypePick__c` : Upgrade, Upsell, Downgrade, Downsell. `Sous_Type__c` : Upsell, Downgrade, Roll-Out.
- **Montants** : `Amount`, `Montant_recurrent_1an__c` (1-Year Recurring Amount), `TECH_Delta_recurrent_sur_1_An__c` (1-Year Recurring Delta New), `Montant_Gagne__c`, `Montant_En_Cours__c`.
- `LastStageChangeDate` (standard), `Date_Abandon_ou_Perte__c`, `Interlocuteur__c` (Contact), `Account_ID_case_insensitive__c` (ID 18 car.).
- Opportunity Contact Role : `Role` (Decision Maker, Champion (Yooz), Champion (Competitor), Influencer, User, Executive Sponsor, Gatekeeper, Technical Influencer, Business Buyer, Detractor, Legal reviewer), `IsPrimary`.

## Account

- **Prospect/Client Status** `Satut__c` (sic) : Prospect, Prospect Out, Prospect Try…, Prospect Buy, Client, Client BPO, Client Out, Client Payment Dispute, Client Suspended, ITESOFT leads, Test, Production… Sur le Contact : `TECH_AccountProspectClientStatusAuto__c`.
- **Partner/Reseller Status** `Type_de_partenaire__c`.
- `Lead_ID_converted_from__c` : Lead d'origine, « Not from a lead » si compte créé directement.
- `RetentionLifecycleStatusPick__c` (niveau 2).

## Campaign / Campaign Member

- Campaign `Type` (18 valeurs, FR/EN mélangées) : Advanced, Advanced Replay, Business Drink, Client/Signed Partner engagement, Customer Testimonial, Database Purchase, Display / Banners, Educational, Educational Replay, Emailing, Leads Generation, Operational, Paid Search, Paid Social, Partner or Reseller Events, Tradefairs, Training, White Paper.
- Campaign `Status` : In progress, Done, Canceled, Planned. `CampaignObjectivePick__c` : Acquisition / Retention. `Target__c` : Business/Expert/Partner. `VerticalPick__c`. Coûts : `BudgetedCost`, `ActualCost`, `CostPerMemberAuto__c`.
- Campaign Member `Status` : 22 valeurs mélangées FR/EN (Sent, Registered, Inscrit, Attended, Présent, Responded, Répondu…). `HasResponded`, `FirstRespondedDate`, `CreatedDate`, `Date_creation_membre__c`, `AccountId__c` (texte), `Nouvelle_Piste__c` / `Nouvelle_Piste_Net__c` (New Lead / Net New Lead).

## Task / Event

- `ActivityDate` libellé « Due Date Only ». Task : `CompletedDateTime`. Event : `StartDateTime`, `EndDateTime`.
- `Type` (Call, Meeting, Other ; Email sur Event) **et** `Type_de_T_che__c` « Task Type » (Email, Call, Other, No action required). `Subtype__c` (Product demo, Lead processing, Marketing…).
- BookIt : `BookItMeetingOriginPick__c` (BookIt Form = formulaire web + routage LeanData ; BookIt Link = lien direct), `BookItMeetingRsvpStatus__c`, `BookItMeetingStatus__c` (Canceled / Rescheduled, vide = maintenu).

## Glossaire de Chris (export du 9/11/2026)

- 422 lignes Lead (339 live, 83 depreciated), 13 catégories. Apporte : définitions, nom API HubSpot, **sens de la sync** (Two way / Always use Salesforce / Prefer Salesforce unless blank) et commentaires d'audit.
- Les 24 lignes non-Lead (Account_Tier__c, Parrainage_Count__c…) **n'existent pas dans le schéma réel** : lignes d'exemple, à ignorer.
