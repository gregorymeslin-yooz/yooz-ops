# Scoring — NORAM Behaviour Score (FRO)

*Status: Drafted · Sources: HubSpot scoring screenshot (2026-11-10), HubSpot property definitions (2026-11-10), Greg (voice notes) · Validator: Greg*

## What the score does
- HubSpot score **"NORAM Behaviour Score (FRO)"** (`noram_behaviour_score_fro`), synced two-way to Salesforce `BehavioralScore__c`.
- **≥ 100 → MQL**, set by a Salesforce flow when the score changes; this applies from MEL or from Recycle. Persona and Company scores play no part.
- **100 is a threshold, not a cap.** A score can go higher (75 + 75 = 150) but means nothing more: 125 and 175 are both simply MQL. Never rank MQLs by score. Tool limits: ±500 per score type, ±1,000 combined, plus each group's cap (below).
- **Only MEL and above are scored.** Suspects (MCL) are not scored. In practice they show 0 in both systems (all 1.84M NORAM Suspects in Salesforce on 2026-11-10), and that 0 means "not scored", not "engaged with nothing". Scoring starts when the lead becomes MEL (form, import…), so a lead whose first action was a form starts with points.
- **Reset**: a HubSpot workflow resets the score when the status changes (e.g. Recycle → 0).
- **MQL stays MQL** until Recycle or a manual move forward, even if decay pulls the score below 100. Check on 2026-11-10: 285 of the 768 NORAM Leads in `MQL` (37%) score below 100, 174 of them at exactly 0. Among the 271 that entered MQL in the last 90 days, 42 are below 100 (18 at 0). A 0 on an MQL is not supposed to happen. It comes either from a forgotten MQL whose points decayed over time, or from an unfixed bug; recent MQLs at 0 are too new for decay to explain, so flag them as anomalies. Also, 250 current MQLs entered MQL before 2025-10-01, i.e. at or before the migration.

## How it is built (as understood)

| Group | Type | Group cap | Decay | What scores |
|---|---|---|---|---|
| RT Behaviour | Property | ±200 | — | The **current value** of 4 Request Type properties, each scored separately |
| Form interactions | Event | ±150 | −50% every 6 months | Form submissions, page visits and ad interactions, **scored every time** they happen |
| Email Clic | Event | ±30 | None | Marketing email clicks, by frequency |
| Project Declaration Date | Property | None | — | +75 while a project is declared, cancelled after 30 days |

**Two scoring paths.** Leads who come by themselves (website forms) are scored by **Form interactions**. Imported leads (manual or automatic imports, trade shows, databases…) are scored by **RT Behaviour**, through the Request Type stored at import. The form list and the Request Type list contain duplicates and legacy values. Never add up the rules to explain a score: read the contact's actual properties and events.

### 1. RT Behaviour (property group, cap ±200)
Four contact properties, each with the same value → points table:

| Label in the scoring tool | HubSpot internal name |
|---|---|
| Request Type at Latest Import (1) | `request_type_at_latest_conversion` |
| Request Type at Latest Import (2) | `request_type_at_latest_import__2_` |
| Request Type at Latest ads activation (1) | `request_type_at_latest_ads_activation__1_` |
| Request Type at Latest ads interaction (2) | `request_type_at_latest_ads_interaction__2_` |

These are **intermediate Request Types**. They let Yooz score imports and keep a first/last history without overwriting the main Request Type (`type_de_demande__c` / Salesforce `Type_de_demande__c`), which **is not scored**. "Latest Import (1)/(2)" are filled at imports (manual, automatic, trade show, database…), and filling them is optional. "Latest ads activation" and "Latest ads interaction" are the equivalent for ads. A workflow driven by `last_activation_date` (latest import or latest HubSpot form) copies the Request Type into them. [TO CONFIRM: exactly what fills the two ads properties]

Points per value. The scoring tool shows HubSpot labels; the **internal value** below is also the Salesforce API value of `Type_de_demande__c` where one exists.

| Points | HubSpot label (internal value) |
|---|---|
| 100 | Request a demo / Demande de démo (`Request a demo`) · Personalized Meeting (`Rendez-vous personnalisé`) · Request for Proposal (`Appel d’offre`) · Request a quote (`Demande de devis`) · Free evaluation (`Evaluer gratuitement`) · Souscrire à Yooz · Declare a lead from a Partner (`Déclarer une Piste`) · Become Partner (`Devenir partenaire`) · Refer a friend · Echange Stand · Prescription Experts (`Prescription_Experts`) · Contact Request · Contact Request / Demande de contact · Contact · Être rappelé · Be recalled / Être rappelé · Démo live · Live Demo · Demo Live · ENGAGE (`engage`) |
| 75 | Advanced Webinar (`Webinar avancé`) · Replay webinar advanced (`Replay webinar avancé`) · Atelier Avancé |
| 50 | Find out more about Yooz / En savoir plus (`En savoir plus sur Yooz`) · Find out more about Yooz (legacy duplicate) · Educational Webinar (`Webinar educatif`) · Educational Webinar Registration · Replay webinar educational (`Replay webinar educatif`) · Replay webinar · Atelier Educatif |
| 25 | Whitepaper (`Livre Blanc`) · content syndication |
| 0 (not scored) | Other (BDD import) (`Autre`) · Third Party Database · Portal Sign-Up · Download ADV/EDU webinar Slides · S'abonner à la Newsletter · e-invoicing information request · Support · Recruitment · old values (Covid, Séminaire, Goto Webinar…) |

Reading: one Request Type of 100 = MQL on its own. 75 + 25, or 50 + 50 across two properties or events, also reaches MQL.

### 2. Form interactions (event group, cap ±150, decay −50% every 6 months)

**Page visits** (score every time):
- Base URL contains `careers.getyooz` → **−25** (job seekers).
- `/blog`, `/product`, `/solution-ap-automation`, `/solution-p2p-automation`, `/solution-fraud-prevention`, `/solution-payment-automation` `/yooz-vendor-statements`, `/solution-budget-management` → **+10** each.
- `/pricing`, `/the-lean-advantage` → **+25** each.

**Ads**: an ad interaction with a known Ad group → **+10** every time.

**Forms** ("Form submissions – Main", by form name, score every time):

| Points | Forms |
|---|---|
| 100 | US - Get a demo - 2 · NORAM_Request_Demo_2020 · NORAM_Adwords_demo_new_structure · NORAM_Form_Defined_Logic_Free_Demo · NORAM_WebSite_Request-Quote_Pricing · USA Form Request a Quote 2020 · NORAM_Form_Become_a_Partner · NORAM - Declare a lead (Impartner) · US_Popup refer a friend · USA Tradeshows · US - Lean Finance Tool · UK_WEBSITE_REQUEST_DEMO · all Chili Piper forms (`** NORAM_adwords **`, `** NORAM_G2 **`, `** NORAM_capterra **`, `** NORAM_SEA_PaidSearch_SoftwareAdviceLeadForm`, `** US_G2 **`, `** US_capterra **`, `** US_organic **`, `** US ** - chilipiper ERP`, `** US_bing **`, `** US_adwords **`, `** US_bingads **` ×2) · DO NOT USE: TrustRadiusLeadForm, NORAM_Form_Contact_Us_2020, NORAM_Form_banners_Free_Demo, EN_MARKETING_WEBSITE_REQUEST_DEMO · unnamed forms 164d7e36…, b6e90d26…, 50b46f44…, 0168fede…, 58c0c2f8… |
| 75 | NORAM_Live_Advanced_Webinar · NORAM_On-Demand_Advanced_Webinar (×2 incl. Q project) · USA_webinar_Customer_Insider · NORAM_BUSINESS_LP_RET_Webinar_CUS_Live_Insider · *UK_Live_Webinar_Advanced · *SP_Live_Webinar_Avancé · DO NOT USE USA_ABSENTS_Advanced Webinar · unnamed eb37a94f…, 19afe9d0… |
| 50 | NORAM_Live_Educational_Webinars (×2 incl. Q project) · NORAM_Educational_Webinars_On-Demand (×2 incl. Q project) · NORAM_Educative_Webinar_On-Demand · DO NOT USE USA_ABSENT Educative Webinar · DO NOT USE USA_Podcast · unnamed e80475d6…, e155930b… |
| 25 | NORAM_Download_Whitepaper · NORAM_Download_Whitepaper_Automate-AP-process · NORAM_E-Book_Whitepaper (Q project) · NORAM_SuccessFuel_ThrivingInTransition / ShowMeTheMoney / MitigatingAPFraudRisk · ** US_Newsletter ** · DO NOT USE: NORAM_Download_Magnet_Short_Whitepaper, NORAM_E-Book / Whitepaper |

The point levels follow the Request Type logic: demo, quote, partner or meeting = 100; advanced webinar = 75; educational webinar = 50; content = 25. "DO NOT USE" forms still score if they receive submissions.

### 3. Email Clic (event group, cap ±30, no decay)
Clicked a link in a marketing email: exactly 1 time **+5**, exactly 2 times **+10**, 3 times or more **+15**. Opens do not score. Email alone can never make an MQL.

### 4. Project Declaration Date (property group)
- `latest_project_identification_date` is known → **+75**. It is set by a HubSpot flow when a form's "I have a project" box is ticked.
- The same date is more than 30 days old → **−75**.
- Net effect: +75 for 30 days, then 0. A project declaration plus a 25-point action within 30 days = MQL.

## Pitfalls
See pitfalls.md, section "Scoring".
