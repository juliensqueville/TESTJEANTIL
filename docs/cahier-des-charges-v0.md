# Cahier des charges — Outil de configuration JEANTIL

**Version :** 0.1 (brouillon)
**Statut :** à compléter / valider

---

## 1. Contexte et objectifs

JEANTIL souhaite disposer d'un **outil de configuration** permettant de partir de sa structure de données existante (fichier Excel) pour :

1. ajouter un **configurateur 3D** ;
2. ajouter un **configurateur de prix** ;
3. gérer des **affaires** avec historique complet et reprise d'affaires existantes ;
4. produire des **tableaux de bord** (par concessionnaire ou global) : affaires en cours, perdues, vendues ;
5. gérer des **versions** afin de pouvoir revenir à une version antérieure.

L'outil sera d'abord utilisable **hors ligne** par les utilisateurs JEANTIL, puis exposé **sur internet** pour donner des accès à des utilisateurs externes.

> **Document attendu :** cahier des charges de l'utilisateur principal (à intégrer en annexe).
> **Document attendu :** fichier Excel de collecte/génération de données (structure de référence).

---

## 2. Acteurs et niveaux d'accès

L'accès est contrôlé par **mot de passe stocké en base de données** (haché, jamais en clair), associé à un rôle.

| Fonctionnalité | Client potentiel | Concessionnaire | JEANTIL niv. 2 | JEANTIL niv. 1 |
|---|---|---|---|---|
| Configurateur 3D | ✅ (son périmètre) | ✅ | ✅ | ✅ |
| Configurateur de prix | ✅ prix indicatifs | ✅ devis | ✅ (prix spéciaux) | ✅ (tarifs, remises) |
| Créer une demande / affaire | ✅ | ✅ | ✅ | ✅ |
| Voir ses propres affaires | ✅ | ✅ | ✅ | ✅ |
| Voir les affaires de son concessionnaire | ❌ | ✅ | ✅ | ✅ |
| Valider / qualifier une affaire (gagnée, perdue) | ❌ | ✅ (proposition) | ✅ | ✅ |
| Gestion des versions (lister, comparer, restaurer) | ❌ | ✅ (ses affaires) | ✅ | ✅ |
| Tableaux de bord (son périmètre) | ❌ | ✅ | ✅ | ✅ |
| Tableaux de bord globaux | ❌ | ❌ | ✅ | ✅ |
| Administration utilisateurs & droits | ❌ | ❌ | ❌ | ✅ |
| Paramétrage global de l'outil | ❌ | ❌ | ❌ | ✅ |

*Cette matrice est une proposition à valider avec JEANTIL.*

---

## 3. Modes d'utilisation

### 3.1 Mode hors ligne (utilisateurs JEANTIL)
- L'application fonctionne **sans connexion internet**.
- Les données sont stockées localement et **synchronisées** dès que la connexion est rétablie.
- Gestion des conflits de synchronisation à définir (priorité à la version la plus récente, ou validation manuelle).

### 3.2 Mode en ligne (internet)
- À terme, l'outil est hébergé et accessible via navigateur (URL sécurisée HTTPS).
- Authentification par identifiant + mot de passe pour tous les utilisateurs externes.
- Accès simultanés, sauvegarde centralisée.

---

## 4. Structure de données

- La structure de l'outil **reprend celle du fichier Excel existant** (à fournir).
- Mapping Excel → base de données à établir (feuilles, colonnes, types, règles de saisie).
- Import initial des données existantes + export possible vers Excel.

---

## 5. Configurateur 3D

- Visualisation 3D du produit configuré, mise à jour en temps réel selon les choix de l'utilisateur.
- **À préciser** (à intégrer depuis le cahier des charges de l'utilisateur principal) : bibliothèque de modèles 3D, options configurables, export de visuels.

## 6. Configurateur de prix

- Calcul du prix à partir de la configuration choisie et des règles tarifaires.
- Gestion des remises et niveaux de prix selon le rôle (cf. matrice §2).
- Génération de devis / fiches de prix.

---

## 7. Gestion des versions

- Chaque modification d'une configuration ou d'une affaire crée une **version** horodatée et rattachée à son auteur.
- Consultation de l'historique des versions, comparaison entre deux versions.
- **Restauration possible d'une version antérieure** (qui crée elle-même une nouvelle version, sans écraser l'historique).

## 8. Gestion des affaires et historique

- Toutes les affaires sont centralisées dans l'outil.
- Une affaire peut être **reprise à partir d'une affaire déjà réalisée** : reprise de la configuration, du prix et de l'historique complet.
- Historique complet par affaire : créations, modifications, versions, changements de statut, auteurs et dates.

## 9. Tableaux de bord

- Vue **par concessionnaire** et vue **globale**.
- Indicateurs : affaires **en cours**, **perdues**, **vendues** (gagnées), avec montants, taux de conversion.
- Filtres : période, concessionnaire, statut, commercial.
- Export possible (Excel / PDF).

---

## 10. Exigences non fonctionnelles

- **Sécurité :** mots de passe hachés, HTTPS, gestion des sessions, droits par rôle, protection contre les accès non autorisés.
- **Disponibilité :** mode hors ligne obligatoire pour JEANTIL ; synchro fiable.
- **Traçabilité :** qui a fait quoi et quand (logs sur les actions sensibles).
- **Sauvegarde :** sauvegardes régulières de la base de données centralisée.
- **Compatibilité :** navigateurs récents ; export Excel compatible avec le fichier existant.

## 11. Pistes techniques (à valider)

- Application web (front moderne) + API + base de données relationnelle (ex. PostgreSQL).
- Mode hors ligne : application progressive (PWA) ou application embarquant une base locale synchronisée.
- Configurateur 3D : moteur 3D web (ex. Three.js).
- Import/export Excel via bibliothèque dédiée (ex. format .xlsx).

## 12. Phasage proposé

1. **Phase 1 :** reprise de la structure Excel, gestion des utilisateurs/rôles, affaires + historique, versioning de base (hors ligne JEANTIL).
2. **Phase 2 :** ouverture sur internet, accès concessionnaires et clients potentiels, synchro en ligne.
3. **Phase 3 :** configurateur de prix avancé, tableaux de bord, exports.
4. **Phase 4 :** configurateur 3D.

## 13. Points ouverts

- [ ] Recevoir le fichier Excel de référence.
- [ ] Recevoir et intégrer le cahier des charges de l'utilisateur principal.
- [ ] Valider la matrice des droits (§2).
- [ ] Définir les règles de conflit de synchronisation hors ligne.
- [ ] Définir le périmètre exact du configurateur 3D.
