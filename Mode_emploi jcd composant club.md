# Mode d'emploi — Créer le composant `club` avec Joomla Component Builder (JCB)

**Site Joomla du club cynophile — Phase 1 (socle)**

Ce document explique pas à pas comment fabriquer, avec **JCB**, le composant `club` contenant les **8 tables** de la phase 1 : `activite`, `niveau`, `adherent`, `chien`, `groupe`, `chienadherent`, `activitechien` et **`fichier`** (base documentaire de partage de fichiers).

> **À lire d'abord**
> - Les étapes générales (champ → vue d'administration → composant → compilation → installation) sont issues du tutoriel officiel « Hello World » de JCB.
> - Les adaptations propres à votre projet (clés étrangères, tables de liaison, fichiers protégés) sont signalées par **⚠ à vérifier** : je n'ai pas pu les tester dans l'interface de JCB. Faites-les d'abord sur le site de test.
> - Intitulés et boutons peuvent varier légèrement selon la version de JCB.
> - La base documentaire (sections 5.5, 6, 8) est la partie la plus délicate : le choix du champ de téléversement et la protection des fichiers demandent un peu de code personnalisé.

## Ce qui a changé dans cette version

| Modification | Où |
|---|---|
| 7 vues → **8 vues** : ajout de la vue `fichier` (base documentaire) | Sections 6, 7 |
| Nouveaux champs : `titre`, `description_fichier`, `fichier_joint`, `type_fichier`, `portee`, `fichier_groupe_id`, `fichier_chien_id`, `fichier_adherent_id`, `user_id` | Sections 5.1 à 5.5 |
| Champ `user_id` (compte Joomla) ajouté à la vue `adherent` | Section 6.3 |
| Le composant a désormais **une vue publique « Mes documents »** (avant : aucune vue publique) | Sections 7, 8 |
| Nouvelle section **Espace membre : afficher et télécharger les fichiers** | Section 8 |
| Nouvelle section **Modifier le composant après installation** | Section 13 |
| Paragraphe orphelin « vue _ document » et notes Field Relations / Conditions rangés | Section 6.4 |
| Renvois d'étapes renumérotés | Tout le document |

## Sommaire

1. [Principe de JCB](#1-principe-de-jcb)
2. [Préparer l'environnement](#2-préparer-lenvironnement)
3. [Installer JCB](#3-installer-jcb)
4. [Essai « Hello World » (recommandé)](#4-essai--hello-world--recommandé)
5. [Créer les champs](#5-créer-les-champs)
6. [Créer les 8 vues d'administration](#6-créer-les-8-vues-dadministration)
7. [Créer le composant `club`](#7-créer-le-composant-club)
8. [Espace membre : afficher et télécharger les fichiers](#8-espace-membre--afficher-et-télécharger-les-fichiers)
9. [Compiler et installer](#9-compiler-et-installer)
10. [Vérifier et saisir les données de démonstration](#10-vérifier-et-saisir-les-données-de-démonstration)
11. [Conserver le travail](#11-conserver-le-travail)
12. [Passer en production](#12-passer-en-production)
13. [Modifier le composant après installation](#13-modifier-le-composant-après-installation)
14. [Problèmes fréquents](#14-problèmes-fréquents)

---

## 1. Principe de JCB

JCB est une extension Joomla qui **fabrique** des composants à partir de ce que vous décrivez. Quatre notions :

| Notion JCB | Rôle | Dans votre projet |
|---|---|---|
| **Champ** (Field) | Une colonne de données | nom, race, date de naissance, fichier… |
| **Vue d'administration** (Admin View) | Relie des champs à une table et crée les écrans de liste et de saisie | une vue par table : `chien`, `adherent`, `fichier`… |
| **Composant** (Component) | Regroupe les vues d'administration (et les vues du site) | `club` |
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
4. **Pour la base documentaire** : vérifier dans la configuration PHP que le téléversement est actif (`file_uploads = On`) et que `upload_max_filesize` et `post_max_size` sont au moins à 10 Mo.

> **⚠ à vérifier** — Version cible Joomla 5 : JCB compile pour la version de Joomla sur laquelle il est installé. Vérifiez dans les options globales de JCB si une version cible est proposée, et choisissez Joomla 5. 

---

## 3. Installer JCB

1. Télécharger la dernière version de JCB depuis la page des versions du projet sur GitHub (dépôt `vdm-io/Joomla-Component-Builder`).
2. Dans Joomla : **Système > Installer > Extensions**, téléverser le fichier.
3. Vérifier que **Composants > Component Builder** apparaît dans le menu.

---

## 4. Essai « Hello World » (recommandé)

Avant les 8 tables, faites un essai minimal pour comprendre le mécanisme (environ une heure). Le tutoriel officiel se trouve dans le dépôt `joomengine/jcb-documentation` (fichier `Hello-World-with-Joomla-Component-Builder.md`).

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
| `titre` | Titre du fichier | 255 | NOT NULL |

> Le champ `titre` sert à la vue `fichier` (section 5.5). Il existe aussi un `nom` pour les autres vues : ne pas les confondre.

**Astuce pour le champ `email`** : dans l'onglet *Set Properties*, JCB propose un réglage de **validation** ; choisir `Email` pour que Joomla vérifie le format de l'adresse.

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
| `type_fichier` | Type de fichier | `vaccination\|Vaccination,administratif\|Administratif,licence\|Licence,cours\|Cours,club\|Club,autre\|Autre` |
| `portee` | Portée (qui peut voir) | `club\|Tous les adhérents,groupe\|Un groupe,chien\|Un chien,adherent\|Un adhérent,moniteurs\|Moniteurs` |

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

### 5.3 Dates et nombre

| Name | Label | Type | Data Type | Null |
|---|---|---|---|---|
| `date_naissance` | Date de naissance | Calendar | DATE | NULL |
| `date_debut` | Date de début | Calendar | DATE | NOT NULL |
| `date_fin` | Date de fin | Calendar | DATE | NULL |
| `capacite` | Capacité maximale | Number | INT | NULL |

> **⚠ à vérifier** — Si le type **Number** n'existe pas dans la liste, utiliser **Text** avec le Data Type `INT`.

> La date de dépôt d'un fichier n'a pas besoin de champ : JCB ajoute automatiquement la date de création et l'auteur (colonnes techniques).

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
| `fichier_groupe_id` | Groupe concerné | **NULL** | même requête que `groupe_id` |
| `fichier_chien_id` | Chien concerné | **NULL** | même requête que `chien_id` |
| `fichier_adherent_id` | Adhérent concerné | **NULL** | même requête que `adherent_id` |

> **Pourquoi des champs `fichier_…` distincts ?** Dans la vue `fichier`, le groupe, le chien et l'adhérent sont **facultatifs** (selon la portée). Or les champs `groupe_id`, `chien_id` et `adherent_id` sont `NOT NULL`. Le réglage « Null » étant propre au champ, il faut des champs séparés, déclarés `NULL`.

**Champ « compte Joomla » (liaison adhérent ↔ utilisateur)** — Type = **User** (type standard de Joomla qui liste les utilisateurs), **Data Type** = `INT`, **Length** = `11`, **Null** = `NULL` :

| Name | Label | Null |
|---|---|---|
| `user_id` | Compte Joomla | NULL |

> **⚠ à vérifier**
> - **Noms de tables** : `#__club_chien` suppose que JCB nomme les tables `#__<composant>_<vue>`. Après la première installation, contrôlez les noms réels dans phpMyAdmin et corrigez les requêtes si besoin.
> - **Type SQL** : s'il n'apparaît pas dans la liste des types de JCB, solution de repli pour la démonstration : un champ **Number** (INT) dans lequel on saisit l'identifiant du chien ou de l'adhérent à la main.
> - **Type User** : s'il n'apparaît pas, utiliser le type SQL avec la requête `SELECT id AS value, name AS text FROM #__users ORDER BY name`.
> - **Listes vides au départ** : ces listes déroulantes ne se remplissent qu'une fois les tables installées et des enregistrements saisis (voir l'ordre de saisie à l'étape 10).

### 5.5 Champs de la base documentaire xx

| Name | Label | Type | Data Type | Null | Remarque |
|---|---|---|---|---|---|
| `titre` | Titre du fichier | Text | VARCHAR 255 | NOT NULL | voir 5.1 |
| `description_fichier` | Description | Textarea | TEXT | NULL | facultatif |
| `fichier_joint` | Fichier | **voir ci-dessous** | VARCHAR 255 | NOT NULL | stocke le **nom** du fichier |
| `type_fichier` | Type de fichier | List | VARCHAR 50 | NOT NULL | voir 5.2 |
| `portee` | Portée | List | VARCHAR 50 | NOT NULL | voir 5.2 |
| `fichier_groupe_id` | Groupe concerné | SQL | INT 11 | NULL | voir 5.4 |
| `fichier_chien_id` | Chien concerné | SQL | INT 11 | NULL | voir 5.4 |
| `fichier_adherent_id` | Adhérent concerné | SQL | INT 11 | NULL | voir 5.4 |

**Choix du champ de téléversement (`fichier_joint`).**

> **⚠ Point de sécurité essentiel.** Le champ **Media** standard de Joomla range les fichiers dans le dossier public `images/` : **n'importe qui connaissant l'adresse peut alors les télécharger, même sans être connecté.** Il ne convient donc **pas** à des fichiers privés (certificats vaccinaux, justificatifs…).

Trois solutions, de la plus simple à la plus sûre :

| Solution | Principe | Verdict |
|---|---|---|
| **A. Type File / Upload de JCB** | Si JCB propose un type de champ de téléversement de fichier (à chercher dans la liste des types : *Upload*, *File*, *Media*), l'essayer et noter **où il range** le fichier. | ⚠ à vérifier : acceptable seulement si le dossier peut être protégé (section 8.3). |
| **B. Champ Text + code personnalisé** | Le champ stocke un nom de fichier ; le téléversement vers un dossier privé est géré par du code personnalisé (JCB : *Custom Code*). | Le plus sûr, mais demande du développement. |
| **C. Extension existante** | Utiliser DOCman ou Phoca Download pour les fichiers, et garder `club` pour le reste. | À envisager si A et B sont trop coûteux (cahier des charges, section 20.8). |

Pour la phase 1, essayer **A** ; en cas d'échec, passer à **C**. Dans tous les cas, ne **jamais** laisser des fichiers privés dans un dossier public.

---

## 6. Créer les 8 vues d'administration

**Menu : Component Builder → Admin Views → New.**

### 6.1 Procédure (à répéter pour chaque vue)

1. Onglet **Details** : renseigner **Name (single record)** (singulier) et **Name (list of records)** (pluriel). Le **System Name** se remplit tout seul (par exemple `activite / activites`). Remplir aussi **Short Description**, qui est **obligatoire** (une courte phrase, par exemple « Activités pratiquées au club »). Laisser **Type** sur `read/write` ; les icônes sont facultatives.
2. Cliquer sur **Enregistrer** (sans fermer) pour créer la vue une première fois.
3. Ouvrir l'onglet **Fields**, section **Linked Fields**, puis cliquer sur **+ Create**. Le formulaire des champs liés s'ouvre.
4. Pour chaque champ de la vue : cliquer sur le bouton vert **+** pour ajouter une ligne, sélectionner le champ dans la zone **Field \*** (bouton **Modifier** pour ouvrir la liste), puis régler la ligne selon les tableaux de la section 6.3.
5. Cliquer sur **Enregistrer & Fermer**, vérifier que les champs apparaissent dans **Linked Fields**, puis **Enregistrer** la vue.

> Fixer d'abord les noms de la vue, **enregistrer une première fois**, puis revenir ajouter les champs : JCB n'autorise l'ajout de certains éléments qu'après le premier enregistrement.

> **Noms des vues** : choisir des noms **sans tiret bas** (`chienadherent` et non `chien_adherent`), pour éviter des soucis de nommage dans le code généré.

> **Pourquoi `fichier` et non `document` ?** « document » est un mot utilisé en interne par Joomla ; l'employer comme nom de vue risque de provoquer un conflit dans le code généré (⚠ à vérifier). `fichier` / `fichiers` est plus sûr.

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
| **Filter** | Laisser sur `No` pour la démonstration (sauf vue `fichier`, voir 6.3). |
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
| `user_id` | 8 | 0 | — | — | — | — |

> `user_id` relie l'adhérent à son compte Joomla. Sans lui, la page « Mes documents » ne peut pas savoir quels fichiers montrer. Un compte Joomla ne doit être associé qu'à un seul adhérent.

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

#### Vue 8 — `fichier` / `fichiers` (base documentaire)

**Details** : Short Description = « Fichiers partagés avec les personnes autorisées ».

| Champ | Order in Edit | Liste | Title | Sortable | Searchable | Link |
|---|---|---|---|---|---|---|
| `titre` | 1 | 1 | ✔ | ✔ | ✔ | ✔ |
| `type_fichier` | 2 | 2 | — | ✔ | — | — |
| `portee` | 3 | 3 | — | ✔ | — | — |
| `fichier_groupe_id` | 4 | 4 | — | ✔ | — | — |
| `fichier_chien_id` | 5 | 5 | — | ✔ | — | — |
| `fichier_adherent_id` | 6 | 0 | — | — | — | — |
| `fichier_joint` | 7 | 0 | — | — | — | — |
| `description_fichier` | 8 | 0 | — | — | — | — |

Réglages complémentaires de cette vue :

- **Filter** : mettre `Yes` (ou équivalent) sur `type_fichier` et `portee`, pour filtrer la liste dans l'administration.
- Garder les colonnes techniques **Published** et **Access** (ajoutées par JCB quand le composant a l'option **Has Access**, section 7) : elles servent à dépublier un fichier et à lui appliquer un niveau d'accès Joomla.
- La date de dépôt et l'auteur sont conservés automatiquement par JCB (création / modification).

### 6.4 Notes

- **Vues 6 et 7** : ce sont des tables de liaison. La vue 6 réalise la relation plusieurs-à-plusieurs chien / adhérent (une ligne par couple : SAM → Sandrine, SAM → Jean). La vue 7 réalise l'affectation chien → groupe.
- **⚠ à vérifier** — Ces deux vues n'ont pas de champ texte naturel pour le titre : on désigne `chien_id` comme titre. Si JCB exige un vrai champ texte, ou si la liste affiche un numéro à la place du nom du chien, ajouter un champ texte facultatif `libelle` (Libellé) dans chacune de ces vues et le désigner comme titre.
- **Vue 8 (`fichier`)** : un fichier a une **portée** (qui peut le voir). Les champs groupe / chien / adhérent ne sont à remplir que selon la portée choisie : portée « Un groupe » → remplir *Groupe concerné* ; « Un chien » → *Chien concerné* ; « Un adhérent » → *Adhérent concerné* ; « Tous les adhérents » ou « Moniteurs » → rien. Ce contrôle de cohérence (par exemple refuser une portée « Un chien » sans chien) demande un petit code personnalisé, ou se fait à la saisie.
- **Nom des champs** : si vous avez nommé un champ différemment (par exemple `status_adherent` au lieu de `statut_adherent`), sélectionnez simplement celui qui existe dans la liste.
- **Familles** : le champ `adherent_principal_id` de la vue `adherent` suffit pour la démonstration.
- **Photo du chien** : omise dans cette démonstration, à ajouter plus tard.
- **Activités et niveaux** : la liste du cahier des charges (section 7.1) est une liste de départ, à valider avec le club (Obéissance / Obédience, Canicross / Canimarche). Les activités et niveaux sont des **enregistrements** (vues 1 et 2) et non des champs fixes : on les modifie dans l'administration sans recompiler. Prévoir à terme un champ **statut** (actif / inactif) sur ces deux vues pour désactiver sans supprimer (section 13).
- **Field Relations** sert à lier des champs entre eux dans un même formulaire, par exemple une liste de niveaux qui change selon l'activité choisie. Ce n'est pas dans le cahier des charges de la phase 1. Ce serait toutefois utile ici pour la vue `fichier` (afficher le champ « Groupe » seulement si la portée est « Un groupe »).
- **Field Conditions** sert à afficher un champ seulement si un autre a une certaine valeur, par exemple « Numéro LOF » uniquement quand LOF est sur « Oui ». C'est une amélioration de confort, pas un prérequis. Vous pourrez l'ajouter plus tard.

---

## 7. Créer le composant `club`

**Menu : Component Builder → Components → New.**

1. **Name** : `Club` (le nom système devient `club`).
2. Icône ou image : facultatif.
3. Onglet **Admin View Settings** :
   - relier la vue principale à **Chiens** ;
   - ajouter ensuite les **8** vues d'administration dans la liste des vues du composant (bouton **Add** de la section Admin Views).
4. Activer les options suivantes (comme dans le tutoriel) :
   - **Add to Main Menu**
   - **Allow Sub-menu**
   - **Auto-checking**
   - **History**
   - **Has Metadata**
   - **Has Access** (nécessaire pour les niveaux d'accès des fichiers)
   - **Allow Import / Export**
5. Section **Site View Options** : **Create Site View → Yes**, mais **uniquement pour la vue « Mes documents »** décrite en section 8.
   > Les données des chiens, adhérents et leurs coordonnées ne doivent **toujours pas** être exposées sur le site : une seule vue publique, « Mes documents », qui ne montre que les fichiers autorisés pour l'utilisateur connecté.
6. **Save & Close**.

> Un composant ne peut pas être installé tant qu'aucune vue d'administration n'y est reliée.

---

## 8. Espace membre : afficher et télécharger les fichiers

Cette section met en place la page « **Mes documents** » : une liste de fichiers, filtrée selon l'utilisateur connecté, avec un bouton de téléchargement protégé. C'est la partie qui demande du code personnalisé.

> **⚠ à vérifier** — Je n'ai pas pu tester cette partie dans JCB. Les étapes ci-dessous donnent la démarche et la logique ; les intitulés de JCB (Site Views, Dynamic Get, Custom Code) peuvent différer. Travaillez-y d'abord sur le site de test.

### 8.1 Créer la vue du site « fichiers »

**Menu : Component Builder → Site Views → New.**

1. **Name** : `fichiers`. Short Description : « Mes documents ».
2. Associer cette vue au composant `club` (section 7).
3. Dans l'onglet de **récupération des données** (*Main Get* ou *Dynamic Get*), choisir la table liée à la vue d'administration `fichier`.
4. Dans la mise en page de la vue (*Default / Template*), afficher pour chaque fichier : **titre**, **type**, **date**, et un **lien de téléchargement** (voir 8.3).

### 8.2 Filtrer : ne montrer que les fichiers autorisés

La liste doit appliquer la règle de visibilité du cahier des charges (section 20.4). Dans la récupération des données, ajouter un **filtre personnalisé** (*Custom Code / Custom Query*). Logique à reproduire, avec `:uid` = identifiant de l'utilisateur Joomla connecté :

```sql
SELECT f.*
FROM #__club_fichier AS f
WHERE f.published = 1
  AND f.access IN (/* niveaux d'accès de l'utilisateur */)
  AND (
        f.portee = 'club'

     OR (f.portee = 'groupe' AND f.fichier_groupe_id IN (
           SELECT ac.groupe_id
           FROM #__club_activitechien ac
           JOIN #__club_chienadherent ca ON ca.chien_id = ac.chien_id
           JOIN #__club_adherent a       ON a.id = ca.adherent_id
           WHERE a.user_id = :uid))

     OR (f.portee = 'chien' AND f.fichier_chien_id IN (
           SELECT ca.chien_id
           FROM #__club_chienadherent ca
           JOIN #__club_adherent a ON a.id = ca.adherent_id
           WHERE a.user_id = :uid))

     OR (f.portee = 'adherent' AND f.fichier_adherent_id IN (
           SELECT a.id FROM #__club_adherent a WHERE a.user_id = :uid))

     OR (f.portee = 'moniteurs' AND /* l'utilisateur est dans le groupe Joomla « Moniteurs » */)
  )
ORDER BY f.created DESC
```

> **⚠ à vérifier** — Noms de tables (`#__club_…`, voir 5.4) et colonnes (`published`, `access`, `created` sont des colonnes techniques ajoutées par JCB). Dans le code final, utiliser les requêtes préparées de Joomla (variable liée pour `:uid`), **ne jamais** coller l'identifiant directement dans le texte SQL.

**Version simple pour démarrer** : ne gérer que les portées `club` et `moniteurs`, plus le niveau d'accès Joomla. Les portées groupe / chien / adhérent s'ajoutent ensuite, une par une, en testant chacune avec un compte de démonstration.

### 8.3 Protéger les fichiers eux-mêmes

Un filtre dans la liste ne suffit pas : un fichier rangé dans un dossier public reste téléchargeable par quiconque connaît son adresse.

1. **Dossier privé.** Ranger les fichiers dans un dossier **hors de la racine web** si l'hébergement le permet (par exemple `/home/.../club_prive/`). À défaut, un dossier sous la racine **bloqué** par un fichier `.htaccess` :

   ```apache
   # À placer dans le dossier des fichiers (Apache 2.4)
   Require all denied
   ```

   Avec Nginx, le blocage se fait dans la configuration du serveur, pas par `.htaccess`.
2. **Téléchargement contrôlé.** Le lien « Télécharger » ne pointe **pas** vers le fichier mais vers une action du composant (par exemple `index.php?option=com_club&task=fichier.download&id=12`). Cette action :
   - vérifie que l'utilisateur est **connecté** ;
   - recharge le fichier et **réapplique la règle de visibilité** (8.2) pour cet identifiant : ne jamais se fier à la seule liste affichée ;
   - si autorisé, envoie le fichier ; sinon, répond « accès refusé » (403).
3. **Nom aléatoire.** Renommer le fichier à l'enregistrement (nom aléatoire, conserver le titre d'origine dans la base) pour qu'aucune adresse ne soit devinable.
4. **Limites.** N'accepter que PDF, JPG, PNG, DOCX ; taille maximale 10 Mo ; vérifier le type réel du fichier, pas seulement son extension.
5. **Sauvegardes.** Inclure ce dossier dans la sauvegarde du site (Akeeba Backup) : les fichiers ne sont pas dans la base.

> **⚠ à vérifier** — Dans JCB, cette action de téléchargement s'écrit avec du **code personnalisé** (*Custom Code*) ajouté au contrôleur de la vue du site. Si vous n'êtes pas à l'aise avec le PHP, c'est le bon moment pour envisager la solution C (extension DOCman ou Phoca Download), section 5.5.

### 8.4 Menu et accès

1. Après installation (section 9), créer dans Joomla (**Menus → Main Menu → Nouveau**) un élément de menu « **Mes documents** » pointant vers la vue `fichiers` du composant `club`.
2. Réglage **Niveau d'accès = Registered** (Inscrit) : seuls les utilisateurs connectés voient le menu.
3. Créer les **groupes d'utilisateurs** Joomla (Adhérents, Moniteurs, Gestionnaire vétérinaire, Gestionnaire financier) et leurs niveaux d'accès, comme décrit dans `mode-emploi-demo-production.md`.

---

## 9. Compiler et installer

1. Menu **Component Builder → Compiler**.
2. Sélectionner le composant **Club**.
3. Décocher les éléments facultatifs inutiles.
4. Cliquer sur **Compile**.
5. À la fin de la compilation, cliquer sur **Install** : le composant s'installe directement dans ce Joomla de test.

> **⚠ à vérifier** — La compilation produit aussi un fichier `.zip`. Repérez le lien ou le dossier où JCB le dépose : c'est ce fichier qu'on installera plus tard en production.

---

## 10. Vérifier et saisir les données de démonstration

1. Ouvrir **Composants > Club** : les **huit** vues doivent apparaître.
2. Contrôler dans phpMyAdmin les noms des tables créées (et corriger les requêtes SQL de l'étape 5.4 et de la section 8.2 si elles diffèrent de `#__club_…`).
3. Saisir les données **dans cet ordre**, car les listes déroulantes dépendent des enregistrements existants :

| Ordre | Vue | Exemples (fictifs) |
|---|---|---|
| 1 | Activités | Obéissance, Agilité, Hooper |
| 2 | Niveaux | Chiot, Obéissance 1, Obéissance 2, Loisir, Compétition |
| 3 | Adhérents | DEMO Sandrine, DEMO Dupont Jean, DEMO Dupont Marie (principal : Jean), DEMO Martin Sophie ; **relier un compte Joomla de test à DEMO Sandrine** |
| 4 | Chiens | DEMO SAM, DEMO REX, DEMO NALA |
| 5 | Groupes | Obéissance 2 (mercredi 19:30), Agility Loisir (samedi 10:00) |
| 6 | Chien/adhérent | SAM→Sandrine (principal), SAM→Jean (copropriétaire), REX→Sandrine, NALA→Sophie |
| 7 | Activité/chien | SAM→Obéissance 2 et Agility Loisir, REX→Obéissance 2 |
| 8 | Fichiers | DEMO Règlement (portée : tous les adhérents) ; DEMO Planning O2 (portée : un groupe → Obéissance 2) ; DEMO Certificat SAM (portée : un chien → SAM) |

> **Utiliser uniquement des données et des fichiers fictifs**, préfixés par `DEMO`.

4. **Tester les droits des fichiers** (indispensable) : se connecter avec le compte de test lié à Sandrine, ouvrir « Mes documents » et vérifier qu'elle voit le règlement, le planning O2 (SAM et REX sont en O2) et le certificat de SAM ; puis avec un compte lié à Sophie (NALA seulement), vérifier qu'elle voit **uniquement** le règlement. Enfin, **essayer l'adresse directe d'un fichier** sans être connecté : l'accès doit être refusé.
5. Les droits par groupe (moniteur, vétérinaire, financier) se règlent ensuite dans **Composants > Club > Options > onglet Droits**, comme décrit dans `mode-emploi-demo-production.md`.

---

## 11. Conserver le travail

Le plus précieux n'est pas le `.zip` mais la **définition** du composant dans JCB : en cas de modification, on recompile à partir d'elle.

- Dans **Components**, utiliser la fonction **Export component** de JCB (elle exporte aussi les vues d'administration et les champs liés) et garder le fichier ou la clé générée.
- Mettre dans le dépôt Git : le `.zip` compilé, l'export JCB, ce mode d'emploi et un jeu de données fictives (`demo_data.sql`). **Ne pas mettre les fichiers réels des adhérents dans Git.**

```bash
git add .
git commit -m "Composant club généré avec JCB (phase 1 + base documentaire)"
git push
```

> Si vous recompilez et réinstallez le composant, les tables peuvent être recréées et les données saisies perdues. Avant de désinstaller : dans la vue d'administration, onglet **MySQL**, activer la **sauvegarde de table** (en excluant les champs `created_by`, `modified_by`, `access`, `asset_id`), puis compiler avant de désinstaller. Les fichiers du dossier privé ne sont pas touchés, mais **sauvegardez-les aussi**.

---

## 12. Passer en production

1. Faire une sauvegarde complète du site (Akeeba Backup), **dossier des fichiers privés compris**.
2. Installer **uniquement le `.zip` du composant `club`** (étape 9) sur le site réel. **Ne pas installer JCB en production.**
3. Créer le **dossier privé** des fichiers sur le serveur de production, avec son blocage (section 8.3), et vérifier qu'une adresse directe est refusée.
4. Appliquer le mode d'emploi `mode-emploi-demo-production.md` (groupes, droits, données fictives, nettoyage).
5. Relier chaque adhérent à son compte Joomla (champ `user_id`) avant d'ouvrir « Mes documents ».

---

## 13. Modifier le composant après installation

Selon ce que l'on veut changer, il y a deux façons de faire.

| Changement | Où le faire | Recompiler ? |
|---|---|---|
| Ajouter / renommer / désactiver une **activité** ou un **niveau** | Administration → Composants → Club → Activités / Niveaux | **Non** |
| Ajouter un **adhérent, chien, groupe, fichier** | Administration du composant | **Non** |
| Modifier **qui voit quoi** (portée d'un fichier, niveau d'accès) | Fiche du fichier | **Non** |
| Ajouter une **option** à une liste fixe (nouveau type d'adhésion, nouveau jour, nouveau type de fichier) | JCB → Fields → le champ → ligne `options` | **Oui** |
| Ajouter un **champ** à une vue (adresse, photo, saison…) | JCB → Fields (nouveau champ) puis Admin Views → *Linked Fields* | **Oui** |
| Ajouter une **vue** (vaccination, cotisation, présence, saison) | JCB → Admin Views, puis la relier au composant | **Oui** |
| Changer les droits d'un groupe | Composants → Club → Options → Droits | **Non** |

**Procédure de recompilation sans perte de données :**

1. Sauvegarder le site (base et dossier des fichiers).
2. Dans JCB, faire la modification (champ, vue ou option).
3. **Compiler** puis **Installer** : JCB met à jour la structure.
4. ⚠ à vérifier : avec une mise à jour par-dessus l'installation existante, vos données sont normalement conservées, mais **testez d'abord sur le site de test avec une copie des données**. Si vous devez désinstaller, activez avant la **sauvegarde de table** (section 11).
5. Contrôler que les listes déroulantes et les droits fonctionnent encore.

**Pour que les activités soient vraiment évolutives** : ajouter dès que possible un champ **statut** (actif / inactif) aux vues `activite` et `niveau`, et filtrer les listes déroulantes (requêtes 5.4) pour ne proposer que les éléments actifs. Ainsi, on désactive une activité arrêtée sans perdre l'historique des chiens qui l'ont pratiquée.

---

## 14. Problèmes fréquents

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
| Des données ont disparu après réinstallation | Activer la sauvegarde de table avant de désinstaller (étape 11). |
| Le moniteur voit trop de données | Normal : le filtrage « seulement ses groupes » demande un développement spécifique, hors démonstration. |
| **Le fichier téléversé est téléchargeable sans connexion** | Il est rangé dans un dossier public (cas du champ Media dans `images/`). Le déplacer vers un dossier privé et appliquer la section 8.3. |
| **Le téléversement échoue** | Vérifier `upload_max_filesize` et `post_max_size` (section 2), les droits d'écriture sur le dossier, et la liste des extensions autorisées. |
| **« Mes documents » est vide pour un adhérent** | Vérifier : le champ `user_id` de l'adhérent est renseigné ; le chien est lié à l'adhérent (vue `chienadherent`) et affecté à un groupe (vue `activitechien`) ; le fichier est **publié** et son niveau d'accès correspond à celui de l'utilisateur. |
| **Un adhérent voit les fichiers d'un autre** | La requête de la section 8.2 n'applique pas le filtre par `user_id`, ou deux adhérents partagent le même compte Joomla. Corriger le filtre et vérifier qu'un compte n'est lié qu'à un adhérent. |
| Conflit de nom ou erreur à la compilation de la vue `fichier` | Ne pas utiliser le nom `document` pour la vue (mot réservé par Joomla) ; garder `fichier` / `fichiers`. |