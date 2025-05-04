# MovieParisApp – Application Web de gestion de films (Paris)
<br>

## 🎬 Présentation du projet
**MovieParisApp** est une application web (**Java EE**) qui offre des services REST pour gérer la programmation de films dans les cinémas parisiens, à l’image du site AlloCiné.

<details>
<summary>🛠️ Fonctionnalités principales</summary>

- **Administrateurs (cinémas)** :
  - Se connecter.
  - Ajouter de nouveaux films avec :
    - Titre.
    - Durée.
    - Langue.
    - Sous-titres éventuels.
    - Réalisateur.
    - Acteurs principaux.
    - Âge minimum.
    - Plage de diffusion (dates, jours, horaires).
    - Ville (adresse du cinéma).

- **Utilisateurs publics** :
  - Accès libre (sans login).
  - Liste des films par ville.
  - Consultation du détail de chaque film (synopsis, horaires, cinéma, etc.).

</details>


<details>
<summary>⚙️ Technologies utilisées</summary>

- **Backend** :
  - Java EE.
  - **JAX-RS (Jersey)** pour les services REST.
  - **Jackson** pour la sérialisation JSON.
  - Déploiement sur **Tomcat**.

- **Base de données** :
  - **MySQL**.

- **Frontend** :
  - **JSP/HTML** pour l’interface utilisateur.

- **Build & Déploiement** :
  - **Maven** (packaging WAR).
  - Gestion des dépendances.
  - Préparé pour intégration continue.

</details>


<br>

## 🏗️ Architecture du système
L’architecture suit un modèle **MVC** classique, organisé en trois couches principales :
1. **Interface Web (JSP)** — Vue.
2. **Services REST (JAX-RS / Jersey)** — Contrôleur.
3. **Base de données MySQL + DAO** — Modèle.
<details>
<summary>🖥️ Interface Web (JSP)</summary>

- Les **administrateurs cinéma** interagissent via des pages JSP (*login*, *ajout de film*).
  - Ces pages envoient des requêtes HTTP **synchrones** vers les endpoints REST (exemple : `POST /api/login`).
- Les **utilisateurs publics** accèdent à des JSP (*liste des films*, *détails*).
  - Ces pages déclenchent des appels REST soit **asynchrones (AJAX)**, soit côté serveur (**server-side**), pour récupérer les données.

</details>

<details>
<summary>🔧 Backend REST (JAX-RS via Jersey)</summary>

- Les requêtes HTTP sont interceptées par le **ServletContainer Jersey**.
- Jersey achemine les requêtes vers les classes Java **annotées** (`@Path`, `@GET`, `@POST`, etc.).
- Configuration dans `web.xml` :
  - Déclaration du servlet Jersey.
  - Package à scanner pour les ressources REST.
  - Mapping des URL sur le motif `/api/*`.

</details>

<details>
<summary>🗄️ Accès aux données (DAO / MySQL)</summary>

- Les ressources REST appellent la **couche DAO** :
  - Vérification des identifiants (`UserDAO`).
  - Lecture / écriture de films (`MovieDAO`).
- La DAO effectue les requêtes SQL sur MySQL.
- Les résultats sont renvoyés en JSON (*Jackson*).

</details>

<details>
<summary>🔄 Flux général</summary>

1. Le navigateur charge les pages JSP.
2. Les JSP initient des appels REST.
3. Jersey dirige les requêtes vers les ressources REST.
4. Les DAO accèdent à MySQL.
5. Les réponses JSON sont renvoyées aux JSP.

</details>

<details>
<summary>✅ Résumé du découpage</summary>

| Couche | Rôle |
|--------|------|
| **JSP** | Vue (interface utilisateur) |
| **REST (Jersey)** | Contrôleur |
| **DAO + MySQL** | Modèle |

</details>

<details>
<summary>⚠ Remarque sur les rôles Admin / Public</summary>

- **Admin** dispose des mêmes capacités que Public, avec des privilèges supplémentaires (ajout de films).
- Le diagramme reflète cette hiérarchie : `Admin` hérite de `Public`.
</details>

<details>
<summary>📊 🔍 <strong>🖼️ DIAGRAMME D’ARCHITECTURE — CLIQUEZ POUR AFFICHER</strong> 🔍 📊</summary>
  
![Schéma d’architecture](docs/architecture/architecture_systeme.png)
</details>





<br>

## 📁 Arborescence du projet

Le projet Maven est structuré de manière standard pour une application web Java (**packaging WAR**).

<details>
<summary>📂 Voir l’arborescence</summary>

```plaintext
MovieParisApp/
├── README.md                   # Documentation du projet (présentation, diagrammes, endpoints...)
├── pom.xml                     # Fichier Maven (dépendances Jersey, Jackson, MySQL, JUnit...)
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/movieparisapp/
│   │   │       ├── model/      # Classes métier (modèle de données)
│   │   │       │   ├── Movie.java       # Classe entité Film (POJO)
│   │   │       │   ├── Theatre.java     # Classe entité Cinéma/Salle
│   │   │       │   └── User.java        # Classe entité Utilisateur (admin cinéma)
│   │   │       ├── dao/        # Classes d’accès aux données (JDBC)
│   │   │       │   ├── MovieDAO.java    # DAO pour les films
│   │   │       │   ├── TheatreDAO.java  # DAO pour les salles
│   │   │       │   └── UserDAO.java     # DAO pour les utilisateurs (auth)
│   │   │       └── rest/       # Services REST (JAX-RS Resources)
│   │   │           ├── MovieResource.java   # Endpoints REST /movies
│   │   │           └── AuthResource.java    # Endpoint REST /login
│   │   ├── resources/
│   │   │   ├── db/
│   │   │   │   ├── schema.sql   # Script SQL de création de la base (tables Movie, Theatre, User)
│   │   │   │   └── data.sql     # Script SQL d’insertion de données mock
│   │   │   └── mock/
│   │   │       ├── movies.json   # Données JSON mock des films (pour dev frontend)
│   │   │       ├── theatres.json # Données JSON mock des salles
│   │   │       └── users.json    # Données JSON mock des utilisateurs
│   │   └── webapp/
│   │       ├── WEB-INF/
│   │       │   ├── web.xml      # Descripteur de déploiement (configuration de Jersey, etc.)
│   │       │   └── web.xml      # (Autres configs web, si nécessaires)
│   │       ├── login.jsp        # Page de login admin cinéma
│   │       ├── addMovie.jsp     # Formulaire d’ajout de film
│   │       ├── moviesByCity.jsp # Page publique listant les films d’une ville
│   │       └── movieDetails.jsp # Page publique de détail d’un film
│   └── test/
│       └── java/
│           └── com/movieparisapp/
│               ├── rest/
│               │   └── MovieResourceTest.java   # Tests unitaires des endpoints REST
│               └── dao/
│                   └── MovieDAOTest.java        # Tests unitaires des DAO (ex: MovieDAO)
```
</details>

<details>
<summary>💡 Remarque sur l’organisation des modules</summary>
<br>
L’arborescence est conçue pour que chaque développeur puisse se concentrer sur son module :

- Le développeur **frontend** travaille dans `src/main/webapp` (*JSP + JSON mock*).
- Le développeur **backend REST** travaille dans `src/main/java/com/movieparisapp/rest`.
- Les modules sont isolés pour limiter les interférences.

</details>
