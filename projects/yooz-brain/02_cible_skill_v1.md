# 02 — Cible : structure du skill V1

*Proposition de départ, à amender via le journal des décisions.*

## Principe

Un skill se lit en deux temps. Claude voit toujours le nom et la description. Il ouvre `SKILL.md` quand la question s'y rapporte, puis seulement les fichiers `references/` dont il a besoin.

Donc :
- `SKILL.md` porte les règles d'or et l'aiguillage ;
- le détail va dans les références, découpées pour qu'une question ne charge que 1 ou 2 fichiers.

## Arborescence

```
yooz-brain/
├── SKILL.md                     # quand l'utiliser, règles d'or, aiguillage (< 300 lignes)
└── references/
    ├── lead-journey.md          # statuts MCL→Client, transitions, dates d'entrée, scoring
    ├── attribution.md           # First/Last Touch, Lead Source (Detail), Primary Campaign Source, Responded, règles d'attribution
    ├── hubspot-salesforce.md    # correspondance des objets, sync, inclusion list, IDs, Probance
    ├── pitfalls.md              # pièges transverses, chacun avec un exemple réel
    ├── reliability.md           # depuis quand chaque champ est fiable, taux de remplissage
    └── objects/
        ├── lead-contact.md
        ├── account.md
        ├── opportunity.md       # + Opportunity Contact Role, Opportunity History
        ├── campaign.md          # + Campaign Member
        ├── task-event.md        # + BookIt
        └── hubspot-contact.md
```

## SKILL.md : contenu attendu

1. **Frontmatter.**
   - `name: yooz-brain`.
   - `description` qui dit quand se déclencher : toute question sur les leads, le funnel, les sources, les campagnes, les opps ou les champs Salesforce/HubSpot de Yooz.
2. **Règles d'or (10 maximum).** Les erreurs qu'on ne veut plus jamais voir. Exemples :
   - filtrer NORAM via Sales Territory ;
   - ne jamais utiliser la lifecyclestage HubSpot ;
   - ne pas dater un MQL avant octobre 2025 ;
   - joindre les IDs sur le même format (15 ou 18 caractères).
3. **Aiguillage** : quel fichier de références lire selon le type de question.
4. **Méthode de réponse** : annoncer les champs utilisés et leur fiabilité, afficher les effectifs, signaler les limites.

## Gabarit d'une fiche propriété

Dans `objects/*.md`, une fiche par champ, triée par groupe (mêmes groupes que l'inventaire).

```
### First Touch Lead Source
- API : `[nom API exact]` · Objets : Lead, Contact, Account, Opportunity · Système maître : Salesforce
- Définition Yooz : canal d'activation du lead (ex. Paid Search), pas sa première interaction avec un contenu.
- Valeurs clés : `3rd Party Database` = imports ZoomInfo/SMARTe pour l'outbound (pas des leads achetés) ; `Outsourced LeadsGen` = syndication achetée ; …
- Fiable depuis : … · Remplissage : …
- Utiliser pour : … · Ne pas utiliser pour : …
- Piège : un MEL ou plus ne peut pas être en `3rd Party Database`. Si c'est le cas, c'est une anomalie de donnée.
```

Les lignes « Valeurs », « Fiable depuis » et « Piège » sont omises si elles sont vides. Il ne faut jamais les remplir pour la forme.

## Gabarit d'une notion ou d'un piège

Pour une notion : titre, règle en une phrase, explication courte, exemple réel, champs concernés.

Pour un piège, on ajoute deux lignes : « Symptôme » (ce que l'IA conclurait à tort) et « Correction ».

## Critères de sortie V1

- Toutes les recos V1 de l'inventaire sont intégrées ou écartées avec une raison.
- Lead journey, attribution et correspondance HubSpot ↔ Salesforce sont validés par Chris.
- La banque de questions de test (voir 03) passe à 80 % ou plus. Aucune erreur ne doit toucher une règle d'or.
- `SKILL.md` fait moins de 300 lignes. Aucun fichier de références ne dépasse environ 400 lignes.
