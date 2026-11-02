# Cahier des charges — Outil de configuration JEANTIL

**Version :** 0.2 (intègre le cahier des charges initial de Martin)
**Statut :** à valider

**Sources :**
- Échanges avec le porteur du projet (niveaux d'accès, mode hors ligne / internet, versioning, tableaux de bord) ;
- Cahier des charges initial de Martin (collecte de données, plan, devis, affaires, rôles) — intégré ci-dessous ;
- Fichier Excel existant : structure de données de référence (**document attendu**).

---

## 1. Contexte et objectifs

JEANTIL conçoit des installations d'alimentation animale (systèmes de distribution de rations en bâtiment d'élevage). L'outil de configuration a pour but de :

1. partir de la **structure du fichier Excel existant** pour collecter les données d'une affaire ;
2. proposer des **rations type** (sélection des aliments → quantités proposées) ;
3. produire un **plan** du bâtiment et des circuits de distribution, avec un **visuel 3D** ;
4. établir un **devis** chiffré à partir des cellules sélectionnées et des distances ;
5. gérer les **affaires** avec historique, **versions** et reprise de dossiers réalisés ;
6. fournir des **tableaux de bord** par concessionnaire ou globaux (en cours, perdues, vendues).

L'outil est d'abord utilisable **hors ligne** par les utilisateurs JEANTIL, puis exposé **sur internet** pour les utilisateurs externes.

---

## 2. Acteurs et droits

Authentification par **identifiant + mot de passe stocké en base** (haché). Chaque utilisateur a un rôle.

| Rôle | Origine | Droits |
|---|---|---|
| **Admin commerce** (JEANTIL niveau 1) | Martin | Utilisateur central : **valide tout** (affaires, plans, devis), administre utilisateurs et droits, accès à toutes les affaires et à tous les tableaux de bord. |
| **Utilisateur technique** (JEANTIL niveau 2) | Martin | Commentaires, **validation des plans**, **modification de la collecte de données** ; sur le devis : **commentaires uniquement**. |
| **Utilisateur commercial** | Martin | Accès au **devis**, à la **collecte de données**, aux **commentaires**, au **changement d'état d'affaire**. |
| **Concessionnaire** | Martin + échanges | Accès **uniquement si l'affaire est partagée** par un utilisateur commercial ou admin. Peut **créer une affaire** et faire une **collecte de données**, **recevoir** devis / dossier / plans, **commenter** et **suggérer** un changement d'état d'affaire (avec commentaire). |
| **Client potentiel** | Échanges | Accès limité à définir (ex. consultation de son devis / dossier partagé). |

---

## 3. Modes d'utilisation

- **Hors ligne (utilisateurs JEANTIL)** : application utilisable sans connexion, données stockées localement, **synchronisation** à la reconnexion (règles de conflit à définir).
- **En ligne (internet)** : hébergement web sécurisé (HTTPS), accès multi-utilisateurs simultanés pour concessionnaires et clients.

---

## 4. Collecte de données (depuis le fichier Excel)

La structure de l'outil reprend celle du fichier Excel existant, enrichie de :

- **Groupes types d'animaux** à utiliser ;
- **Produits (aliments)** avec données enrichies, dont la **précision obtenue par produit** ; classement systématique : d'abord ce qui passe dans le **système JEANTIL**, ensuite ce qui passe par les **vis** ;
- **Tailles de bottes standard avec poids associés** ;
- **Paille broyée ou non** ;
- **Types de cellules possibles** ;
- **Historique des dossiers réalisés** et **installations en service** (réutilisables).

### Rations type
Sélection des aliments de la ration → l'outil **propose les quantités**.

---

## 5. Plan et configurateur 3D

- **Capture de carte avec mesure des distances** ;
- Dessin des **couloirs et bâtiments** ; attribution des **groupes aux espaces de distribution** ;
- Dessin du **bâtiment cuisine** ; dessin des **circuits avec distances** ; marquage des **portes automatiques** ;
- Saisie du **nombre de jours d'autonomie minimum** → le logiciel **présélectionne les cellules** ;
- **Plan de base avec dimensions**, sans détail des cellules (visuel grossier) ;
- **Configurateur 3D** : visualisation du bâtiment et des circuits en 3D à partir du plan.

---

## 6. Devis (configurateur de prix)

- Définition du devis en fonction des **cellules sélectionnées**, de la **distance de fil**, etc. ;
- **Lecture du plan PDF pour le devis** si possible, sinon saisie des points pour un **devis rapide** ;
- **Montant total** avec **descriptif des éléments chiffrés** + **case commentaire** ;
- Possibilité de produire un **devis client** ;
- **Dossier global de présentation** généré ;
- **Listing des animaux** associés au plan de situation ;
- **Listing des produits associés aux rations** (classés : système JEANTIL d'abord, puis vis).

---

## 7. Affaires et gestion des versions

- À la création, on ouvre une **affaire**, associée à un **commercial** et un **distributeur (concessionnaire)** ;
- À l'actualisation : soit on **corrige une version**, soit on en **crée une nouvelle** (nouvelle collecte de données, nouveau plan, nouveau devis possibles) ;
- **Plusieurs versions de devis possibles dans une version d'affaire** ;
- Numérotation : **V1-A, V1-B, V2A, V3A…** (version d'affaire + version de devis) ;
- Possibilité de **revenir à une version antérieure** (restauration historisée) ;
- **Tableau de toutes les affaires en cours avec suivi**.

---

## 8. Tableaux de bord

- Vue **par concessionnaire** et vue **globale** ;
- Indicateurs : affaires **en cours, perdues, vendues**, montants, taux de conversion ;
- Filtres : période, concessionnaire, statut, commercial ;
- Export possible (Excel / PDF).

---

## 9. Exigences non fonctionnelles

- **Sécurité** : mots de passe hachés, HTTPS, sessions, droits par rôle, partage explicite des affaires vers les concessionnaires ;
- **Disponibilité** : mode hors ligne obligatoire pour JEANTIL, synchro fiable ;
- **Traçabilité** : qui a fait quoi et quand (versions, commentaires, changements d'état) ;
- **Sauvegarde** : sauvegardes régulières de la base centralisée ;
- **Compatibilité** : export Excel compatible avec le fichier existant.

## 10. Pistes techniques (à valider)

- Application web + API + base de données relationnelle (ex. PostgreSQL) ;
- Mode hors ligne : PWA ou application avec base locale synchronisée ;
- Configurateur 3D : moteur 3D web (ex. Three.js) ;
- Import/export Excel (.xlsx) ; lecture de plans PDF à évaluer.

## 11. Phasage proposé

1. **Phase 1** : structure depuis l'Excel, rôles/authentification, collecte de données, affaires + versioning (V1-A…), hors ligne JEANTIL.
2. **Phase 2** : ouverture internet, accès concessionnaires / clients, synchro en ligne, reprise d'affaires existantes.
3. **Phase 3** : plan + distances + présélection des cellules, devis chiffré, dossiers de présentation, tableaux de bord.
4. **Phase 4** : configurateur 3D, rations type avec quantités proposées.

## 12. Points ouverts

- [ ] Recevoir le **fichier Excel** de référence (structure exacte des données).
- [ ] Valider la correspondance rôles Martin ↔ niveaux JEANTIL (§2).
- [ ] Préciser les droits du **client potentiel**.
- [ ] Définir les règles de **conflit de synchronisation** hors ligne.
- [ ] Évaluer la **lecture automatique des plans PDF** pour le devis.
- [ ] Définir les règles de calcul des **rations type** (quantités proposées).
