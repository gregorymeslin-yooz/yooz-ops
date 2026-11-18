# Yooz Brain — contexte GTM Yooz (Salesforce, HubSpot, lead journey)

*Snapshot du projet Claude « Yooz Brain » au 2026-11-18 · Porteur : Greg (Marketing Automation, Ops NORAM) · Statut : brouillon, en cours de validation par Chris*

Ce dossier rassemble ce qu'on a décidé et documenté sur la donnée GTM de Yooz : statuts du lead journey, attribution, scoring, correspondance HubSpot ↔ Salesforce, champs Salesforce, pièges de données. Il sert de **source de contexte pour tout projet Claude** qui touche à ces sujets, en attendant que le skill `yooz-brain` soit finalisé et déployé.

## Pour une IA qui lit ce dossier

1. **Commence par `skill/yooz-brain/SKILL.md`** : règles d'or + table d'aiguillage vers le bon fichier de référence.
2. Pour **tout chiffre, comptage ou taux**, lis aussi `skill/yooz-brain/references/pitfalls.md`.
3. Pour une décision ou son historique (« pourquoi on fait comme ça ? »), lis `04_journal_decisions.md`.
4. Pour un champ Lead précis absent des fiches, cherche dans `sources/glossaire_lead_2026-11-09.tsv`.

**Règles d'usage**
- Le contenu du skill est en anglais, les fichiers de cadrage en français.
- Les marqueurs `[TO CONFIRM]` / `[À CONFIRMER]` / `[À VALIDER]` signalent des points **non tranchés** : ne les présente pas comme des faits.
- Les chiffres datés (« on 2026-11-10 ») sont des relevés ponctuels, pas des valeurs à jour. Revérifie-les avec les connecteurs si la réponse en dépend.
- Connecteurs Salesforce et HubSpot : **lecture seule**. Ne jamais écrire ni modifier quoi que ce soit dans les CRM (règle de Greg).
- Ordre de confiance des sources : déclaration validée (Greg, Chris, Flo) > glossaire de Chris > schéma réel > exports/analyses > inventaire. En cas de contradiction, signaler plutôt que trancher.

## Contenu

| Chemin | Rôle |
|---|---|
| `skill/yooz-brain/SKILL.md` | Point d'entrée du futur skill : règles d'or, aiguillage, méthode de réponse |
| `skill/yooz-brain/references/lead-journey.md` | Statuts MCL → MEL → MQL → SAL → SQL → client, transitions, équivalents EMEA |
| `skill/yooz-brain/references/scoring.md` | NORAM Behaviour Score : seuil MQL, groupes, barème |
| `skill/yooz-brain/references/attribution.md` | First/Last Touch, valeurs de source, Primary Campaign Source, UTM |
| `skill/yooz-brain/references/hubspot-salesforce.md` | Système maître, inclusion list de sync, Probance, dates de demande |
| `skill/yooz-brain/references/pitfalls.md` | Pièges transverses (symptôme → correction) |
| `skill/yooz-brain/references/reliability.md` | Depuis quand chaque champ est exploitable |
| `skill/yooz-brain/references/objects/*.md` | Fiches champ par objet : Lead & Contact, Account, Opportunity, Campaign, Task & Event |
| `00_instructions_projet.md` | Instructions du projet Claude (rôle, règles de rédaction) |
| `01_contexte_projet.md` | Pourquoi ce projet, équipe, périmètre V1 |
| `02_cible_skill_v1.md` | Structure cible du skill, gabarits de fiche, critères de sortie |
| `03_methode_et_sources.md` | Méthode, ordre de confiance des sources, banque de tests |
| `04_journal_decisions.md` | **Toutes les décisions prises** + questions ouvertes |
| `claude/questions_brain.md` | Questions en attente (Greg, Chris) et flag F-CONTACT-3PD |
| `sources/glossaire_lead_2026-11-09.tsv` | Glossaire complet des champs Lead (Chris), 422 lignes, sens de sync HubSpot |
| `sources/schema_salesforce_2026-11-09.md` | Schéma réel extrait de Salesforce : valeurs API, types, comptages |
| `sources/name-generator-naming-rules_2026-11-10.md` | Règles complètes du Name Generator (noms de campagnes et d'assets) |
| `dashboard/pulse-dashboard.md` | Spec du dashboard Pulse (définitions de KPI, contrôles Health, constats data) |
| `dashboard/pulse-today.md` | Spec de la page Today de Pulse |
| `dashboard/gme-business-dashboard.md` | Clone partagé de Pulse (GME Business Dashboard) |

Les chemins reprennent ceux du projet Claude : un fichier modifié là-bas se remplace ici au même endroit.

## Ce qui restera utile après le skill

Une fois le skill validé et déployé, `skill/` pourra être supprimé d'ici (le skill fera foi). À garder a priori, car utiles au-delà du skill : `04_journal_decisions.md`, `sources/` (glossaire, schéma, Name Generator) et `dashboard/` (définitions de KPI et constats data).
