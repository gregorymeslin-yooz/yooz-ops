# Instructions pour Claude

Ce repo est la mémoire de travail long terme de Greg pour Yooz. Le projet Claude « Yooz Ops » contient les instructions de posture ; ce repo contient le contenu.

## Avant de travailler sur un sujet

1. Lire `projects/README.md` pour trouver le projet concerné.
2. Lire le `README.md` du projet (statut, décisions, prochaines étapes).
3. Avant toute analyse Salesforce ou HubSpot : lire `knowledge/salesforce/` et `knowledge/hubspot/`.
4. Avant une analyse contenu → pipeline ou une décision sur petits volumes : lire `playbooks/`.

## En écrivant

- Mettre à jour le `README.md` du projet en fin de session (statut, décisions datées, prochaine étape).
- Un piège de donnée ou une définition réutilisable va dans `knowledge/`, pas seulement dans le projet.
- Nouveau projet : copier `projects/_template/` et ajouter une ligne dans `projects/README.md`.
- Ne jamais committer d'export brut avec des données personnelles (voir `.gitignore`).
- Messages de commit courts, en français, au présent : `theme-reporting: ajoute règles R45-R47`.
