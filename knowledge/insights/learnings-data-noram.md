# Enseignements data Yooz (NORAM) — analyses consolidées

Fusion de 3 analyses (sept.–oct. 2026). Seuls les constats, chiffres clés, définitions et limites ont été gardés. Les fichiers de travail (xlsx, pptx, scripts) n'ont pas été repris.

---

## 1. Conversion MQL → Won par verticale (analyse du 01–05/10/2026)

**Source et définitions**
- Source : report SF, MQL first entry ≥ 22/09/2025, territoire NORAM. 4 164 leads, 780 opps uniques.
- Regroupements : Automotive Dealer + Services = Automotive ; Construction + Construction Services = Construction.
- Verticales focus : Automotive, Heavy Truck, Construction, Manufacturing.
- Win rate = gagnées ÷ fermées (gagnées + perdues + abandonnées).

**Biais majeur**
- Le champ Vertical est vide sur ~100 % des leads convertis avant février 2026.
- Conséquence : sur la période complète, « Sans verticale » semble mieux convertir, mais c'est un artefact.
- Pour le haut de funnel, utiliser la cohorte MQL fév.–août 2026.
- 48 % des MQL et 30 % des comptes NORAM n'ont pas de verticale : c'est un chantier data quality.

**Haut de funnel (cohorte MQL fév.–août 2026)**

| Verticale | MQL | MQL→Opp | Win rate | ARR gagné / MQL |
|---|---|---|---|---|
| Heavy Truck | 57 | 24,6 % | 29 % (2/7) | 1 805 $ |
| Manufacturing | 254 | 19,3 % | 21 % (8/38) | 1 323 $ |
| Construction | 229 | 18,3 % | 12,5 % (4/32) | 334 $ |
| Automotive | 149 | 31,5 % | 3 % (1/34) | 121 $ |
| Sans verticale | 1 765 | 15,8 % | 11 % | 255 $ |

**Conclusions**
- Le ciblage vertical est justifié : Manufacturing + Heavy Truck représentent ~13 % des MQL et ~45 % de l'ARR gagné.
- Automotive : génère beaucoup mais ne close pas. Problème de closing ou de produit, pas de génération.
- Construction : commence à peine à closer. 30–34 % des pertes S2+ sont liées à des features ou intégrations, c'est une piste produit.
- Manufacturing : rupture nette à partir des MQL de février 2026 (win rate 1/47 → 10/54 ; Fisher p ≈ 0,01).
  - Le bond est au closing : MQL→S2 reste stable (~17 %).
  - Il est concentré sur les opps créées le jour du MQL (0/26 → 8/28).
  - Il touche tous les canaux. Les campagnes Manufacturing ne l'expliquent pas (0 win tracé), le message Lean non plus.
  - Mêmes AE avant et après.
  - **Cause non identifiée.** Test à faire : si l'effet est réel, les cohortes juin–août finiront vers 15–20 % de win rate ; si c'est du hasard, vers ~9 %.

**Paid Search**
- « Paid Search » mélange Google Ads, Microsoft Ads et les sites d'avis.
- Les formulaires NORAM_adwords servent aussi au trafic Microsoft : le nom du formulaire n'identifie pas Google. Pour isoler Google, utiliser le gclid ou utm=adwords.
- Google Ads après la refonte (bascule US_adwords → NORAM_adwords le 17/02/2026) : win rate 5 % → 16 % (2/38 → 9/55), sur de petits volumes.
- Sites d'avis : 0 win sur 40.
- Pic de MQL de janv.–mars dû aux téléchargements de contenus verticaux : 230 MQL, 1,7 % → opp, 0 deal. À comparer aux formulaires de démo Google Ads : 37 % → opp. C'est un sujet de règles MQL et de nurture.
- Attention aux périodes de longueur différente : comparer des taux ou des volumes par mois, jamais des volumes bruts.

---

## 2. Unengaged HubSpot : définition et activation hors email (28/09/2026)

**Définition observée**
- `engaged` = « Non Engaged » quand `hs_email_sends_since_last_engagement` ≥ 10.
- Le recalcul se fait en différé : environ 35k contacts à ≥ 10 envois sont encore « Engaged ».
- « Engaged » est la valeur par défaut, y compris pour les contacts qui n'ont jamais reçu d'email.
- La règle de la propriété custom `engaged` n'est pas documentée. Elle ne recoupe pas forcément la suppression automatique native de HubSpot.
- L'historique email est conservé par adresse : un contact purgé puis recréé garde son statut Non Engaged.

**Chiffres (fenêtre 6 mois)**
- Non Engaged : 488k, dont 64 % n'ont jamais ouvert ni cliqué.
- Activés hors email (visite web trackée, hors EMAIL_MARKETING et OFFLINE) : ~5 000, soit 1,1 %. Environ 64 % viennent du paid.
- Sur 12 mois : 2,4 %.
- Seuls 22 % des Non Engaged ont un jour été identifiés sur le site, donc ces chiffres sont des planchers.

**Limites**
- `hs_latest_source` ne garde que la dernière session.
- Probance n'est pas couvert.
- Il n'existe pas de date d'entrée en Unengaged (le dernier open sert de proxy).
- Des prospects qui viennent de demander une démo restent flaggés Non Engaged.

---

## 3. Engaged vs Unengaged — performance email NORAM (15/09–14/10/2026, MAJ 27/10)

**Méthode à garder**
- Comparer uniquement des emails jumeaux : même email, même jour, audience différente.
- Lire HubSpot (MEL/MQL/Recycle, bots exclus) et Probance (Suspects, bots non filtrables) séparément, jamais en mélangeant les deux.
- Listes HubSpot :
  - Engaged : 15021, avec suppression auto ~70k par envoi.
  - Unengaged : 63185 (« UNENGAGED ONLY »). Le nom de l'email contient « UNEN ».

**Résultats HubSpot (3 paires)**

| | Engaged | Unengaged |
|---|---|---|
| Open rate | 24,6 % | 0,9 % |
| Click rate | 1,20 % | 0,04 % |

L'écart se retrouve sur chaque paire.

**Bilan par envoi Unengaged**
- Gain : ~68k délivrés, ~26 cliqueurs.
- Coût : ~61 désinscriptions, ~166 hard bounces, 2 plaintes spam.
- Un envoi retire donc plus de contacts de la base qu'il n'en réactive.
- Les cliqueurs ne sont pas de nouveaux leads : ce sont des MEL/MQL/Recycle existants.

**Probance (paire AP Fraud)**
- Hard bounces : 13,6 % côté Unengaged, contre 0,21 % côté Engaged. C'est un problème de qualité de base, pas de performance email.
- Les clics Unengaged (7 707) sont presque égaux aux désinscriptions (7 402). Hypothèse : ce sont des clics sur le lien de désinscription, à vérifier dans le report par lien.

**Health score HubSpot**
- Passé de 49 à 31 (hard bounce portail 0,74 %).
- La baisse ne vient pas des envois Unengaged, dont le taux de bounce est de 0,24 %. Cause à investiguer : imports récents ? nouveaux domaines ?

**Position recommandée : réactivation contrôlée**
- Un envoi trimestriel maximum.
- Le meilleur asset disponible.
- Nettoyage des bounces avant chaque envoi.
- Règle de sortie après N tentatives ignorées.
- Mesure de ce que deviennent les cliqueurs.

**Export Probance (`phmReport_*.csv`) : points d'attention**
- Encodage UTF-16, séparateur tabulation.
- Dates au format JJ/MM/AAAA.
- Espace insécable fine comme séparateur des milliers, virgule décimale.
- Plusieurs lignes par campagne (une par jour d'envoi).
