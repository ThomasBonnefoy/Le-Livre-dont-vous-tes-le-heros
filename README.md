# Éditeur / Lecteur « Livre dont vous êtes le héros »

**SAÉ 2.01 — Le livre dont vous êtes le héros**

Application de bureau en **C++ / Qt 6** pour **écrire** un livre-jeu, **vérifier** qu'il tient debout, y **jouer** et le **publier** en site web.

**[Jouer au livre d'exemple *La Caverne Maudite* exporté par l'application](https://thomasbonnefoy.github.io/Le-Livre-dont-vous-tes-le-heros/)**

## Contexte du projet

Ce projet a été réalisé dans le cadre d'une **SAE** (*Situation d'Apprentissage et d'Évaluation*, **2.01 – Le livre dont vous êtes le héros**) du **BUT Informatique**, à l'**IUT Lyon 1 – site de Bourg-en-Bresse**.

La SAÉ est le dispositif pédagogique qui structure les projets du BUT : l'IUT confie aux étudiants un projet conséquent, réalisé en équipe, qui reproduit les conditions d'un travail réel : cahier des charges, gestion de projet, travail collaboratif et livrable évalué. Ces situations d'apprentissage constituent l'un des principaux vecteurs d'évaluation de la formation, en **contrôle continu**.

Le sujet : réaliser une application de bureau complète permettant de créer des livres « dont vous êtes le héros », ces récits où le lecteur choisit la suite de l'histoire à chaque page, en gérant ses points de vie, son expérience et ses objets.

Le projet s'inscrit dans un **cadre strictement pédagogique**, avec un objectif de production concrète : une application fonctionnelle couvrant tout le cycle de vie d'un livre-jeu.

| | |
|---|---|
| Cadre | SAÉ 2.01, BUT Informatique 1ʳᵉ année, IUT Lyon 1 – site de Bourg-en-Bresse |
| Durée | Une semaine, juin 2026 |
| Équipe | 3 personnes |
| Technologies | C++17, Qt 6 (Widgets), CMake, JSON, HTML / CSS, Leaflet |

---

## Présentation du projet

L'application couvre l'ensemble du cycle de vie d'un livre-jeu :

1. **Écrire** : l'auteur rédige ses pages en texte riche et les relie par des choix.
2. **Vérifier** : l'application détecte les pages qu'aucun chemin n'atteint et les impasses oubliées.
3. **Jouer** : un mode lecture fait vivre l'histoire avec points de vie, expérience et inventaire.
4. **Publier** : le livre s'exporte en site web, avec une carte interactive des chapitres.

L'objectif est de proposer un outil complet permettant de passer d'un simple texte à un récit interactif jouable en ligne, sans écrire une ligne de code.

---

## Objectifs pédagogiques

Cette SAÉ mobilise principalement trois compétences du BUT Informatique :

* **Réaliser un développement d'application** : concevoir et coder une application complète en orienté objet, avec une interface graphique Qt Widgets.
* **Optimiser des applications** : choisir les bonnes structures de données et appliquer un algorithme de graphe à un problème réel.
* **Travailler dans une équipe informatique** : se répartir le travail, partager le code avec Git et tenir un suivi des tâches.

---

## Travail réalisé / Fonctionnalités

### Édition
* Éditeur de **texte riche** : gras, italique, souligné, police, couleur, surlignage, alignements.
* Insertion d'**images**, de **listes**, de **liens** et de la date.
* Annuler / rétablir, couper / copier / coller, zoom, affichage du code HTML.
* Gestion **multi-pages** (ajout / suppression / réorganisation des pages).

### Structure du livre
* **Liens entre les pages** qui portent des effets, par exemple :
  `page3.html#pv=-10;objet=Epee` fait perdre 10 PV et donne une épée.
* Effets disponibles : `pv`, `xp`, `objet`, ainsi que les conditions `requiert` (objet possédé) et `requiertpage` (page déjà visitée).
* **Vérification de la cohérence** : détection des pages jamais accessibles (aucun choix n'y mène) et des culs-de-sac (page sans issue qui n'est pas une fin).

### Lecture (mode jeu)
* **Points de vie**, **expérience** et **inventaire** affichés en permanence.
* Détection des fins : victoire, défaite, PV à zéro.
* Boutons **Continuer** et **Retour** (avec restauration de l'état précédent).

### Sauvegarde et export
* Enregistrement / ouverture au format **JSON**, avec **sauvegarde automatique**.
* **Export d'un site web complet** : une page HTML par page du livre + une page d'accueil avec une **carte interactive** (Leaflet).
* Sur le site exporté, l'état du joueur est conservé d'une page à l'autre grâce au **localStorage** du navigateur.
* **Export PDF**, impression et aperçu avant impression.

---

## Architecture et organisation du code

Le modèle (pages, choix, conditions, livre) est **séparé de l'interface** (fenêtres d'édition et de lecture) : chaque classe a un seul rôle, et le modèle peut être testé sans interface.

| Fichier | Rôle |
|---|---|
| `main.cpp` | Point d'entrée de l'application |
| `mainwindow.h / .cpp / .ui` | Fenêtre principale à onglets : Édition, Lecture, Paramètres |
| `fenetrerun.h / .cpp / .ui` | Fenêtre de lecture du livre (mode jeu) |
| `livre.h / .cpp` | Le livre : ensemble des pages, export en site web |
| `page.h / .cpp` | Une page : titre, contenu HTML, type, choix |
| `choix.h / .cpp` | Un choix : page cible, effets et condition |
| `condition.h / .cpp` | Conditions d'accès à un choix |
| `EtatJoueur.h` | PV, XP et objets du joueur |
| `ressources.qrc`, `Icones/` | Icônes de l'interface |
| `Livre/` | Exemple de livre : *La Caverne Maudite* (JSON) |

---

## Fonctionnement de l'application

### Édition
L'auteur saisit ses pages dans un éditeur de texte riche, puis crée les liens entre elles à l'aide du menu **Insertion → Insérer un lien**, en indiquant la page cible (ex. `page2.html`) et éventuellement des effets (`#pv=-5;objet=Epee`).

### Vérification de cohérence
Le menu **Outils → Vérifier la cohérence du livre** parcourt le livre comme un graphe et signale à l'auteur les pages inaccessibles et les culs-de-sac qu'il ne verrait pas à l'œil nu.

### Lecture
L'onglet **Lecture** lance l'aventure à partir de la page de départ, avec les valeurs initiales définies dans l'onglet **Paramètres** (PV, XP, objets de départ). Le lecteur avance de choix en choix ; chaque choix peut modifier l'état du joueur.

### Export web
L'application génère un site autonome : `index.html` (carte des étapes + sommaire) et une page `pageN.html` par page du livre. Le joueur y retrouve PV, XP et inventaire, conservés d'une page à l'autre.

---

## Organisation du travail

Projet mené en groupe de 3 dans le cadre de la SAÉ 2.01.

### Membres du groupe

* [Yann Madry](https://github.com/yann-madry)
* [Tristan Muller](https://github.com/tristanmuller007)
* [Thomas Bonnefoy](https://github.com/ThomasBonnefoy)

### Répartition du travail

| Partie | Responsable |
|---|---|
| Modèle de données, sauvegarde JSON, vérification de cohérence | Yann Madry |
| Éditeur de texte riche, export HTML | Tristan Muller |
| Mode lecture (points de vie, expérience, objets), carte interactive | Thomas Bonnefoy |

Le code a été versionné sur le **GitLab de l'IUT**, avec des réunions d'équipe régulières et une **fiche de suivi** par personne.

### Contribution personnelle

Le projet a fait l'objet d'une contribution personnelle par membre, centrée sur les parties listées ci-dessus. Chacun a également pris en charge une **fiche de suivi individuelle** décrivant ses tâches, le temps passé et ses choix techniques.

---

## Documents

Les documents associés au projet sont disponibles dans le dossier `documents/` :

* `sujet.pdf` : consignes officielles de la SAÉ ;
* `Fiche_Bonnefoy_Thomas.docx` : fiche de suivi individuelle (tâches, temps passé, description technique).

Dossier `docs/` :

* Livre d'exemple *La Caverne Maudite* exporté en site web, [consultable en ligne](https://thomasbonnefoy.github.io/Le-Livre-dont-vous-tes-le-heros/) ou en ouvrant `docs/index.html` dans un navigateur.

---

## Implémentation

### Points techniques

* **Structures de données Qt** : `QMap<int, Page>` pour retrouver une page par son identifiant, `QVector<Choix>` pour les choix d'une page.
* **Parcours en profondeur itératif** : une pile (`QVector` utilisé avec `takeLast()`) et la liste des pages déjà visitées, pour explorer tous les chemins sans tourner en boucle. Les pages jamais atteintes et les pages sans issue sont signalées à l'auteur.
* **Sérialisation JSON** : objets imbriqués (`QJsonObject`, `QJsonArray`) et conversion d'un type énuméré (`enum class`) en donnée brute.
* **Export web** : génération d'une page HTML par chapitre, en UTF-8 via `QFile` et `QTextStream`, et d'une carte interactive avec la bibliothèque Leaflet.

### Structure du dépôt

```text
.
├── README.md
├── LICENSE
├── code/                     Projet Qt (CMake)
│   ├── CMakeLists.txt
│   ├── main.cpp              Point d'entrée
│   ├── mainwindow.*          Fenêtre d'édition
│   ├── fenetrerun.*          Fenêtre de lecture
│   ├── livre.* page.*        Modèle : livre et pages
│   ├── choix.* condition.*   Modèle : choix et conditions
│   ├── EtatJoueur.h          État du lecteur (PV, XP, objets)
│   ├── Icones/               Icônes de l'interface
│   └── Livre/                Livre d'exemple au format JSON
├── docs/                     Livre d'exemple exporté en site web
└── documents/                Consignes et fiche de suivi
```

### Compiler et lancer

Prérequis : **Qt 6** (modules Widgets et PrintSupport), **CMake 3.16** ou plus, un compilateur **C++17**.

```bash
cmake -S code -B build
cmake --build build
```

On peut aussi ouvrir `code/CMakeLists.txt` dans **Qt Creator** et compiler directement. Pour essayer l'application : lancer le programme, puis **Fichier → Ouvrir** et choisir le livre d'exemple `code/Livre/La_Caverne_Maudite.json`.

---

## Suite du projet

Plusieurs axes d'amélioration ont été identifiés :

* éditeur visuel du graphe des pages (glisser-déposer) pour voir la structure de l'histoire d'un coup d'œil ;
* thèmes graphiques supplémentaires pour l'export web ;
* tests unitaires du modèle (livre, choix, conditions) ;
* versions prêtes à l'emploi pour Windows et macOS.

Ces éléments constituent des **pistes d'évolution** plutôt que des manques bloquants.

---

## Licence

Ce projet est sous licence [Creative Commons BY-NC 4.0](LICENSE) : vous pouvez le réutiliser et l'adapter en citant les auteurs, mais pas à des fins commerciales.
