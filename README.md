# MovieParisApp – Application Web de gestion de films (Paris)

## Présentation du projet

**MovieParisApp** est une application web (Java EE) qui offre des services REST pour gérer la programmation de films dans les cinémas parisiens, à l’image du site AlloCiné. 

Le projet permet aux cinémas (admins) de se connecter et d’ajouter de nouveaux films avec tous leurs détails (titre, durée, langue, sous-titres éventuels, réalisateur, acteurs principaux, âge minimum) ainsi que la plage de diffusion (dates de début/fin, jours de la semaine et horaire des séances). Ces informations incluent également la ville où le film est projeté (via l’adresse du cinéma).

Côté utilisateur public, l’application offre une page en libre accès (sans login) pour lister les films par ville donnée et consulter le détail de chaque film (synopsis complet, horaires, cinéma, etc.).

Le projet est développé en Java avec le framework **JAX-RS** (implémentation Jersey) pour les services REST et la bibliothèque **Jackson** pour la gestion du format JSON, le tout déployé sur un conteneur **Tomcat**. Les données sont persistées dans une base **MySQL** (possibilité d’utiliser HSQLDB en mémoire selon les besoins).

L’application suit une architecture **MVC** avec un frontend **JSP/HTML** pour l’interface utilisateur et un backend RESTful pour la logique métier, ce qui permet de bien séparer la présentation du traitement. Le build utilise **Maven** (packaging WAR) afin de faciliter l’intégration continue et la gestion des dépendances.

## 🏗️ Architecture du système

L’architecture suit un modèle **MVC** classique, organisé en trois couches principales :
1. **Interface Web (JSP)** — Vue.
2. **Services REST (JAX-RS / Jersey)** — Contrôleur.
3. **Base de données MySQL + DAO** — Modèle.

---

### 🖥️ Interface Web (JSP)

- Les **administrateurs cinéma** interagissent via des pages JSP (*login*, *ajout de film*).
  - Ces pages envoient des requêtes HTTP **synchrones** vers les endpoints REST (exemple : `POST /api/login`).
- Les **utilisateurs publics** accèdent à des JSP (*liste des films*, *détails*).
  - Ces pages déclenchent des appels REST soit **asynchrones (AJAX)**, soit côté serveur (**server-side**), pour récupérer les données.

---

### 🔧 Backend REST (JAX-RS via Jersey)

- Les requêtes HTTP sont interceptées par le **ServletContainer Jersey**.
- Jersey achemine les requêtes vers les classes Java **annotées** (`@Path`, `@GET`, `@POST`, etc.).
- La configuration se fait dans `web.xml` :
  - Déclaration du servlet Jersey.
  - Spécification du package à scanner pour les ressources REST.
  - Mapping des URL sur le motif `/api/*` (exemple : `POST /api/movies`).

---

### 🗄️ Accès aux données (DAO / MySQL)

- Les ressources REST appellent la **couche DAO** :
  - Vérification des identifiants (`UserDAO`).
  - Lecture / écriture de films (`MovieDAO`).
- La DAO effectue les requêtes SQL sur la base **MySQL**.
- Les résultats sont renvoyés aux ressources REST, puis **sérialisés en JSON** (via *Jackson*).

---

### 🔄 Flux général

Le schéma d’architecture (voir ci-dessus) illustre le flux :

1. Le navigateur charge les pages JSP.
2. Les JSP initient des appels REST (synchrones ou AJAX).
3. Jersey dirige les requêtes vers les ressources REST.
4. Les DAO accèdent à la base MySQL.
5. Les réponses JSON sont renvoyées aux JSP, puis au navigateur.

---

### ✅ Résumé du découpage

| Couche | Rôle |
|--------|------|
| **JSP** | Vue (interface utilisateur) |
| **REST (Jersey)** | Contrôleur (logique métier, traitement des requêtes) |
| **DAO + MySQL** | Modèle (accès et gestion des données persistantes) |

---

### ⚠ Remarque sur les rôles Admin / Public

- Les **administrateurs (Admin)** disposent des mêmes capacités que les utilisateurs publics **avec des privilèges supplémentaires** (par exemple l’ajout de films).
- Le diagramme reflète cette hiérarchie : `Admin` hérite des actions `Public`.

---

### 📝 Remarque finale

Ce découpage suit les **bonnes pratiques MVC web Java EE** et reste facilement extensible.

---

## 🖼️ Diagramme d’architecture
![Schéma d’architecture](docs/architecture/architecture_systeme.png)
