# Méthode : mesurer l'impact des contenus marketing sur le pipeline

Méthode tirée du Theme Performance Reporting NORAM (octobre 2026, `projects/theme-performance-reporting/`), réutilisable pour toute analyse du type « quel contenu, thème, format ou canal génère des opportunités ? ». Les scripts de référence (01 à 14) ne sont plus stockés (nettoyage du 18/11/2026) ; à déposer dans `projects/theme-performance-reporting/scripts/` s'ils sont retrouvés.

---

## 1. Cadrer avant d'extraire

- **Reformuler la question en métrique.** « Est-ce que le thème X marche mieux que Y ? » devient « part des leads exposés à X devenus opportunité, comparée à la moyenne ». La conversion finale (opp, pipeline, won), c'est mieux que les clics.
- **Définir la cohorte par date d'entrée**, pas par date de conversion. Exemple : leads entrés en MEL ou MQL depuis le 01/10/2025, en prenant min(MEL, MQL) parce que beaucoup de leads passent directement en MQL. Exclure les périodes artificielles, comme une migration de masse.
- **Figer un snapshot**, c'est-à-dire une date de photo, et tout dater par rapport à elle.
- **Écrire les définitions avant de calculer, et les faire valider** :
  - territoire ;
  - type d'opp retenu (new business uniquement, hors upsell et hors renouvellement) ;
  - définition de chaque étape (voir section 5) ;
  - seuil de volume minimal.
- **Ne jamais réutiliser un mot qui a déjà un sens dans l'entreprise.** « First touch » = First Touch Lead Source chez Yooz, donc on dit « premier contenu consommé ».
- **Prévoir un plan par étapes avec un propriétaire par étape**, et un point de validation à la fin de chaque étape.

## 2. Extraire la donnée (économie et traçabilité)

- **Exports CSV en UTF-8**, une ligne par événement. On extrait seulement les colonnes utiles.
- **Garder la trace des fichiers bruts.** On calcule leur empreinte (sha256) et on ne les modifie jamais : chaque transformation se fait par script.
- **Sources typiques**

| Source | Contenu | Rôle |
|---|---|---|
| E0 | Propriétés contact (statut, dates de lifecycle, ERP, NAICS, First Page Seen, First Conversion, Original Source) | Données du lead |
| E1 | Clics email HubSpot : nom de l'email et lien cliqué | Interactions |
| E2 | Soumissions de formulaires : formulaire, page, titre | Interactions |
| E6 | Membres de campagnes Salesforce, joints par email | Interactions |
| E9 | Pages vues HubSpot | Interactions |
| E5 | Opportunités et contact roles Salesforce | Opportunités |

- **Pour identifier un contenu, commencer par son nom** (nom de l'email, de la campagne, URL, nom du formulaire). On n'ouvre le contenu que si le nom ne dit rien.
- **Profiler chaque fichier dès réception** : volumes, valeurs vides, placeholders du type « (No value) », doublons exacts, formats de date.

## 3. Identifier le contenu (classification bottom-up)

1. **Inventorier** tous les libellés distincts (emails, campagnes, formulaires, URL) avec leur volume.
2. **Écrire des règles regex ordonnées** dans un fichier de référence (`projects/theme-performance-reporting/ref/regles_sujets.csv` : ordre, id, motif, sujet, bucket). La première règle qui matche gagne. Les règles spécifiques passent avant les génériques.
3. **Fixer une priorité des champs par source** :
   - clic email : le lien cliqué, puis le nom de l'email ;
   - formulaire : la page, puis le titre, puis le nom du formulaire.

   Un champ neutre (BOFU, générique) ne gagne que si aucun autre champ ne porte de thème. Exception : pour une page vue ou un formulaire, une URL générique est décisive. Le titre de la page (souvent le slogan du site) ne doit pas la requalifier.
4. **Normaliser les URL** : décoder (%20…), retirer le protocole, le domaine et les paramètres, mettre en minuscules.
5. **Séparer les buckets thématiques des buckets non thématiques** : Neutre/BOFU (demande de démo, pricing), Générique/marque, Événements & salons (un canal, pas un message), Partenaires, Clients, Hors périmètre.
6. **Ajouter des règles « post »** pour les cas que la regex ne peut pas trancher :
   - exclusion hors territoire ;
   - webinars datés via une table de correspondance ;
   - un lead de syndication hérite de l'asset du formulaire du même contact à ±30 jours ;
   - un sujet ERP est découpé par écosystème.
7. **Livrer un référentiel** (sujets, buckets, règles, éléments à vérifier) que le métier valide (ici le Head of Content), puis réintégrer ses arbitrages dans les règles.

## 4. Construire le parcours et relier les opportunités

- **Une table de touches unique** : contact, horodatage, source, libellé, détail, sujet, bucket.
- **Relier contact et opp par les contact roles**, sur l'ID Salesforce à 15 caractères ou sur l'email. On ne joint jamais sur une valeur vide.
- **Une opp n'est attribuable que si min(created, close) ≥ date d'entrée** du contact dans la cohorte.
- **Exclure les opps hors sujet**, en liste explicite et tracée : process internes (PI), upsell, opps de partenariat typées par erreur comme du new business.

## 5. Définir les métriques

- **Base d'analyse** : les leads qui ont consommé au moins un contenu thématique. On retire ceux qui étaient déjà en opp avant leur premier contenu, puisque le contenu n'a pas pu générer l'opp.
- **Taux d'opp** : part des leads d'un thème qui deviennent opportunité. On l'affiche toujours à côté de la moyenne.

| Étape | Définition | Moyenne de référence |
|---|---|---|
| Attract | Premier contenu thématique consommé | Taux d'opp de toute la base |
| Move | Contenus consommés ensuite, plus d'un jour après le premier et avant l'opp | Taux d'opp des leads qui ont au moins un contenu Move |
| Convert | Dernier contenu thématique **avant** la création de l'opp (dernier en date s'il n'y a pas d'opp) | Taux d'opp des leads qui ont un contenu Convert |

- **Une moyenne par étape.** Comparer le Move à la moyenne de l'Attract le gonfle, parce que les leads engagés convertissent plus.
- **Lecture simple** : « Above » à partir de 1,5x la moyenne de l'étape, « Average » entre 0,75x et 1,5x, « Below » sous 0,75x.
- **Seuil** : 30 leads minimum pour afficher un résultat. Pour comparer des segments, il faut au moins 10 opps dans le segment, et au moins 3 opps derrière un « best ».
- **Pipeline influencé** : 100 % du montant à chaque thème touché, ce qui veut dire que les totaux ne s'additionnent pas. On ajoute un contrôle en crédit linéaire.
- **Won** : indicatif seulement, sauf si le volume le permet.

## 6. Vérifier la donnée (checklist QA)

**Contrôles techniques**

- [ ] Réconciliation à chaque étape : nombre de lignes en entrée, nombre de lignes exclues avec leur motif, nombre en sortie.
- [ ] Parsing des dates en `format='mixed'`. Sans ça, pandas a mis silencieusement 5 895 dates à NaT et réduit la base de 40 %.
- [ ] Doublons exacts retirés, avec une clé assez fine (le lien cliqué en fait partie).
- [ ] Départage déterministe des touches simultanées, par un tri stable sur date, source, règle et libellé. On mesure combien de contacts sont concernés.
- [ ] Contacts test exclus, et aucune touche orpheline (touche sans contact dans la cohorte).
- [ ] `groupby().first()` à éviter : il prend la première valeur non vide colonne par colonne et peut mélanger des lignes. Utiliser `drop_duplicates`.

**Recalcul indépendant**

- [ ] Un agent séparé recode tous les chiffres à partir de la spec, sans lire les scripts.
- [ ] Il contrôle aussi chaque affirmation du livrable, et pas seulement les chiffres.

**Vérification manuelle**

- [ ] Échantillon de 20 contacts tiré avec une graine fixe : 6 avec opp, 2 exclus, 12 sans opp.
- [ ] Une chronologie par contact, comparée par le métier à HubSpot et Salesforce.
- [ ] L'échantillon reste figé d'une version à l'autre.

**Relecture critique**

- [ ] Relire chaque résultat surprenant comme un bug potentiel avant d'y croire. Ici, toutes les surprises étaient des biais.

## 7. Catalogue des biais rencontrés (à tester systématiquement)

| Biais | Symptôme | Correction |
|---|---|---|
| Landing pages Ads avec prise de RDV classées en thème (URL qui contient un mot-clé) | Un thème « produit » à 17 % de conversion | Toute page `/en-us/ad-*` = demande de démo (BOFU). Vérifier First Conversion et Original Source |
| Session de demande de démo : HubSpot rattache les pages vues de la visite où le lead s'identifie | Les pages ERP ou produit vues le jour de la démo passent pour la « première touche » | Exclure de Attract et Move les touches à ±1 jour d'une demande de démo |
| Inscription à une campagne Salesforce datée à minuit, alors qu'elle est la trace d'un formulaire HubSpot | La source affichée est fausse et le Move est gonflé | Retirer l'inscription Salesforce si une touche HubSpot du même sujet existe à ±1 jour |
| Titre de page = slogan du site | La home classée « Payments » | Une URL générique est décisive, le titre ne la requalifie pas |
| URL encodées | PDF non reconnus | Décoder les URL avant de classer |
| Leads achetés (syndication) | Un thème plombé par son canal (0,3 % contre 1,9 %) | Contrôle par canal, et tag « bought ». Reconnaître les vendors aux noms des campagnes et formulaires |
| Opp antérieure au premier contenu | Un thème crédité d'une opp qu'il n'a pas pu générer | Exclure ces contacts de la base |
| Last touch postérieur à l'opp | Convert gonflé (23 % des contacts avec opp) | Convert = dernier contenu avant l'opp |
| Move pendant la même visite que l'Attract | Move gonflé | Ne compter le Move qu'à partir de plus d'un jour après le premier contenu |
| Champ renseigné seulement en phase de qualification (ERP au stade MQL) | Les leads sans ERP convertissent à 0,1 % (biais de sélection) | Analyser l'ERP sur les MQL dont l'ERP est déclaré |
| Contenu mal rangé (formation partenaires classée en Fraud) | Une opp partenaire comptée dans un thème | Prévoir des règles Partenaires et Clients avant les thèmes |
| Opps de partenariat typées new business | Pipeline client faussé | Liste d'exclusion explicite, validée par le métier |
| Petits nombres et concentration (un seul deal = 90 % du pipeline d'un thème) | Des indices spectaculaires sur 1 à 3 opps | Seuils minimum, nombre d'opps affiché, mention « directionnel » |
| Canaux invisibles (clics Probance) | Sous-estimation | Documenter la limite. Ne visible que via l'UTM des pages vues |
| Industrie inconnue pour 67 % des opps | Coupe par verticale illisible | Le dire, et ne pas conclure |

**Règle d'or** : avant de conclure qu'un thème convertit, regarder d'où viennent concrètement ses premiers contenus (source, libellé, First Conversion, Original Source), et les jours qui précèdent ses opps.

## 8. Restituer (audience dirigeante)

- **Le titre de chaque slide est la conclusion.** Un visuel et trois chiffres maximum par slide.
- **Afficher des pourcentages lisibles**, du type « X % des leads sont devenus une opportunité », avec la ligne de moyenne. Pas d'indices du type « 2,3x ».
- **Mettre des étiquettes en clair (Above / Average / Below / Best)** plutôt que des pictos à décoder, et définir chaque colonne sur la slide.
- **Ajouter un exemple de lecture** en sous-titre sur les graphiques complexes, par exemple un nuage de points en échelle logarithmique.
- **Mettre la méthode en slide 2**, juste après la couverture, et réserver les détails (tableau complet, sources par asset) à l'annexe.
- **Écrire des recommandations actionnables** (« Do X »), pas des constats. Les faire valider par le propriétaire du sujet avant de présenter.
- **Préparer les questions probables** : base de la moyenne, sens de « Direct », détail « bought » / « in-house » de chaque asset.

## 9. Livrables types

1. Un référentiel des sujets (xlsx), validé par le métier.
2. Les résultats (xlsx) : Lisez-moi, puis un onglet par étape, contrôle canal, sujets, coupes, réconciliation.
3. Un échantillon de vérification manuelle (xlsx).
4. Un journal des contrôles (md), qui date chaque biais trouvé avec son impact chiffré.
5. Un deck généré par script depuis les données, ce qui permet de le régénérer en quelques minutes après chaque correction.
