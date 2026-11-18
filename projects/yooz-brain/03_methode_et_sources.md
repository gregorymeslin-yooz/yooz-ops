# 03 — Méthode de travail et sources

## Sources, par ordre de confiance

1. **Déclaration validée par Greg, Chris ou Flo.** Elle fait foi, même contre la doc système.
2. **Glossaire des propriétés de Chris** (Lead Salesforce et HubSpot). C'est la base des fiches Lead & Contact.
3. **Schéma réel.** Il vient de l'API Salesforce (getObjectSchema) ou des propriétés HubSpot. On s'en sert pour les noms API, les types et les valeurs de picklist.
4. **Exports et analyses NORAM d'octobre 2026.** On s'en sert pour les taux de remplissage, les dates de fiabilité et les pièges illustrés par des cas réels (Gengras, NU-WOOL, Key Auto, Tyson Foods…).
5. **Inventaire du cerveau.** Il donne la liste de ce qu'il faut couvrir et les notes de départ. Ce n'est pas une source de définition.

En cas de contradiction entre deux sources, ne pas trancher seul : signaler et poser la question.

## Rôle de chaque outil

- **Inventaire (artifact)** : le backlog. Le statut de chaque élément suit Prévu → En rédaction → Rédigé → Validé (ou Écarté).
- **Ce projet Claude** : l'atelier, où l'on fait les discussions, les brouillons, les arbitrages et les tests.
- **Le skill** : le livrable. On le régénère à partir des fiches validées.

Pour éviter les doubles versions, une fiche validée vit dans le skill. L'inventaire ne garde que le statut et une note courte.

## Ordre de rédaction proposé

1. Trier les 17 recos (intégrer ou écarter), puis figer le périmètre V1.
2. `lead-journey.md` et les seuils de scoring. Tout le reste en dépend.
3. `attribution.md` : c'est là que se sont produites les erreurs les plus coûteuses.
4. `hubspot-salesforce.md` : correspondance, sync, Probance.
5. `objects/lead-contact.md`, à partir du glossaire de Chris.
6. Opportunity, puis Campaign et Campaign Member, puis Task & Event, puis Account.
7. `pitfalls.md` et `reliability.md`, alimentés tout au long du travail.
8. `SKILL.md` en dernier, une fois les règles d'or connues.

## Cycle pour chaque lot (un fichier de références ou un groupe de champs)

1. Greg fournit la source (extrait du glossaire, capture, export).
2. Claude fait un état des lieux : ce qui est couvert, ce qui manque, les contradictions.
3. Claude rédige au format final et liste les `[À CONFIRMER]`.
4. Greg, puis Chris pour les sujets CRM, corrigent. La fiche passe à « Rédigé ».
5. On fait un test sur les questions concernées. La fiche passe à « Validé ».

## Banque de questions de test

C'est un fichier à constituer dans le projet (`tests_questions.md`) : 20 à 30 vraies questions, idéalement collectées auprès de Michael, Cody, Jesse et Kirsten. Pour chacune, on note :
- la question ;
- les champs et objets à utiliser ;
- la bonne réponse ou les éléments obligatoires ;
- l'erreur typique à éviter.

Exemples de types de questions :
- « Combien de MQL NORAM en Q3 2026 par First Touch Lead Source ? »
- « Quelle campagne a vraiment généré l'opp Gengras ? »
- « Quel taux MQL → opp pour la syndication de contenu ? »
- « Ce compte est-il un nouveau client ? »

On teste dans une conversation neuve, skill activé et connecteurs Salesforce et HubSpot branchés, sans le contexte de ce projet.

## Règles de rédaction

- On écrit pour une IA qui connaît déjà Salesforce et HubSpot en général. On ne documente que l'écart propre à Yooz.
- Un exemple réel vaut mieux qu'une explication.
- On emploie les noms exacts (label + nom API), sans paraphrase.
- On date ce qui peut changer (« depuis le 22/09/2025 », « au 9/11/2026 »).
