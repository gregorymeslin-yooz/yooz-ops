# Questions pour Greg — Cerveau Yooz

*Réponds par numéro en voice note (« Q3 : … »). Claude reformule, range dans le bon fichier et retire la question. Mis à jour le 13/11/2026.*

Q1 à Q51 : réponses intégrées (sauf Q10, reportée et traitée au cas par cas, et Q49, à faire plus tard).

## En attente de Greg

## Pour Chris
- **Advertising / To be defined** : Greg confirme le changement de valeur par défaut sur le First Touch (peut-être aussi le Last Touch), mais rien ne se voit dans les données à fin octobre 2026. La nouvelle valeur par défaut est-elle en production ?
- **Probability Score** : plus utilisé ?
- **31 % des opps NORAM sans Contact** (primary contact) : est-ce normal pour certains types d'opp ?
- **Contacts MEL+ en `3rd Party Database`** : voir le flag F-CONTACT-3PD ci-dessous (à regarder avec Greg).

## Flag F-CONTACT-3PD — Contacts engagés en `3rd Party Database` (13/11/2026)

**Constat.** Le piège A4 dit qu'un enregistrement MEL ou plus encore en First Touch `3rd Party Database` est une anomalie. Vérifié sur les **leads** : 0. Sur les **contacts** NORAM (`Sales_Territory__c = 'NORAM'`, `LeadSource = '3rd Party Database'`) :

| `LifecycleStatus__c` | Contacts |
|---|---|
| MEL | 55,544 |
| Recycle | 4,030 |
| SAL | 839 |
| SQL | 37 |
| MQL | 3 |
| **Total** | **60,453** |

Détail des 55,544 MEL :
- 55,236 ont `FirstEntryDateMEL__c` le **2025-09-20** (le tampon de migration).
- 53,852 ont été créés par **Integration LeanData**. Années de création : 37,940 en 2024, 14,012 en 2025, 3,166 en 2023, 273 en 2026.
- Seuls 4 n'ont pas de `TECH_ConvertedLeadId__c` : presque tous viennent d'un lead converti.
- Compte : 39,856 « Prospect Out », 15,222 « Prospect », le reste en « Prospect Try… ».
- Seuls **78** ont une vraie demande (`Last_Hubspot_Demand_Date_Yooz__c` rempli).

**Théories (non vérifiées).**
1. **Migration (la plus probable).** Le 2025-09-20, la migration a traduit l'ancien statut des contacts vers la nomenclature NORAM. Des contacts qui étaient l'équivalent d'un MCL (jamais engagés) ont été mis en MEL, peut-être via un mapping « Marketing Nurture → MEL » appliqué à tous les contacts, ou un statut par défaut. Indices : 99 % ont la date d'entrée MEL du jour de la migration, et presque aucun n'a de demande.
2. **Conversion automatique par LeanData.** Des leads achetés (MCL, `3rd Party Database`) ont été rattachés automatiquement par LeanData à un compte existant et convertis en contacts. La conversion ne repasse pas par la logique « MCL → MEL à la première vraie interaction », donc la source n'a jamais été écrasée. Le contact a pu recevoir un statut par défaut au lieu de `Suspect`. Cette théorie explique la source et le créateur, et se combine avec la 1 pour le statut.
3. **Règle de source différente côté Contact.** L'écrasement de la First Touch à la première vraie interaction ne fonctionnerait que sur le Lead, pas sur le Contact. Cela expliquerait les 839 SAL et 37 SQL encore en `3rd Party Database` : de vrais contacts engagés, mais dont la source n'a jamais été mise à jour.
4. **Statut correct, source jamais nettoyée.** Moins probable pour les MEL (78 demandes seulement), mais possible pour les SAL et SQL.

**Ce qu'il faut trancher.**
- Ces ~55k contacts MEL sont-ils de vrais MEL ? Si non, faut-il les corriger dans Salesforce ou seulement les exclure des comptages de stock ?
- Pour les SAL et SQL en `3rd Party Database` : quelle est la vraie source ? Faut-il une règle d'écrasement côté Contact ?

**Impact en attendant.**
- Funnel (nouvelles entrées depuis le 2025-10-01) : aucun.
- Stock de MEL côté contacts : gonflé d'environ 55k.
- Dashboard Pulse : le contrôle « Purchased source on engaged leads » reste limité aux leads.
- Brain : selon la réponse, nouveau piège F12 (« MEL contacts issus de la migration ») dans `pitfalls.md`, ou extension de A4 aux contacts.

Lot 8 (Q52 à Q55) : intégré le 10/11/2026.

Lot 9 (Q56 à Q63, scoring) : intégré le 10/11/2026.

Lot 10 (Q64 et Q65) : intégré le 10/11/2026.

## Lot 11 — `lead-contact.md` et campagnes

66. **Territoire d'un Contact** : le Contact a son propre `Sales_Territory__c`, qui diffère de celui de son compte pour environ 2 000 contacts NORAM. Pour filtrer les contacts NORAM, on prend le territoire du contact ou celui du compte ?
67. **Last Activity Date** : il existe le champ custom `Date_de_Derni_re_Activit__c` et le champ standard `LastActivityDate`, et l'équipe CRM dit que ce ne sont pas les mêmes. Lequel suit la dernière activité Sales ?
68. **Reseller vs Referral Partner** : sur le Lead, `Revendeur_ou_int_grateur__c` et `Apporteur_d_affaires__c`. Florian demande d'expliciter la différence avec les autres champs partenaires. Tu as la règle ?
69. **Name Generator** : depuis quand est-il utilisé pour nommer les campagnes Salesforce ? Ça permet de savoir à partir de quelle date un nom de campagne est fiable.
