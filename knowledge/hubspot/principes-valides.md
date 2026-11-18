# Principes techniques validés (HubSpot, Salesforce, reporting, Intercom)

Copie de référence des principes figurant dans les instructions du projet Claude « Yooz Ops ». À réutiliser comme base, pas à redémontrer.

## HubSpot
- Active Lists comme condition de branchement dans un workflow = bugs de timing (la liste se met à jour après l'évaluation du workflow). Préférer des filtres de propriété inline.
- Les champs UTM cachés ne se réinitialisent pas entre deux soumissions de formulaire : un visiteur récurrent peut porter des UTM obsolètes. Nécessite une propriété dédiée type « Last Submission UTM Campaign » alimentée en JavaScript à la soumission.
- Le « click-through rate » HubSpot est un CTOR (clics ÷ ouvertures). Il reste exact car les deux numérateurs ne sont pas arrondis.
- HubSpot arrondit les gros volumes dans ses rapports. Pour un chiffre précis (ex. delivered), calcul inverse depuis les comptes bruts (ouvertures brutes ÷ taux d'ouverture).
- Propriété `engaged` : voir `knowledge/insights/learnings-data-noram.md` §2 (définition observée de Non Engaged).

## Reporting
- Distinguer les périodes de pic d'enrôlement des régimes stables avant de conclure sur la performance d'un workflow.
- Grouper par date d'entrée dans la cohorte, pas par date de conversion.
- Un filtre sur l'état courant répond à une question de situation présente, pas à une question historique : utiliser des champs horodatés.

## Salesforce
- Les Campaign Members ne remontent pas à travers une hiérarchie de campagnes ; seuls les champs agrégés `Hierarchy*` remontent. Reporting membre = filtre explicite sur les enfants ou convention de nommage avec `contains`. Hiérarchie à deux niveaux, membres uniquement dans les enfants.
- Voir aussi `knowledge/salesforce/gotchas-arr-acquisition.md`.

## Intercom
- La priorité entre bots s'évalue par moment de déclenchement, pas sur la session. Un bot qui atteint son cap de fréquence rend le suivant éligible. Pas de cap global natif inter-workflows.
