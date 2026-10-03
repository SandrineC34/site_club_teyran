# Mode d'emploi — Créer le composant `club` avec Joomla Component Builder (JCB)

**Site Joomla du club cynophile — Phase 1 (socle)**

Ce document explique pas à pas comment fabriquer, avec **JCB**, le composant `club` contenant les 7 tables de la phase 1 : `activite`, `niveau`, `adherent`, `chien`, `groupe`, `chien_adherent`, `activite_chien`.

> **À lire d'abord**
> - Les étapes générales (champ → vue d'administration → composant → compilation → installation) sont issues du tutoriel officiel « Hello World » de JCB.
> - Les adaptations propres à votre projet (clés étrangères, tables de liaison, noms) sont signalées par **⚠ à vérifier** : je n'ai pas pu les tester dans l'interface de JCB. Faites-les d'abord sur le site de test.
> - Intitulés et boutons peuvent varier légèrement selon la version de JCB.

## Sommaire

1. [Principe de JCB](#1-principe-de-jcb)
2. [Préparer l'environnement](#2-préparer-lenvironnement)
3. [Installer JCB](#3-installer-jcb)
4. [Essai « Hello World » (recommandé)](#4-essai--hello-world--recommandé)
5. [Créer les champs](#5-créer-les-champs)
6. [Créer les 7 vues d'administration](#6-créer-les-7-vues-dadministration)
7. [Créer le composant `club`](#7-créer-le-composant-club)
8. [Compiler et installer](#8-compiler-et-installer)
9. [Vérifier et saisir les données de démonstration](#9-vérifier-et-saisir-les-données-de-démonstration)
10. [Conserver le travail](#10-conserver-le-travail)
11. [Passer en production](#11-passer-en-production)
12. [Problèmes fréquents](#12-problèmes-fréquents)

---

## 1. Principe de JCB

JCB est une extension Joomla qui **fabrique** des composants à partir de ce que vous décrivez. Quatre notions :

| Notion JCB | Rôle | Dans votre projet |
|---|---|---|
| **Champ** (Field) | Une colonne de données | nom, race, date de naissance… |
| **Vue d'administration** (Admin View) | Relie des champs à une table et crée les écrans de liste et de saisie | une vue par table : `chien`, `adherent`… |
| **Composant** (Component) | Regroupe les vues d'administration | `club` |
| **Compilateur** (Compiler) | Génère le code et le `.zip` installable | Compile, puis Install |

Déroulé :

```text
Champs  →  Vues d'administration  →  Composant  →  Compiler  →  Installer
```

JCB ajoute automatiquement les colonnes techniques (identifiant, publié, dates de création et de modification, droits d'accès…).

---

## 2. Préparer l'environnement

JCB est un **outil de développement** : on l'installe sur le **site de test local**, jamais sur le site public.

1. Disposer d'un Joomla 5.4 local (Laragon, PHP 8.3), comme décrit dans `mode-emploi-demo-phase1.md`, Partie A.
2. Se connecter à `http://club.test/administrator`.
3. Pour compiler un composant complet, prévoir de la mémoire PHP suffisante. Si la compilation échoue, augmenter `memory_limit` (par exemple à 512 Mo) dans la configuration PHP de Laragon, puis redémarrer.

> **⚠ à vérifier** — Version cible Joomla 5 : JCB compile pour la version de Joomla sur laquelle il est installé. Vérifiez dans les options globales de JCB si une version cible est proposée, et choisissez Joomla 5.

---

## 3. Installer JCB

1. Télécharger la dernière version de JCB depuis la page des versions du projet sur GitHub (dépôt `vdm-io/Joomla-Component-Builder`).
2. Dans Joomla : **Système > Installer > Extensions**, téléverser le fichier.
3. Vérifier que **Composants > Component Builder** apparaît dans le menu.

---

## 4. Essai « Hello World » (recommandé)

Avant les 7 tables, faites un essai minimal pour comprendre le mécanisme (environ une heure). Le tutoriel officiel se trouve dans le dépôt `joomengine/jcb-documentation` (fichier `Hello-World-with-Joomla-Component-Builder.md`).

Il se résume à :

1. **JCB → Fields → New** : champ `greeting`, type Text.
2. **Admin Views → New** : vue `greeting` / `greetings`, avec ce champ.
3. **Components → New** : composant `World`, relié à la vue.
4. **Compiler** → choisir le composant → **Compile** → **Install**.

Si cet essai fonctionne, passez à la suite. Vous pouvez ensuite désinstaller `World`.

---

## 5. Créer les champs

**Menu : Component Builder → Fields → New.**

Pour chaque champ :

1. Onglet **Set Properties** : renseigner **Name**, **Label** et **Type**.
2. Onglet **Database** : renseigner **Data Type**, **Length**, **Null Switch**. Laisser **Modelling Method** sur `Default`.
3. **Save & Close**.

Un même champ peut être réutilisé dans plusieurs vues (par exemple `nom`). Si JCB le refuse, créez un champ distinct par vue.

### 5.1 Champs texte (Type : Text, VARCHAR)

| Name | Label | Longueur | Null |
|---|---|---|---|
| `nom` | Nom | 255 | NOT NULL |
| `prenom` | Prénom | 255 | NOT NULL |
| `email` | Email | 255 | NULL |
| `telephone` | Téléphone | 30 | NULL |
| `race` | Race | 255 | NULL |
| `num_identification` | N° d'identification (puce / tatouage) | 50 | NULL |
| `num_lof` | N° LOF | 50 | NULL |
| `heure` | Heure (ex. 19:30) | 10 | NULL |
| `lieu` | Lieu | 255 | NULL |

### 5.2 Champs « liste » (Type : List, options statiques, VARCHAR 50)

| Name | Label | Options |
|---|---|---|
| `statut_adherent` | Statut | Actif, Inactif |
| `type_adhesion` | Type d'adhésion | Solo, Famille, Moniteur, Bienfaiteur |
| `statut_chien` | Statut | Actif, Inactif, Parti |
| `sexe` | Sexe | Mâle, Femelle |
| `lof` | LOF | Oui, Non |
| `relation` | Relation | Propriétaire principal, Copropriétaire, Responsable, Conducteur, Autre |
| `jour` | Jour | Lundi, Mardi, Mercredi, Jeudi, Vendredi, Samedi, Dimanche |
| `statut_groupe` | Statut | Actif, Inactif |

### 5.3 Dates et nombre

| Name | Label | Type | Data Type | Null |
|---|---|---|---|---|
| `date_naissance` | Date de naissance | Calendar | DATE | NULL |
| `date_debut` | Date de début | Calendar | DATE | NOT NULL |
| `date_fin` | Date de fin | Calendar | DATE | NULL |
| `capacite` | Capacité maximale | Number | INT | NULL |

> **⚠ à vérifier** — Si le type **Number** n'existe pas dans la liste, utiliser **Text** avec le Data Type `INT`.

### 5.4 Champs « lien » (clés étrangères)

Un champ lien affiche une **liste déroulante** alimentée par une autre table. On utilise le type **SQL**, un type standard de Joomla qui construit une liste à partir d'une requête. La requête doit renvoyer deux colonnes nommées `value` (l'identifiant) et `text` (le libellé affiché).

Paramètres : **Type** = SQL ; **Data Type** = `INT` ; **Length** = `11`.

| Name | Label | Null | Requête (champ Query) |
|---|---|---|---|
| `activite_id` | Activité | NOT NULL | `SELECT id AS value, nom AS text FROM #__club_activite ORDER BY nom` |
| `niveau_id` | Niveau | NOT NULL | `SELECT id AS value, nom AS text FROM #__club_niveau ORDER BY nom` |
| `chien_id` | Chien | NOT NULL | `SELECT id AS value, nom AS text FROM #__club_chien ORDER BY nom` |
| `adherent_id` | Adhérent | NOT NULL | `SELECT id AS value, CONCAT(nom, ' ', prenom) AS text FROM #__club_adherent ORDER BY nom` |
| `adherent_principal_id` | Adhérent principal | NULL | même requête que `adherent_id` |
| `moniteur_id` | Moniteur | NULL | même requête que `adherent_id` |
| `groupe_id` | Groupe | NOT NULL | `SELECT id AS value, nom AS text FROM #__club_groupe ORDER BY nom` |

> **⚠ à vérifier**
> - **Noms de tables** : `#__club_chien` suppose que JCB nomme les tables `#__<composant>_<vue>`. Après la première installation, contrôlez les noms réels dans phpMyAdmin et corrigez les requêtes si besoin.
> - **Type SQL** : s'il n'apparaît pas dans la liste des types de JCB, solution de repli pour la démonstration : un champ **Number** (INT) dans lequel on saisit l'identifiant du chien ou de l'adhérent à la main.
> - **Listes vides au départ** : ces listes déroulantes ne se remplissent qu'une fois les tables installées et des enregistrements saisis (voir l'ordre de saisie à l'étape 9).

---

## 6. Créer les 7 vues d'administration

**Menu : Component Builder → Admin Views → New.**

Pour chaque vue :

1. Renseigner **Name (Singular)**, **Name (Plural)** et **System Name**.
2. Onglet **Fields** → **Add Field**, puis ajouter les champs listés ci-dessous.
3. Pour le champ « titre » : cocher **Show in list**, **Title field**, **Sortable**, **Searchable**, **Linked to edit view**.
4. Pour les autres champs importants : cocher **Show in list**.
5. **Save & Close**.

> Fixer d'abord les noms de la vue, **enregistrer une première fois**, puis revenir ajouter les champs : JCB n'autorise l'ajout de certains éléments qu'après le premier enregistrement.

> **Noms des vues** : choisir des noms **sans tiret bas** (`chienadherent` et non `chien_adherent`), pour éviter des soucis de nommage dans le code généré.

| N° | Singular / Plural / System name | Champs à ajouter (titre en gras) |
|---|---|---|
| 1 | `activite` / `activites` | **`nom`** |
| 2 | `niveau` / `niveaux` | **`nom`**, `activite_id` |
| 3 | `adherent` / `adherents` | **`nom`**, `prenom`, `email`, `telephone`, `statut_adherent`, `type_adhesion`, `adherent_principal_id` |
| 4 | `chien` / `chiens` | **`nom`**, `sexe`, `date_naissance`, `race`, `lof`, `num_lof`, `num_identification`, `statut_chien` |
| 5 | `groupe` / `groupes` | **`nom`**, `activite_id`, `niveau_id`, `jour`, `heure`, `moniteur_id`, `lieu`, `capacite`, `statut_groupe` |
| 6 | `chienadherent` / `chienadherents` | `chien_id`, `adherent_id`, `relation` |
| 7 | `activitechien` / `activitechiens` | `chien_id`, `groupe_id`, `date_debut`, `date_fin` |

**Notes :**

- **Vues 6 et 7** : ce sont des tables de liaison. La vue 6 réalise la relation plusieurs-à-plusieurs chien / adhérent (une ligne par couple : SAM → Sandrine, SAM → Jean). La vue 7 réalise l'affectation chien → groupe.
- **⚠ à vérifier** — Ces deux vues n'ont pas de champ texte naturel pour le « titre ». Si JCB en exige un, ajouter un champ texte facultatif `libelle` (Libellé) dans chacune.
- **Familles** : le champ `adherent_principal_id` de la vue `adherent` suffit pour la démonstration.
- **Photo du chien** : omise dans cette démonstration, à ajouter plus tard.

---

## 7. Créer le composant `club`

**Menu : Component Builder → Components → New.**

1. **Name** : `Club` (le nom système devient `club`).
2. Icône ou image : facultatif.
3. Onglet **Admin View Settings** :
   - relier la vue principale à **Chiens** ;
   - ajouter ensuite les 7 vues d'administration dans la liste des vues du composant (bouton **Add** de la section Admin Views).
4. Activer les options suivantes (comme dans le tutoriel) :
   - **Add to Main Menu**
   - **Allow Sub-menu**
   - **Auto-checking**
   - **History**
   - **Has Metadata**
   - **Has Access**
   - **Allow Import / Export**
5. Section **Site View Options** : **Create Site View → No**.
   > Pas de vue publique : les données (chiens, adhérents, coordonnées) ne doivent exister que dans l'administration.
6. **Save & Close**.

> Un composant ne peut pas être installé tant qu'aucune vue d'administration n'y est reliée.

---

## 8. Compiler et installer

1. Menu **Component Builder → Compiler**.
2. Sélectionner le composant **Club**.
3. Décocher les éléments facultatifs inutiles.
4. Cliquer sur **Compile**.
5. À la fin de la compilation, cliquer sur **Install** : le composant s'installe directement dans ce Joomla de test.

> **⚠ à vérifier** — La compilation produit aussi un fichier `.zip`. Repérez le lien ou le dossier où JCB le dépose : c'est ce fichier qu'on installera plus tard en production.

---

## 9. Vérifier et saisir les données de démonstration

1. Ouvrir **Composants > Club** : les sept vues doivent apparaître.
2. Contrôler dans phpMyAdmin les noms des tables créées (et corriger les requêtes SQL de l'étape 5.4 si elles diffèrent de `#__club_…`).
3. Saisir les données **dans cet ordre**, car les listes déroulantes dépendent des enregistrements existants :

| Ordre | Vue | Exemples (fictifs) |
|---|---|---|
| 1 | Activités | Obéissance, Agilité, Hooper |
| 2 | Niveaux | Chiot, Obéissance 1, Obéissance 2, Loisir, Compétition |
| 3 | Adhérents | DEMO Sandrine, DEMO Dupont Jean, DEMO Dupont Marie (principal : Jean), DEMO Martin Sophie |
| 4 | Chiens | DEMO SAM, DEMO REX, DEMO NALA |
| 5 | Groupes | Obéissance 2 (mercredi 19:30), Agility Loisir (samedi 10:00) |
| 6 | Chien/adhérent | SAM→Sandrine (principal), SAM→Jean (copropriétaire), REX→Sandrine, NALA→Sophie |
| 7 | Activité/chien | SAM→Obéissance 2 et Agility Loisir, REX→Obéissance 2 |

> **Utiliser uniquement des données fictives**, préfixées par `DEMO`.

4. Les droits par groupe (moniteur, vétérinaire, financier) se règlent ensuite dans **Composants > Club > Options > onglet Droits**, comme décrit dans `mode-emploi-demo-production.md`.

---

## 10. Conserver le travail

Le plus précieux n'est pas le `.zip` mais la **définition** du composant dans JCB : en cas de modification, on recompile à partir d'elle.

- Dans **Components**, utiliser la fonction **Export component** de JCB (elle exporte aussi les vues d'administration et les champs liés) et garder le fichier ou la clé générée.
- Mettre dans le dépôt Git : le `.zip` compilé, l'export JCB, ce mode d'emploi et un jeu de données fictives (`demo_data.sql`).

```bash
git add .
git commit -m "Composant club généré avec JCB (phase 1)"
git push
```

> Si vous recompilez et réinstallez le composant, les tables peuvent être recréées et les données saisies perdues. Avant de désinstaller : dans la vue d'administration, onglet **MySQL**, activer la **sauvegarde de table** (en excluant les champs `created_by`, `modified_by`, `access`, `asset_id`), puis compiler avant de désinstaller.

---

## 11. Passer en production

1. Faire une sauvegarde complète du site (Akeeba Backup).
2. Installer **uniquement le `.zip` du composant `club`** (étape 8) sur le site réel. **Ne pas installer JCB en production.**
3. Appliquer le mode d'emploi `mode-emploi-demo-production.md` (groupes, droits, données fictives, nettoyage).

---

## 12. Problèmes fréquents

| Symptôme | Piste |
|---|---|
| L'installation échoue : « aucune vue » | Relier au moins une vue d'administration au composant. |
| La compilation s'arrête | Augmenter la mémoire PHP (`memory_limit`). |
| Une liste déroulante est vide | Saisir d'abord des enregistrements dans la table liée ; vérifier le nom de la table dans la requête. |
| Erreur SQL à l'ouverture d'une fiche | La requête du champ SQL renvoie un nom de table ou de colonne incorrect : la tester dans phpMyAdmin. |
| Le type SQL est absent | Utiliser le champ Number (INT) et saisir les identifiants à la main pour la démonstration. |
| Des données ont disparu après réinstallation | Activer la sauvegarde de table avant de désinstaller (étape 10). |
| Le moniteur voit trop de données | Normal : le filtrage « seulement ses groupes » demande un développement spécifique, hors démonstration. |