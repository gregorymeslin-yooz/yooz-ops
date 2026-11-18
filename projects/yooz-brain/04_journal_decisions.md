# 04 — Journal des décisions

*Une ligne par décision : date, décision, raison, qui. Les questions ouvertes sont en bas. On met ce fichier à jour à chaque arbitrage.*

## Décisions prises

| Date | Décision | Raison | Qui |
|---|---|---|---|
| oct. 2026 | Le cerveau est le niveau 1 de Marketing Intelligence ; on commence par NORAM. | Les erreurs d'analyse venaient du manque de contexte Yooz. | Greg, Chris, Flo |
| oct. 2026 | Le lead journey reste le cœur ; les coûts et le pipe avancé passent au niveau 2. | Il faut fiabiliser la base avant d'élargir. | Greg, Flo |
| oct. 2026 | On ne fait pas de nettoyage du CRM dans ce projet ; on documente les champs tels quels. | L'équipe CRM est saturée. | Greg, Flo |
| 9/11/2026 | L'output V1 est un skill Claude (`yooz-brain/`). | Il est réutilisable, partageable et se charge à la demande. | Greg |
| 9/11/2026 | L'inventaire sert de backlog ; une fiche validée vit dans le skill. | Ça évite les doubles versions. | Greg |
| — | Seule notion de statut : la propriété « Statut » (Lead Status), jamais la lifecyclestage HubSpot. | Les lifecycle stages ne sont pas utilisées chez Yooz. | Greg (validé) |
| 9/11/2026 | Le contenu du skill est rédigé en anglais ; les discussions restent en français. | Noms de champs, utilisateurs NORAM et futur WW en anglais. | Greg |
| 9/11/2026 | Les brouillons du skill vivent dans ce projet (`skill/yooz-brain/…`) ; on pousse dans un vrai skill une fois validé. | Itérer sans redéployer. | Greg |
| 9/11/2026 | Après conversion, on suit toujours le Contact (`LifecycleStatus__c`), jamais le Lead. | Le Lead reste figé à `Converti` ; la conversion peut arriver à n'importe quel stade. | Greg |
| 9/11/2026 | Seul le Behavioral Score déclenche le MQL (≥ 100, y compris depuis Recycle) ; Persona, Company Score et Ready for MQL n'interviennent pas. | Règle du flow Salesforce. | Greg |
| 9/11/2026 | Purge/Out sont comptés en volume mais exclus de tous les taux, opps et revenus. | Ce sont des fiches inutilisables. | Greg |
| 9/11/2026 | Les contacts Customer (MEC, MQC, SAC, SQC, Customer Nurture) sont gardés, mais isolés du funnel d'acquisition. | Ce sont des clients (upsell / CSM). | Greg |
| 10/11/2026 | Attribution par défaut d'une opp = First Touch Lead Source de l'opp (valeur à la création). | Le champ n'est plus modifiable à la main ; Primary Campaign Source n'est pas fiable. | Greg |
| 10/11/2026 | Opp : `StageName` = état, `Sales stage (US)` = étape, `1-Year Recurring Amount` = montant. Corrige la note de l'inventaire (« ne pas utiliser Sales stage (US) »). | Déclaration Greg. | Greg |
| 10/11/2026 | Dates MEL/MQL exploitables à partir du 01/10/2025 (officiel 20/09/2025). | Le 20/09 est un tampon de migration (6 263 MQL NORAM ce jour-là) et des rattrapages ont suivi. | Greg (+ vérif. Claude) |
| 10/11/2026 | Partenaires conservés dans les analyses, avec mention ou comparaison avec/sans ; comptes Test exclus. | Ça dépend du cadrage de la question. | Greg |
| 10/11/2026 | Engagement campagne = Campaign Member Status (pas Responded) ; campagnes `Operational` exclues. | Responded est automatique et non utilisé. | Greg |
| 10/11/2026 | Glossaire de Chris : seules les lignes Lead sont prises en compte ; les autres objets sont des exemples. | Seul l'objet Lead est rempli pour l'instant. | Greg |
| 10/11/2026 | Contact SAL = opp en Sales stage 1 ; SQL = stages 2 à 4 ; gagné = Customer Nurture. Seul le primary contact de l'opp suit ces statuts. | Déclaration Greg. | Greg |
| 10/11/2026 | Avant le 20/09/2025 : SDR Nurture ≈ MQL, Marketing Nurture ≈ MEL. On compare les volumes de statuts, pas les taux de funnel. | Pas de scoring, règles de transition différentes. | Greg |
| 10/11/2026 | Montant d'opp = 1-Year Recurring Amount converti (`convertCurrency`) ; toujours indiquer la devise. | Les opps NORAM mélangent USD, CAD et EUR ; « converted » = devise de l'utilisateur. | Greg (+ vérif. Claude) |
| 10/11/2026 | Le cas Gengras reste un exemple, pas une règle. | Cas atypique. | Greg |
| 10/11/2026 | Primary contact d'une opp = champ `Contact` (`Interlocuteur__c`), pas le Contact Role Primary. | 13 % d'écart entre les deux quand ils sont remplis (vérif. sur 1 442 opps NORAM) ; le champ fait foi. | Greg (+ vérif. Claude) |
| 10/11/2026 | Devise de restitution : USD pour NORAM, GBP pour UK, EUR pour EMEA, toujours sur montant converti. | Système de base existant. | Greg |
| 10/11/2026 | Ajout de `references/scoring.md` (règles et barème du NORAM Behaviour Score), hors structure initiale de 02. | Barème long et dupliqué ; il ne doit se charger que pour les questions de score. | Greg |
| 10/11/2026 | Les MQL entrés avant le 01/10/2025 (anciens SDR Nurture migrés) sont isolés comme « MQL hérités ». Période d'analyse par défaut : 2026 et après. | À la migration, les SDR Nurture sont passés MQL sans le scoring actuel ; les prochaines analyses portent sur 2026+. | Greg |
| 10/11/2026 | Les pièges transverses sont regroupés dans `pitfalls.md` (lu pour toute question chiffrée) ; les pièges propres à un champ restent dans sa fiche. | Un seul endroit à tenir à jour ; on évite les doublons entre fichiers. | Greg |
| 10/11/2026 | `lead-contact.md` ne couvre que les champs de l'inventaire ; le glossaire complet reste une source du projet, hors skill. | Concision : seuls les champs utilisés au quotidien changent une réponse. | Greg |
| 9/11/2026 | Méthode de collecte : Greg répond en voice note aux questions numérotées de `questions_brain.md` ; Claude reformule et range. | Plus rapide que la rédaction directe. | Greg |

## Questions ouvertes

| Question | Options | Reco Claude | À trancher par |
|---|---|---|---|
| Déploiement | Skill perso, puis skill d'organisation / plugin | Skill perso pour les tests. Ensuite un plugin « Yooz GTM » qui regroupe yooz-brain et les skills marketing existants (reports, naming QA, email writing…), si on veut les distribuer ensemble. | Greg, admin Claude |
| Copie maître éditable | SharePoint .md / ce projet | SharePoint si Chris ou Flo doivent éditer sans Claude. | Greg |
| Validation finale | Chris seul / Chris + Flo | Chris pour le CRM, Flo pour le périmètre et les règles d'or. | Chris, Flo |
| Inclure les termes EMEA dès la V1 | Non / table de correspondance seule | Table de correspondance seule (Marketing Nurture ≈ MEL, etc.). | Greg |
