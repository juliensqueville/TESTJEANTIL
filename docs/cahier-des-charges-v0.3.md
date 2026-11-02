# Cahier des charges — Outil de configuration JEANTIL

**Version :** 0.3 (intègre le cahier des charges initial de Martin + la structure du fichier Excel `20261102 - V1 -  - Descriptif.xlsx`)
**Statut :** à valider

**Sources :**
- Échanges avec le porteur du projet (niveaux d'accès, mode hors ligne / internet, versioning, tableaux de bord) ;
- Cahier des charges initial de Martin ;
- Fichier Excel de référence analysé : **`20261102 - V1 -  - Descriptif.xlsx`** (14 feuilles, tableaux structurés, formules, listes de validation).

---

## 1. Contexte et objectifs

JEANTIL conçoit des installations d'alimentation animale (robots mélangeurs/distributeurs, cellules, circuits de filoguidage). L'outil de configuration remplace/étend le fichier Excel de descriptif d'affaire pour :

1. collecter les données d'une affaire (produits, groupes d'animaux, bâtiments, recettes) ;
2. calculer automatiquement capacités machines, temps de fonctionnement, puissance, coûts et planning de distribution ;
3. produire un **plan** du bâtiment et des circuits, avec un **visuel 3D** ;
4. établir un **devis** chiffré ;
5. gérer les **affaires** avec historique, **versions** (V1-A, V1-B…) et reprise de dossiers réalisés ;
6. fournir des **tableaux de bord** par concessionnaire ou globaux (en cours, perdues, vendues).

L'outil est d'abord utilisable **hors ligne** par les utilisateurs JEANTIL, puis exposé **sur internet**.

---

## 2. Acteurs et droits

Authentification par **identifiant + mot de passe stocké en base** (haché). Chaque utilisateur a un rôle.

| Rôle | Droits |
|---|---|
| **Admin commerce** (JEANTIL niveau 1) | Utilisateur central : **valide tout**, administre utilisateurs/droits, accès à toutes les affaires et tableaux de bord. |
| **Utilisateur technique** (JEANTIL niveau 2) | Commentaires, **validation des plans**, **modification de la collecte de données** ; sur le devis : **commentaires uniquement**. |
| **Utilisateur commercial** | Accès au **devis**, à la **collecte de données**, aux **commentaires**, au **changement d'état d'affaire**. |
| **Concessionnaire** | Accès **uniquement si l'affaire est partagée** par un commercial ou admin. Peut **créer une affaire**, faire une **collecte de données**, **recevoir** devis/dossier/plans, **commenter** et **suggérer** un changement d'état (avec commentaire). |
| **Client potentiel** | Accès limité à définir (ex. consultation du devis/dossier partagé). |

---

## 3. Modes d'utilisation

- **Hors ligne (JEANTIL)** : application utilisable sans connexion, données locales, **synchronisation** à la reconnexion (règles de conflit à définir).
- **En ligne (internet)** : hébergement web sécurisé (HTTPS), accès multi-utilisateurs simultanés.

---

## 4. Structure du fichier Excel de référence → modèle de données

Le classeur `20261102 - V1 -  - Descriptif.xlsx` (14 feuilles) est la **structure de départ**. Chaque feuille devient une entité/écran de l'outil :

| Feuille Excel | Rôle | Devient dans l'outil |
|---|---|---|
| `1ère page` | Fiche affaire : **Client, Distributeur, Numéro d'affaire** + sommaire | Écran « Affaire » (en-tête) |
| `GENERAL` | Référentiels : **liste de produits** (densité démêlé/stockage, débit kg/min, puissance kWh, %MS, prix €/kg) ; **catalogue cellules/machines** (VRAC, VRAC-CUBE, TREMIES, BOTTE, LIQUIDE : volume, longueur, puissances, PAV, charge maxi) ; types d'approvisionnement | Tables de référence **produits** et **cellules/machines** (admin) |
| `Produits` | Désignation des produits utilisés : classe, nom spécifique, TYPE, poids/dimensions bottes-cubes, densités spécifiques, débits, + **contrôles de saisie** (listes déroulantes, messages d'erreur) | Écran **Produits de l'affaire** + règles de validation |
| `Groupes animaux` | Groupes d'animaux, effectifs, répartition par bâtiment, robot, longueur de distribution, distance cuisine-zone, paillage, totaux | Écran **Groupes d'animaux** |
| `Bâtiments et DIVERS` | Calcul de la **longueur de filoguidage** par bâtiment (B1–B9), distances cuisine (X1/Y1, X2/Y2), données robot (modèle R4M4, charge max, vitesses, consommations, débits) | Écran **Bâtiments & filoguidage** + table **robots** |
| `Recettes` | **Recettes en kg par animal** : matrice animaux × produits | Écran **Recettes** (rations) |
| `Capacités machines` | Capacités des cellules sélectionnées | Calcul auto **Capacités machines** |
| `Temps de fonctionnement` | Temps par groupe et par cellule | Calcul auto **Temps de fonctionnement** |
| `Puissance consommée` | Puissance par poste | Calcul auto **Puissance** |
| `Récap` | Synthèse par groupe : nb distributions/jour, raclage, nb bêtes, poids total, densité, temps de mélange, modèle robot | Écran **Récapitulatif** |
| `Coût matière` | Coût de la ration par groupe (matrice produits × animaux, formules matricielles) | Calcul auto **Coût matière** |
| `Comparatif cout énergétique` | Comparaison des coûts énergétiques | Calcul auto **Comparatif énergétique** |
| `PLANNING DE DISTRIBUTION` | Planning de distribution (296 lignes) | Écran **Planning de distribution** |
| `Notes` | **Historique daté des évolutions du fichier** (date + commentaire) | Précurseur du **versioning** de l'outil |

**Éléments techniques à réimplanter dans l'outil** (actuellement en formules Excel) :
- `VLOOKUP` / `SUMIFS` / `SUBTOTAL` / formules matricielles sur tableaux structurés (`TabProduits`, `TabGroupes`, `TabRobot`, `ListProduits`, `tableautemps`…) → calculs côté serveur/base ;
- **Listes déroulantes et validations de saisie** (noms définis : `type_vrac`, `type_tremie`, `type_botte`, `type_liquide`, `type_vrac_cube`, `typerobot`, `choix_oui/non`…) → listes contrôlées en base ;
- Règles métier intégrées aux formules (ex. : pas de données de bottes pour du VRAC, poids de botte obligatoire, densité du mélange calculée sur le produit le plus léger — cf. `Notes`).

**Affaire = classeur** : une affaire correspond à un fichier descriptif complet ; la reprise d'affaire = duplication d'un classeur existant.

---

## 5. Plan et configurateur 3D

- Capture de carte avec mesure des distances ; dessin des couloirs/bâtiments ; attribution des groupes aux espaces de distribution ; bâtiment cuisine ; circuits avec distances ; portes automatiques ;
- Nombre de **jours d'autonomie minimum** → **présélection des cellules** ;
- Plan de base avec dimensions (visuel grossier) + **configurateur 3D** du bâtiment et des circuits.

---

## 6. Devis (configurateur de prix)

- Devis selon **cellules sélectionnées**, **distance de filoguidage** (calculée en §4, feuille `Bâtiments et DIVERS`) ;
- Lecture du plan PDF si possible, sinon saisie rapide ;
- **Montant total + descriptif chiffré + case commentaire** ; **devis client** ; **dossier global de présentation** ; listing animaux ; listing produits/rations (système JEANTIL d'abord, puis vis).

---

## 7. Affaires et gestion des versions

- Affaire créée avec **commercial + distributeur (concessionnaire)** + client + numéro d'affaire ;
- Actualisation : correction d'une version ou **nouvelle version** (nouvelle collecte, nouveau plan, nouveau devis) ;
- **Plusieurs versions de devis par version d'affaire** : numérotation **V1-A, V1-B, V2A, V3A…** ;
- Restauration d'une **version antérieure** historisée ;
- **Tableau de toutes les affaires en cours avec suivi**.

---

## 8. Tableaux de bord

- Vue **par concessionnaire** et **globale** : affaires **en cours, perdues, vendues**, montants, taux de conversion ;
- Filtres : période, concessionnaire, statut, commercial ; export Excel/PDF.

---

## 9. Exigences non fonctionnelles

- **Sécurité** : mots de passe hachés, HTTPS, sessions, droits par rôle, partage explicite des affaires ;
- **Disponibilité** : hors ligne obligatoire pour JEANTIL, synchro fiable ;
- **Traçabilité** : qui/quoi/quand (versions, commentaires, changements d'état) — la feuille `Notes` du Excel en est le modèle ;
- **Sauvegarde** régulière de la base centralisée ;
- **Compatibilité** : import/export du format Excel existant.

## 10. Pistes techniques (à valider)

- Application web + API + base relationnelle (ex. PostgreSQL) ;
- Hors ligne : PWA ou app avec base locale synchronisée ;
- 3D : moteur 3D web (ex. Three.js) ;
- Moteur de calcul : les formules Excel du classeur sont réimplémentées dans l'API (pas de dépendance à Excel côté serveur).

## 11. Phasage proposé

1. **Phase 1** : modèle de données depuis l'Excel (§4), rôles/authentification, affaires + versioning V1-A…, hors ligne JEANTIL, import/export Excel.
2. **Phase 2** : ouverture internet, accès concessionnaires/clients, synchro, reprise d'affaires existantes.
3. **Phase 3** : calculs automatiques (capacités, temps, puissance, coûts, planning), plan + filoguidage, devis, tableaux de bord.
4. **Phase 4** : configurateur 3D, rations type avec quantités proposées.

## 12. Points ouverts

- [ ] Valider la correspondance rôles Martin ↔ niveaux JEANTIL (§2) et les droits du **client potentiel**.
- [ ] Règles de **conflit de synchronisation** hors ligne.
- [ ] Lecture automatique des **plans PDF** pour le devis.
- [ ] Règles de calcul des **rations type** (quantités proposées) à formaliser à partir des recettes Excel.
- [ ] Quelles formules Excel doivent rester paramétrables par l'admin vs. figées dans l'outil ?
