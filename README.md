# Yooz Ops

Espace de travail de Greg (Marketing Automation, Yooz NORAM) : projets, référentiels réutilisables et méthodes. Complète le projet Claude « Yooz Ops », qui ne garde plus que les instructions et un index léger.

## Organisation

| Dossier | Contenu | Règle |
|---|---|---|
| `projects/` | Un dossier par projet (actif ou récent) | Chaque projet a un `README.md` : demande, statut, décisions, où sont les livrables |
| `knowledge/` | Ce qui reste vrai au-delà d'un projet : pièges de donnée, définitions, enseignements chiffrés | Classé par outil ou par nature (`salesforce/`, `hubspot/`, `insights/`, `context/`) |
| `playbooks/` | Méthodes réutilisables (comment mener une analyse, règles de décision) | Une méthode = un fichier, rédigé pour être réappliqué |
| `templates/` | Modèles de README projet, de spec et de journal de décisions | Copier, ne pas modifier sur place |
| `inbox/` | Dépôt rapide avant tri | Vidé régulièrement vers le bon dossier |
| `archive/` | Projets clos | On déplace le dossier entier, on ne supprime pas |

Index des projets : [`projects/README.md`](projects/README.md).

## Conventions

- Noms de fichiers et dossiers en `kebab-case`, sans accents ni espaces.
- Notes datées : `AAAA-MM-JJ_sujet.md` (tri chronologique naturel).
- Dans un projet : `notes/` (travail, comptes rendus), `ref/` (référentiels, règles), `scripts/` (code), `data/` (petits fichiers de référence uniquement), `deliverables/` (versions finales ou liens).
- Un fait réutilisable trouvé pendant un projet remonte dans `knowledge/` ; le projet garde un lien.
- Les livrables lourds (pptx, xlsx) restent sur SharePoint/OneDrive ; le README du projet donne le lien.

## Données : ce qui ne va pas ici

Pas d'export brut contenant des données personnelles (emails, noms de contacts, listes de leads). Le `.gitignore` bloque par défaut `*.csv`, `*.xlsx`, `*.pptx` hors `ref/` et `templates/`. Pour un référentiel CSV à versionner, le mettre dans un dossier `ref/`.
