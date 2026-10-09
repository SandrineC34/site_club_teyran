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

**L'écran du formulaire** comporte :

- en haut, les boutons **Enregistrer**, **Enregistrer & Fermer**, **Enregistrer & Nouveau**, **Annuler** ;
- trois zones : **Type \***, **Name \*** et **Category** ;
- dessous, les onglets **Set Properties**, **Database**, **Scripts**, **Type Info**, **Publishing**, **Permissions**.

**Pour chaque champ, dans cet ordre :**

1. **Type** (liste déroulante en haut à gauche) : le choisir **en premier**. Tant qu'aucun type n'est sélectionné, l'onglet *Set Properties* affiche seulement le message « Please select a field type that you would like to build » et aucune propriété à remplir. Le type à choisir est indiqué dans les tableaux ci-dessous (par exemple **Text** pour un champ texte).
2. **Name** : le nom système du champ, en minuscules, sans espace ni accent (`nom`, `prenom`, `date_naissance`…). C'est la valeur de la colonne « Name » des tableaux.
3. **Category** : laisser vide.
4. Onglet **Set Properties** : une fois le type choisi, les propriétés du champ s'affichent. Renseigner le **libellé** (colonne « Label » des tableaux) et, si l'option est proposée, la rendre **obligatoire** pour les champs `NOT NULL`.
5. Onglet **Database** : renseigner **Data Type**, **Length** et **Null Switch** selon les tableaux. Laisser **Modelling Method** sur `Default`.
6. Cliquer sur **Enregistrer & Nouveau** pour enchaîner avec le champ suivant (ou **Enregistrer & Fermer** pour le dernier).

> **Important — ligne `name` de l'onglet Set Properties.** Pour **tous** les types de champ (Text, List, SQL…), JCB préremplit la ligne `name` avec un exemple (`mytextvalue`, `mylist`, `title`…). Il faut la remplacer par le nom du champ (le même que celui saisi en haut dans **Name**), sinon plusieurs champs porteront le même nom. Dans le tableau *Linked Fields* d'une vue, chaque champ est affiché sous la forme `nom_du_champ [valeur de la ligne name - type]` : les deux noms doivent être identiques (par exemple `prenom [prenom - Text]`, et non `prenom [mytextvalue - Text]`).

> **⚠ à vérifier** — Je n'ai pas vu le contenu de l'onglet *Set Properties* après le choix du type : l'emplacement exact du champ *Label* peut varier. Si vous ne le trouvez pas, ou si le type **Text** n'apparaît pas dans la liste, notez les types proposés avant de continuer.

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

**Où saisir les options.** Choisir d'abord **Type = List** (en haut à gauche). JCB charge alors les réglages propres à ce type dans l'onglet **Set Properties** : c'est là que se trouve la zone des **options** de la liste. Tant que le type n'est pas choisi, cette zone n'existe pas, ce qui explique qu'on ne la voie pas.

**Format de saisie** (d'après la documentation officielle de JCB) : une seule ligne, les options séparées par des virgules, chaque option écrite `valeur|libellé`.

```text
valeur1|Libellé 1,valeur2|Libellé 2,valeur3|Libellé 3
```

- Si la valeur et le libellé sont identiques, on peut omettre la barre verticale (`Actif,Inactif`).
- Pour garder des valeurs propres en base de données, ce document utilise des valeurs sans accent ni espace, et le libellé affiché (avec accents) après la barre `|`.
- Pas de guillemets dans les options, et pas de virgule à l'intérieur d'un libellé : la virgule sépare les options.

| Name | Label | Options à saisir (ligne unique) |
|---|---|---|
| `statut_adherent` | Statut | `actif\|Actif,inactif\|Inactif` |
| `type_adhesion` | Type d'adhésion | `solo\|Solo,famille\|Famille,moniteur\|Moniteur,bienfaiteur\|Bienfaiteur` |
| `statut_chien` | Statut | `actif\|Actif,inactif\|Inactif,parti\|Parti` |
| `sexe` | Sexe | `male\|Mâle,femelle\|Femelle` |
| `lof` | LOF | `oui\|Oui,non\|Non` |
| `relation` | Relation | `proprietaire_principal\|Propriétaire principal,coproprietaire\|Copropriétaire,responsable\|Responsable,conducteur\|Conducteur,autre\|Autre` |
| `jour` | Jour | `lundi\|Lundi,mardi\|Mardi,mercredi\|Mercredi,jeudi\|Jeudi,vendredi\|Vendredi,samedi\|Samedi,dimanche\|Dimanche` |
| `statut_groupe` | Statut | `actif\|Actif,inactif\|Inactif` |

> Dans le tableau, la barre verticale est précédée d'un `\` uniquement pour l'affichage Markdown. **Dans JCB, saisir une barre simple `|`** : par exemple `actif|Actif,inactif|Inactif`.

**Comment est présenté l'onglet *Set Properties*.** Il contient un tableau à trois colonnes : **Property** (liste déroulante), **Value** et **Description**. Chaque ligne correspond à un attribut du champ Joomla. Pour un champ de type List, JCB préremplit quelques lignes avec des exemples :

| Ligne (Property) | Valeur préremplie | À faire |
|---|---|---|
| `type` | `list` | Ne pas modifier |
| `name` | `mylist` | Remplacer par le même nom que celui saisi en haut dans **Name** (par exemple `statut_adherent`) |
| `label` | `Select an option` | **Remplacer par le libellé du champ** (par exemple `Statut`) : c'est ici que se règle le Label |
| `description`, `Message`… | vide | Laisser vide |

Les options de la liste se saisissent dans une **autre ligne de ce même tableau**, plus bas : faire défiler la page vers le bas et repérer la ligne dont la colonne **Property** indique `options` (ou `option`). Coller dans sa colonne **Value** la ligne d'options du tableau ci-dessus (par exemple `actif|Actif,inactif|Inactif`). Si cette ligne n'existe pas, cliquer sur le menu déroulant d'une ligne vide de la colonne **Property** et choisir `options` dans la liste ; la colonne **Description** de la ligne explique ce que JCB attend.

> **⚠ à vérifier** — L'intitulé exact de la ligne des options n'était pas visible sur ma capture (elle se trouve plus bas dans le tableau). Si vous ne la trouvez pas, envoyez-moi une capture de la suite du tableau.

**Astuce pour le champ `email`** (section 5.1) : dans l'onglet *Set Properties*, JCB propose un réglage de **validation** ; choisir `Email` pour que Joomla vérifie le format de l'adresse.

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

### 6.1 Procédure (à répéter pour chaque vue)

1. Onglet **Details** : renseigner **Name (single record)** (singulier) et **Name (list of records)** (pluriel). Le **System Name** se remplit tout seul (par exemple `activite / activites`). Remplir aussi **Short Description**, qui est **obligatoire** (une courte phrase, par exemple « Activités pratiquées au club »). Laisser **Type** sur `read/write` ; les icônes sont facultatives.
2. Cliquer sur **Enregistrer** (sans fermer) pour créer la vue une première fois.
3. Ouvrir l'onglet **Fields**, section **Linked Fields**, puis cliquer sur **+ Create**. Le formulaire des champs liés s'ouvre.
4. Pour chaque champ de la vue : cliquer sur le bouton vert **+** pour ajouter une ligne, sélectionner le champ dans la zone **Field \*** (bouton **Modifier** pour ouvrir la liste), puis régler la ligne selon les tableaux de la section 6.3.
5. Cliquer sur **Enregistrer & Fermer**, vérifier que les champs apparaissent dans **Linked Fields**, puis **Enregistrer** la vue.

> Fixer d'abord les noms de la vue, **enregistrer une première fois**, puis revenir ajouter les champs : JCB n'autorise l'ajout de certains éléments qu'après le premier enregistrement.

> **Noms des vues** : choisir des noms **sans tiret bas** (`chienadherent` et non `chien_adherent`), pour éviter des soucis de nommage dans le code généré.

### 6.2 Comment décider des réglages d'une ligne de champ

Chaque ligne comporte les mêmes réglages. Règles appliquées dans les tableaux de 6.3 :

| Réglage | Règle |
|---|---|
| **Order in Edit** | Position du champ dans le formulaire de saisie : 1 = en haut. Seul l'ordre compte (pas besoin de nombres consécutifs). |
| **Order in list views** | `0` = champ **non affiché** dans la liste ; `1`, `2`, `3`… = colonne affichée, dans cet ordre. (Il n'existe pas de case « Show in list ».) |
| **Title** | **Un seul champ par vue** : celui qui identifie l'enregistrement (`nom` en général). |
| **Sortable** | Cocher pour les colonnes affichées dont le tri est utile (nom, statut, type, activité…). |
| **Searchable** | Cocher pour les champs texte à retrouver par la zone de recherche (nom, prénom, email, race, n° d'identification…). Ne fonctionne que pour un champ affiché dans la liste. |
| **Link** | Cocher **uniquement sur le champ titre** : un clic sur sa valeur ouvre la fiche. |
| **Filter** | Laisser sur `No` pour la démonstration. |
| **Admin**, **Admin Tabs**, **Alignment**, **Permissions** | Laisser les valeurs par défaut (`Default`, `Details`, `Left in Tab`). |

### 6.3 Réglages complets, vue par vue

Légende : ✔ = case à cocher ; — = laisser décoché ; liste = valeur de **Order in list views**.

#### Vue 1 — `activite` / `activites`

| Champ | Order in Edit | Liste | Title | Sortable | Searchable | Link |
|---|---|---|---|---|---|---|
| `nom` | 1 | 1 | ✔ | ✔ | ✔ | ✔ |

#### Vue 2 — `niveau` / `niveaux`

| Champ | Order in Edit | Liste | Title | Sortable | Searchable | Link |
|---|---|---|---|---|---|---|
| `nom` | 1 | 1 | ✔ | ✔ | ✔ | ✔ |
| `activite_id` | 2 | 2 | — | ✔ | — | — |

#### Vue 3 — `adherent` / `adherents`

| Champ | Order in Edit | Liste | Title | Sortable | Searchable | Link |
|---|---|---|---|---|---|---|
| `nom` | 1 | 1 | ✔ | ✔ | ✔ | ✔ |
| `prenom` | 2 | 2 | — | ✔ | ✔ | — |
| `email` | 3 | 3 | — | — | ✔ | — |
| `telephone` | 4 | 0 | — | — | — | — |
| `type_adhesion` | 5 | 4 | — | ✔ | — | — |
| `statut_adherent` | 6 | 5 | — | ✔ | — | — |
| `adherent_principal_id` | 7 | 0 | — | — | — | — |

#### Vue 4 — `chien` / `chiens`

| Champ | Order in Edit | Liste | Title | Sortable | Searchable | Link |
|---|---|---|---|---|---|---|
| `nom` | 1 | 1 | ✔ | ✔ | ✔ | ✔ |
| `sexe` | 2 | 3 | — | ✔ | — | — |
| `date_naissance` | 3 | 0 | — | — | — | — |
| `race` | 4 | 2 | — | ✔ | ✔ | — |
| `lof` | 5 | 0 | — | — | — | — |
| `num_lof` | 6 | 0 | — | — | — | — |
| `num_identification` | 7 | 4 | — | — | ✔ | — |
| `statut_chien` | 8 | 5 | — | ✔ | — | — |

#### Vue 5 — `groupe` / `groupes`

| Champ | Order in Edit | Liste | Title | Sortable | Searchable | Link |
|---|---|---|---|---|---|---|
| `nom` | 1 | 1 | ✔ | ✔ | ✔ | ✔ |
| `activite_id` | 2 | 2 | — | ✔ | — | — |
| `niveau_id` | 3 | 3 | — | ✔ | — | — |
| `jour` | 4 | 4 | — | ✔ | — | — |
| `heure` | 5 | 5 | — | — | — | — |
| `moniteur_id` | 6 | 6 | — | — | — | — |
| `lieu` | 7 | 0 | — | — | — | — |
| `capacite` | 8 | 0 | — | — | — | — |
| `statut_groupe` | 9 | 7 | — | ✔ | — | — |

#### Vue 6 — `chienadherent` / `chienadherents`

| Champ | Order in Edit | Liste | Title | Sortable | Searchable | Link |
|---|---|---|---|---|---|---|
| `chien_id` | 1 | 1 | ✔ | ✔ | — | ✔ |
| `adherent_id` | 2 | 2 | — | ✔ | — | — |
| `relation` | 3 | 3 | — | ✔ | — | — |

#### Vue 7 — `activitechien` / `activitechiens`

| Champ | Order in Edit | Liste | Title | Sortable | Searchable | Link |
|---|---|---|---|---|---|---|
| `chien_id` | 1 | 1 | ✔ | ✔ | — | ✔ |
| `groupe_id` | 2 | 2 | — | ✔ | — | — |
| `date_debut` | 3 | 3 | — | ✔ | — | — |
| `date_fin` | 4 | 4 | — | — | — | — |

### 6.4 Notes

- **Vues 6 et 7** : ce sont des tables de liaison. La vue 6 réalise la relation plusieurs-à-plusieurs chien / adhérent (une ligne par couple : SAM → Sandrine, SAM → Jean). La vue 7 réalise l'affectation chien → groupe.
- **⚠ à vérifier** — Ces deux vues n'ont pas de champ texte naturel pour le titre : on désigne `chien_id` comme titre. Si JCB exige un vrai champ texte, ou si la liste affiche un numéro à la place du nom du chien, ajouter un champ texte facultatif `libelle` (Libellé) dans chacune de ces vues et le désigner comme titre.
- **Nom des champs** : si vous avez nommé un champ différemment (par exemple `status_adherent` au lieu de `statut_adherent`), sélectionnez simplement celui qui existe dans la liste.
- **Familles** : le champ `adherent_principal_id` de la vue `adherent` suffit pour la démonstration.
- **Photo du chien** : omise dans cette démonstration, à ajouter plus tard.

---


Field Relations sert à lier des champs entre eux dans un même formulaire, par exemple une liste de niveaux qui change selon l'activité choisie. Ce n'est pas dans le cahier des charges de la phase 1.
Field Conditions sert à afficher un champ seulement si un autre a une certaine valeur, par exemple « Numéro LOF » uniquement quand LOF est sur « Oui ». C'est une amélioration de confort, pas un prérequis. Vous pourrez l'ajouter plus tard.

Attention pour les activités si faudra revoir cela
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
| « Le formulaire ne peut pas être soumis, car certaines données requises ne sont pas complétées » à l'enregistrement d'un champ | Un champ obligatoire est vide : repérer les zones surlignées en rouge et les onglets marqués. Pour un champ List, vérifier la ligne `options` (onglet Set Properties), supprimer les lignes de propriétés vides (bouton rouge −), et remplir **Data Type**, **Length** et **Null Switch** dans l'onglet Database. |
| Dans **Linked Fields**, un champ s'affiche avec un nom entre crochets différent de son nom (`prenom [mytextvalue - Text]`) | La ligne `name` du champ contient encore l'exemple de JCB. Modifier le champ (icône crayon), onglet Set Properties, et la remplacer par le nom du champ. |
| Un cadenas apparaît à côté d'un champ dans **Linked Fields** | Le champ est probablement verrouillé (ouvert puis quitté sans enregistrer). Cliquer sur le cadenas pour le déverrouiller. |
| Impossible de modifier **Order in list views** depuis le tableau *Linked Fields* | Ce tableau est un résumé en lecture seule : cliquer sur le bouton **Edit** à côté du titre *Linked Fields*, puis choisir la valeur dans la liste déroulante de chaque ligne. |
| Une liste déroulante est vide | Saisir d'abord des enregistrements dans la table liée ; vérifier le nom de la table dans la requête. |
| Erreur SQL à l'ouverture d'une fiche | La requête du champ SQL renvoie un nom de table ou de colonne incorrect : la tester dans phpMyAdmin. |
| Le type SQL est absent | Utiliser le champ Number (INT) et saisir les identifiants à la main pour la démonstration. |
| Des données ont disparu après réinstallation | Activer la sauvegarde de table avant de désinstaller (étape 10). |
| Le moniteur voit trop de données | Normal : le filtrage « seulement ses groupes » demande un développement spécifique, hors démonstration. |