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


<br>

## 📚 Diagramme de classes (modèle Java)

Le diagramme de classes UML ci-dessous présente les principales classes Java du projet et leurs relations. (!!!! la note n'est plus valable pour la classe user nécessairement un admin car n'importe qui peux accéder a la page decatalogue pas besoin de connexion).
![Schéma du Diagramme de Classe](docs/diagramme_de_classe/diagramme_de_classe.png)

<details>
  
<summary>📝 Description du modèle</summary>

Dans ce modèle :

- La classe **Movie** est liée à un **Theatre** (chaque film est projeté dans une salle donnée).
- Chaque **User** possède un attribut `role` qui détermine s’il s’agit d’un **utilisateur public** (lecture seule) ou d’un **administrateur** (peut gérer des cinémas et des films).
  - Les administrateurs (`role = admin`) peuvent être associés à plusieurs cinémas qu’ils gèrent.
- Les classes **DAO** (Data Access Object) sont définies par des interfaces (`MovieDAOInterface`, `TheatreDAOInterface`, `UserDAOInterface`) et implémentées par des classes concrètes.
  - Elles fournissent des méthodes pour interagir avec la base de données (par exemple : récupérer les films par ville, ajouter un film).
- Les classes **Resource** (**MovieResource**, **AuthResource**) exposent les endpoints REST et utilisent les DAO pour la logique métier.
  - Par exemple, `MovieResource.getMovies(city)` appelle `MovieDAO.getMoviesByCity(city)` et renvoie la liste des films au format JSON.
  
Le diagramme respecte la séparation des responsabilités :
- **Modèle (données)** : `Movie`, `Theatre`, `User`.
- **Accès aux données** : DAO (interfaces + implémentations).
- **Exposition REST** : Resources.
</details>

<br>

## 🔗 API REST – Endpoints et formats JSON

Le backend expose quatre endpoints principaux via l’API REST (chemin de contexte `/api/` sur Tomcat).
Toutes les réponses JSON sont produites automatiquement par Jackson à partir des objets Java (POJOs) retournés par les méthodes JAX-RS. Grâce à l’intégration de Jackson avec Jersey, il suffit d’inclure la dépendance appropriée pour que les entités soient sérialisées/désérialisées en JSON. Les méthodes renvoient généralement un objet `Response` JAX-RS ou directement le POJO, Jersey se chargeant d’appliquer la conversion JSON. 

<details>
<summary>🔑 <strong>POST /login – Authentification</strong></summary>

**Description** : Authentification basique d’un cinéma (admin).

**Requête JSON** : JSON contenant les identifiants d’un utilisateur admin de cinéma, par ex:

```json
{ "username": "ugc_admin", "password": "secret" }
```
**Traitement** : vérifie dans la base si un utilisateur avec ce login/mot de passe existe. Si oui, la réponse contient un indicateur de succès (et éventuellement un token de session simple).

**Réponse** : code 200 OK avec JSON, par ex. succès :
```json
{ "success": true, "userId": 1, "message": "Login successful" }
```
(En cas d’échec : `{"success": false, "message": "Invalid credentials"}` et code 401 Unauthorized).
</details>

<details>
<summary>🎞 <strong>POST /movies – Ajout d’un film  (par un admin authentifié)</strong></summary>

**Requête JSON** : JSON représentant le film à ajouter. Ce JSON comprend les métadonnées du film ainsi que la salle/horaire. Par exemple :

```json
{
  "title": "Inception",
  "duration": 148,
  "language": "Anglais (VO sous-titré FR)",
  "director": "Christopher Nolan",
  "mainActors": ["Leonardo DiCaprio", "Ellen Page"],
  "minAge": 12,
  "startDate": "2010-07-16",
  "endDate": "2010-09-30",
  "days": "Lundi,Mercredi,Vendredi",
  "time": "20:00",
  "theatreId": 1
}
```
(Ici `theatreId` identifie la salle où le film sera joué. Dans une implémentation réelle, on associerait le film au cinéma de l’utilisateur authentifié automatiquement, plutôt que de l’envoyer dans le JSON.)

**Traitement** :  crée un nouveau film dans la base de données (après avoir éventuellement vérifié l’authentification de l’appelant). Le DAO insère le film et retourne son ID généré.

**Réponse** : code 201 Created si succès, avec éventuellement le film créé en JSON (incluant son `id` attribué). Par ex.:
```json
{ "success": true, "userId": 1, "message": "Login successful" }
```
</details>


<details> 
  <summary>🎥 <strong>GET /movies?city={ville} – Liste des films par ville</strong></summary>

  **Requête** : paramètre de requête `city` dans l’URL, par ex : `/movies?city=Paris` (Aucune authentification requise).
  
  **Traitement** : interroge la base via `MovieDAO.getMoviesByCity(ville)` pour obtenir tous les films dont la salle est dans la ville spécifiée.
  
  **Réponse** : code 200 OK avec un tableau JSON listant les films. Chaque film inclut ses informations principales. On peut choisir de ne pas tout exposer (par ex. ne pas inclure les horaires détaillés dans la liste), mais pour simplicité on renvoie la structure complète. Exemple de réponse :
  
 ```json
[
  {
    "id": 5,
    "title": "Inception",
    "duration": 148,
    "language": "Anglais (VO sous-titré FR)",
    "director": "Christopher Nolan",
    "mainActors": ["Leonardo DiCaprio", "Elliot Page"],
    "minAge": 12,
    "startDate": "2010-07-16",
    "endDate": "2010-09-30",
    "days": "Lundi,Mercredi,Vendredi",
    "time": "20:00",
    "theatre": {
      "id": 1,
      "name": "UGC Ciné Cité Les Halles",
      "city": "Paris",
      "address": "5 rue du Cinéma, 75001 Paris"
    }
  },
  {
    "id": 6,
    "title": "Titanic",
    "...": "..."
  }
]
``` 

Ici deux films sont retournés pour la ville Paris. L’objet `theatre` imbriqué donne des informations sur le cinéma (on aurait pu n’envoyer que le `theatreId`, mais on affiche le nom et l’adresse pour éviter un aller-retour supplémentaire côté client).
</details>


<details> 
<summary>🎞️ <strong>GET /movies/{id} – Détail d’un film  (accès public)</strong></summary>

**Requête** : l’ID du film dans l’URL (par ex. /movies/5).

**Traitement** : utilise MovieDAO.getMovie(id) pour obtenir le film correspondant (et éventuellement ses informations associées comme la salle).

**Réponse** : code 200 OK avec un objet JSON représentant le film détaillé. Exemple :
```json
{
  "id": 5,
  "title": "Inception",
  "duration": 148,
  "language": "Anglais (VO sous-titré FR)",
  "director": "Christopher Nolan",
  "mainActors": ["Leonardo DiCaprio", "Elliot Page", "Tom Hardy"],
  "minAge": 12,
  "startDate": "2010-07-16",
  "endDate": "2010-09-30",
  "days": "Lundi,Mercredi,Vendredi",
  "time": "20:00",
  "theatre": {
    "id": 1,
    "name": "UGC Ciné Cité Les Halles",
    "city": "Paris",
    "address": "5 rue du Cinéma, 75001 Paris"
  }
}
```
Ce JSON contient tous les détails que l’admin avait fournis lors de l’ajout du film, y compris les informations de salle. Il correspond à ce qui serait affiché sur la page de détail publique.
</details>

<details>
<summary><strong>Exemple d’implémentation — CLIQUEZ POUR AFFICHER</strong> 🔍 📊</summary>

  Le fichier `MovieResource.java` définit les endpoints REST relatifs aux films :
  
  Dans cet exemple simplifié, `MovieDAO` est appelé directement. En pratique, on pourrait ajouter des contrôles d’authentification (par ex., vérifier qu’un utilisateur est bien connecté avant d’accepter l’ajout d’un film). De plus, pour la création de film, le DAO pourrait définir l’ID du film inséré dans l’objet `newMovie` (d’où le `newMovie.getId()` après insertion réussie).
  
```java
@Path("/movies")
@Produces(MediaType.APPLICATION_JSON)
public class MovieResource {

    private MovieDAO movieDao = new MovieDAO();

    @GET
    public Response getMoviesByCity(@QueryParam("city") String city) {
        List<Movie> movies;
        if (city != null && !city.isEmpty()) {
            movies = movieDao.getMoviesByCity(city);
        } else {
            movies = new ArrayList<>();
        }
        return Response.ok(movies).build();
    }

    @GET
    @Path("/{id}")
    public Response getMovieById(@PathParam("id") int id) {
        Movie movie = movieDao.getMovie(id);
        if (movie != null) {
            return Response.ok(movie).build();
        } else {
            return Response.status(Response.Status.NOT_FOUND)
                           .entity("{\"error\":\"Movie not found\"}")
                           .build();
        }
    }

    @POST
    @Consumes(MediaType.APPLICATION_JSON)
    public Response addMovie(Movie newMovie) {
        boolean created = movieDao.addMovie(newMovie);
        if (created) {
            // Retourner l'URI du nouveau film créé, par exemple
            URI uri = URI.create("/movies/" + newMovie.getId());
            return Response.created(uri).entity(newMovie).build();
        } else {
            return Response.status(Response.Status.BAD_REQUEST)
                           .entity("{\"error\":\"Cannot create movie\"}")
                           .build();
        }
    }
}
```

De même, le fichier `AuthResource.java` gère le endpoint `/login` :
Ici, pour simplifier, on renvoie juste un indicateur de succès et l’ID utilisateur en cas de login réussi. Une implémentation plus poussée pourrait créer une session HTTP ou retourner un token JWT que le client devra utiliser lors des appels suivants. Cependant, pour ce projet éducatif, une authentification stateful basique suffit.

```java
@Path("/login")
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
public class AuthResource {

    private UserDAO userDao = new UserDAO();

    @POST
    public Response login(Map<String, String> credentials) {
        String username = credentials.get("username");
        String password = credentials.get("password");
        User user = userDao.authenticate(username, password);
        if (user != null) {
            // On pourrait générer un token ou créer une session
            return Response.ok("{\"success\":true, \"userId\":"+user.getId()+"}").build();
        } else {
            return Response.status(Response.Status.UNAUTHORIZED)
                           .entity("{\"success\":false, \"message\":\"Invalid credentials\"}")
                           .build();
        }
    }
}
```
</details>

<br>

## 🗄️ Base de données (MySQL)

Le schéma relationnel comprend trois tables : **Movie** (Film), **Theatre** (Cinéma/Salle) et **User** (Utilisateur admin).
Ci-dessous le script SQL de création de ces tables, avec les contraintes clés primaires/étrangères appropriées :


<details>
<summary>📝 <strong>Script de création (schema.sql)</strong></summary>
<br>
  
  **Description des tables** :

- **Theatre** : stocke les salles de cinéma (nom, ville, adresse).

- **User** : comptes admin avec login, mot de passe, et référence vers la salle administrée (`theatre_id`).
Remarque : un utilisateur est associé à un seul cinéma.

- **Movie** : films avec leurs métadonnées.
`mainActors` est une chaîne de caractères (pas de table séparée pour les acteurs).
Les champs `startDate`, `endDate`, `days` et `time` décrivent la période de diffusion et les horaires.
`theatre_id` relie chaque film à une salle spécifique.

```sql
-- schema.sql : Création des tables

CREATE TABLE Theatre (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    city VARCHAR(50) NOT NULL,
    address VARCHAR(150)
);

CREATE TABLE User (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL UNIQUE,
    password VARCHAR(100) NOT NULL,
    theatre_id INT,
    FOREIGN KEY (theatre_id) REFERENCES Theatre(id)
);

CREATE TABLE Movie (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(100) NOT NULL,
    duration INT,                -- en minutes
    language VARCHAR(50),
    director VARCHAR(100),
    mainActors VARCHAR(255),     -- liste d'acteurs sous forme de texte
    minAge INT,
    startDate DATE,
    endDate DATE,
    days VARCHAR(50),            -- jours de projection
    time TIME,                   -- heure de la séance
    theatre_id INT NOT NULL,
    FOREIGN KEY (theatre_id) REFERENCES Theatre(id)
);
```
</details>

<details> 
  <summary>📊 <strong>Jeu de données initial (data.sql)</strong></summary>

Explication des données :

- Deux cinémas :
  - **UGC Ciné Cité Les Halles** (Paris, id=1).
  - **Pathé Carré Sénart** (Lieusaint, id=2).

- Deux utilisateurs admins pour chacun.

- Trois films insérés :
  - *Inception* et *Titanic* à Paris.
  - *Interstellar* à Lieusaint.

- Ces données permettent de tester le filtrage par ville avec `GET /movies?city=....`

```sql
-- data.sql : Données initiales (mock)

-- Théâtres
INSERT INTO Theatre (id, name, city, address) VALUES
(1, 'UGC Ciné Cité Les Halles', 'Paris', '5 rue du Cinéma, 75001 Paris'),
(2, 'Pathé Carré Sénart', 'Lieusaint', 'Centre Commercial Carré Sénart 77127 Lieusaint');

-- Utilisateurs (admins cinéma)
INSERT INTO User (id, username, password, theatre_id) VALUES
(1, 'ugc_admin', 'secret', 1),
(2, 'pathe_admin', 'secret', 2);
-- NB : mots de passe en clair pour l’exemple (à chiffrer en pratique).

-- Films (un film dans plusieurs théatre ????§§§§§!!!!!)
INSERT INTO Movie (id, title, duration, language, director, mainActors, minAge,
                   startDate, endDate, days, time, theatre_id)
VALUES
(1, 'Inception', 148, 'Anglais (VO st FR)', 'Christopher Nolan',
     'Leonardo DiCaprio, Ellen Page, Tom Hardy', 12,
     '2010-07-16', '2010-09-30', 'Lundi,Mercredi,Vendredi', '20:00', 1),

(2, 'Titanic', 195, 'Anglais (VO st FR)', 'James Cameron',
     'Leonardo DiCaprio, Kate Winslet', 10,
     '1998-01-07', '1998-04-30', 'Mardi,Jeudi,Samedi', '21:00', 1),

(3, 'Interstellar', 169, 'Anglais (VO st FR)', 'Christopher Nolan',
     'Matthew McConaughey, Anne Hathaway', 10,
     '2014-11-05', '2015-01-15', 'Vendredi,Samedi,Dimanche', '18:30', 2);
```
</details>



<br>

## 🖥️ Frontend JSP/HTML

Le frontend consiste en pages JSP servant d’interface utilisateur web. Ces pages permettent aux différents utilisateurs d’interagir avec l’application :

<details>
<summary>🔐 <strong>login.jsp</strong></summary>

- `login.jsp` – Cette page présente un formulaire de connexion pour les administrateurs de cinéma. Elle comporte des champs username et password, et envoie les informations en POST vers l’endpoint `/api/login` (via le formulaire HTML action="/MovieParisApp/api/login"). Si la réponse indique un succès, l’utilisateur est redirigé vers la page d’ajout de film (`addMovie.jsp`). En cas d’échec, un message d’erreur s’affiche. Le code simplifié de `login.jsp` pourrait être :

  ```jsp
  <%-- login.jsp --%>
  <h2>Connexion Administrateur Cinéma</h2>
  <form method="POST" action="api/login">
    <label>Utilisateur: <input type="text" name="username"/></label><br/>
    <label>Mot de passe: <input type="password" name="password"/></label><br/>
    <button type="submit">Se connecter</button>
  </form>
  
  <%-- Afficher un message d'erreur si fourni --%>
  <c:if test="${not empty param.error}">
    <p style="color:red;">${param.error}</p>
  </c:if>
  ```

**Remarque** :  On peut utiliser JSTL (`<c:if>`) pour afficher un message d’erreur passé en paramètre après une redirection. L’appel REST `/api/login` peut être traité soit via un fetch AJAX, soit en soumettant directement le formulaire à l’URL REST. Ici, pour simplicité, on envisage que Tomcat transfère la requête `/api/login` vers le servlette Jersey, puis que la réponse JSON soit interprétée par la page (éventuellement via JavaScript). Alternativement, on pourrait faire traiter la soumission par un contrôleur qui appelle le REST en interne et redirige en fonction du résultat.
</details>

<details>
<summary>➕ <strong>addMovie.jsp</strong></summary>

 - `addMovie.jsp` – Cette page, accessible seulement après une connexion réussie, fournit un formulaire pour ajouter un film. Les champs correspondent à tous les détails du film (titre, durée, langue, réalisateur, acteurs, âge min, dates début/fin, jours, horaire). L’admin n’a normalement pas besoin de choisir la salle puisque c’est son cinéma par défaut ; on pourrait donc pré-remplir ou cacher ce champ. La soumission du formulaire se fait en POST vers `/api/movies` . Par exemple, un extrait du formulaire JSP :

    ```jsp
    <%-- addMovie.jsp (extrait) --%>
    <h2>Ajouter un nouveau film</h2>
    <form method="POST" action="api/movies">
      <label>Titre: <input type="text" name="title"/></label><br/>
      <label>Durée (min): <input type="number" name="duration"/></label><br/>
      <label>Langue: <input type="text" name="language"/></label><br/>
      <!-- ... autres champs ... -->
      <label>Jours (ex: Lun,Mer,Ven): <input type="text" name="days"/></label><br/>
      <label>Heure: <input type="time" name="time"/></label><br/>
      <!-- Id du cinéma de l'admin (caché ou selection) -->
      <input type="hidden" name="theatreId" value="${session.theatreId}"/>
      <button type="submit">Enregistrer le film</button>
    </form>
    ```
**Remarque** : Après l’ajout, on pourrait simplement afficher un message de confirmation ou vider le formulaire. (Dans une version aboutie, on gérerait la réponse JSON pour confirmer que le film a été créé, éventuellement en affichant son ID ou en ajoutant la liste.)

</details>


<details>
<summary>🎥 <strong>moviesByCity.jsp</strong></summary>

- `moviesByCity.jsp` – Page publique pour consulter les films par ville. Cette page propose par exemple une liste déroulante des villes disponibles ou un champ de recherche. L’utilisateur sélectionne une ville puis déclenche l’affichage des films correspondants. L’implémentation peut se faire de deux façons :
  - **1.  Via AJAX (client-side) :** a page charge initialement la structure de base (en HTML) et un petit script JavaScript va appeler l’API REST `/api/movies?city=...` en asynchrone (par exemple avec `fetch` ou AJAX) pour récupérer la liste en JSON, puis générer dynamiquement la liste des films dans la page.
  - **2. Via server-side (JSP) :** La page JSP elle-même fait l’appel et rend la liste. Par exemple, on pourrait passer la ville en paramètre de requête à `moviesByCity.jsp` (ex: `moviesByCity.jsp?city=Paris`), et dans le JSP utiliser la couche DAO ou un appel interne à l’API pour obtenir la liste et la parcourir avec JSTL pour afficher un tableau HTML.

  Pour favoriser l’indépendance frontend/backend, l’approche AJAX est recommandée. Par exemple, `moviesByCity.jsp` pourrait inclure un script :
  
    ```jsp
    <select id="citySelect">
        <option value="Paris">Paris</option>
        <option value="Lieusaint">Lieusaint</option>
    </select>
    <div id="moviesList"></div>
    
    <script>
        function loadMovies(city) {
            fetch('api/movies?city=' + encodeURIComponent(city))
                .then(resp => {
                    if (!resp.ok) throw new Error("Erreur lors de la récupération des films.");
                    return resp.json();
                })
                .then(data => {
                    const listDiv = document.getElementById('moviesList');
                    listDiv.innerHTML = "";
                    if (data.length === 0) {
                        listDiv.innerHTML = "<p>Aucun film trouvé pour cette ville.</p>";
                    } else {
                        data.forEach(movie => {
                            const link = `<a href="movieDetails.jsp?id=${movie.id}">${movie.title}</a>`;
                            listDiv.innerHTML += `<p>${link} – ${movie.theatre.name}</p>`;
                        });
                    }
                })
                .catch(error => {
                    document.getElementById('moviesList').innerHTML = 
                        `<p style='color:red;'>${error.message}</p>`;
                });
        }
    
        document.getElementById('citySelect').addEventListener('change', function() {
            loadMovies(this.value);
        });
    
        // Charger les films de la ville par défaut au démarrage
        window.addEventListener('load', function() {
            loadMovies(document.getElementById('citySelect').value);
        });
    </script>
    ```
Dans cet exemple, lorsque la ville change, on récupère la liste JSON des films et on insère un lien par film qui mène à la page de détails correspondante. On affiche aussi le nom du cinéma (fourni dans `movie.theatre.name`).

</details>


<details>
<summary>📝 <strong>movieDetails.jsp</strong></summary>

- `movieDetails.jsp` – Page de détails d’un film. Elle affiche toutes les informations sur un film sélectionné. En suivant l’approche AJAX, cette page peut, au chargement, lire le paramètre id dans l’URL (par ex. movieDetails.jsp?id=5) et effectuer un fetch('api/movies/5') pour obtenir le JSON du film, puis remplir le contenu de la page (titre, description, acteurs, horaires, etc.) dynamiquement. 
    
    ```jsp
    <%-- movieDetails.jsp --%>
    <!DOCTYPE html>
    <html>
    <head>
        <title>Détails du film</title>
        <script>
            document.addEventListener("DOMContentLoaded", function() {
                const params = new URLSearchParams(window.location.search);
                const id = params.get("id");
                if (!id) {
                    document.getElementById("movieDetails").innerText = "Aucun ID de film fourni.";
                    return;
                }
    
                fetch("api/movies/" + id)
                    .then(response => {
                        if (!response.ok) {
                            throw new Error("Film introuvable.");
                        }
                        return response.json();
                    })
                    .then(movie => {
                        document.getElementById("title").innerText = movie.title;
                        document.getElementById("director").innerText = movie.director;
                        document.getElementById("cinema").innerText = movie.theatre.name + " (" + movie.theatre.address + ")";
                        document.getElementById("horaire").innerText = movie.days + " à " + movie.time;
                    })
                    .catch(error => {
                        document.getElementById("movieDetails").innerText = error.message;
                    });
            });
        </script>
    </head>
    <body>
        <h2>Détails du film</h2>
        <div id="movieDetails">
            <p><b>Titre :</b> <span id="title"></span></p>
            <p><b>Réalisateur :</b> <span id="director"></span></p>
            <p><b>Cinéma :</b> <span id="cinema"></span></p>
            <p><b>Horaires :</b> <span id="horaire"></span></p>
        </div>
    </body>
    </html>
    ```
Utiliser AJAX / Fetch API dans le JSP pour appeler l’API REST et remplir dynamiquement la page avec JavaScript.

Avantages :
- **Découplage total** → Le frontend (HTML/JS) ne dépend plus de l’implémentation backend.
- **Réutilisable** → Ton API peut être consommée aussi par des applis mobiles ou autres clients.
- **Évolutif** → Si vous migrez le frontend vers Angular/React/Vue plus tard, tu ne changes pas l’API.
</details>





<br>

## 🧪 Mocks JSON pour le frontend

Dans `src/main/resources/mock/`, trois fichiers JSON fournissent des données fictives : 
- **`movies.json`**
- **`theatres.json`**
- **`users.json`**

Ces fichiers représentent respectivement la liste des films, des salles et des utilisateurs, dans le même format que ce que renverrait l’API REST. L’objectif est de permettre au développeur frontend de travailler en parallèle en simulant des réponses d’API. Par exemple, il peut charger `movies.json` via JavaScript au lieu d’appeler réellement `/api/movies`, tant que le backend n’est pas prêt.



<details>
<summary>🎞 <strong>movies.json</strong></summary>

**Remarque** : Le format de `movies.json` est proche de la réponse JSON réelle de GET `/movies?city=...`, à la différence qu’ici chaque film référence la salle par son `theatreId` au lieu d’embarquer l’objet complet. On peut utiliser `theatres.json` pour faire le lien. 
  
Extrait de **`movies.json`** (nos 3 films d’exemple) :

```json
[
  {
    "id": 1,
    "title": "Inception",
    "duration": 148,
    "language": "Anglais (VO st FR)",
    "director": "Christopher Nolan",
    "mainActors": ["Leonardo DiCaprio", "Ellen Page", "Tom Hardy"],
    "minAge": 12,
    "startDate": "2010-07-16",
    "endDate": "2010-09-30",
    "days": "Lundi,Mercredi,Vendredi",
    "time": "20:00",
    "theatreId": 1
  },
  {
    "id": 2,
    "title": "Titanic",
    "duration": 195,
    "language": "Anglais (VO st FR)",
    "director": "James Cameron",
    "mainActors": ["Leonardo DiCaprio", "Kate Winslet"],
    "minAge": 10,
    "startDate": "1998-01-07",
    "endDate": "1998-04-30",
    "days": "Mardi,Jeudi,Samedi",
    "time": "21:00",
    "theatreId": 1
  },
  {
    "id": 3,
    "title": "Interstellar",
    "duration": 169,
    "language": "Anglais (VO st FR)",
    "director": "Christopher Nolan",
    "mainActors": ["Matthew McConaughey", "Anne Hathaway"],
    "minAge": 10,
    "startDate": "2014-11-05",
    "endDate": "2015-01-15",
    "days": "Vendredi,Samedi,Dimanche",
    "time": "18:30",
    "theatreId": 2
  }
]
```
</details> 

<details> 
  <summary>🏢 <strong>theatres.json</strong></summary>
  
Extrait de **`theatres.json`** :

```json
[
  { "id": 1, "name": "UGC Ciné Cité Les Halles", "city": "Paris", "address": "5 rue du Cinéma, 75001 Paris" },
  { "id": 2, "name": "Pathé Carré Sénart", "city": "Lieusaint", "address": "Centre Com. Carré Sénart 77127" }
]
```
</details>


<details> 
  <summary>👤 <strong>users.json</strong></summary>

**Remarque** : Ces données permettent de simuler l’authentification sans requêtes réelles vers la base de données. Le développeur frontend peut ainsi, par exemple, charger *movies.json* et *theatres.json* ensemble pour afficher les films par ville, ou vérifier le login en comparant l’entrée dans *users.json*. Bien sûr, ces mocks doivent rester cohérents avec ce que renverra réellement l’API REST afin de faciliter l’intégration finale.

Extrait de **`users.json`** :

```json
[
  { "id": 1, "username": "ugc_admin", "password": "secret", "theatreId": 1 },
  { "id": 2, "username": "pathe_admin", "password": "secret", "theatreId": 2 }
]
```
</details>




<br>



## ✅ Tests unitaires (JUnit)

Le projet inclut des tests unitaires pour assurer le bon fonctionnement du backend, notamment des services REST et des DAO. Ces tests sont écrits avec JUnit 5 (Jupiter). Chaque développeur peut ainsi vérifier sa partie en isolé :

- ✅ Tests DAO → Validité des opérations CRUD sur la base.
- ✅ Tests REST → Fonctionnement des endpoints HTTP.
- 🔄 Chaque test part d’un état propre (base H2 en mémoire, ou mocks).
- 🔎 Les cas normaux et les erreurs sont couverts (exemple : GET inexistant retourne 404, POST invalide retourne 400).


<details>
<summary>🗄 <strong>Tests DAO</strong></summary>

**Objectif** : Vérifier les opérations de base de données.

**Exemple** : `MovieDAOTest` insère un film factice dans une base de test (par exemple H2 en mémoire), puis vérifie les méthodes `getMoviesByCity`, `getMovie` et `addMovie`.

On utilise le script `schema.sql` pour créer la base, et chaque test travaille dans une transaction pour rétablir l’état initial après exécution.

**Remarque** : Chaque test commence avec une base vide (on efface les films ajoutés à la fin).
Le comportement du DAO est ainsi testé de manière fiable.

**Extrait de test pour MovieDAO** :

```java
class MovieDAOTest {
    private static MovieDAO dao;

    @BeforeAll
    static void initDatabase() {
        dao = new MovieDAO();
        dao.setConnection(TestDB.getConnection()); 
        TestDB.executeSqlScript("schema.sql");
    }

    @AfterEach
    void cleanData() {
        TestDB.executeSql("DELETE FROM Movie");
    }

    @Test
    void testAddAndGetMovie() {
        Movie m = new Movie("TestFilm", 120, "Français", "Test Réal", "Acteur1,Acteur2",
                             0, Date.valueOf("2025-01-01"), Date.valueOf("2025-01-31"),
                             "Lundi,Mardi", "20:00", new Theatre(1));
        boolean ok = dao.addMovie(m);
        assertTrue(ok);
        assertNotNull(m.getId());
        Movie fetched = dao.getMovie(m.getId());
        assertEquals("TestFilm", fetched.getTitle());
    }

    @Test
    void testGetMoviesByCity() {
        Movie m1 = new Movie("FilmParis", ...);
        m1.setTheatre(new Theatre(1, "Cinéma Paris", "Paris", null));
        Movie m2 = new Movie("FilmLyon", ...);
        m2.setTheatre(new Theatre(2, "Cinéma Lyon", "Lyon", null));
        dao.addMovie(m1);
        dao.addMovie(m2);

        List<Movie> parisMovies = dao.getMoviesByCity("Paris");
        assertEquals(1, parisMovies.size());
    }
}
```
</details>

<details> 
  <summary>🌐 <strong>Tests REST (JerseyTest)</strong></summary>

**Objectif** : Tester les ressources REST de manière isolée.
**Remarque** : 
- On peut utiliser JerseyTest pour déployer les endpoints REST en mémoire et envoyer des requêtes HTTP simulées.
- On peut aussi tester `POST /movies` en envoyant un JSON et en vérifiant que le film est créé. Pour éviter de dépendre de la vraie base, les DAO peuvent être remplacés par des mocks.

**Extrait de test pour MovieResourceTest ** :

```java
class MovieResourceTest extends JerseyTest {

    @Override
    protected Application configure() {
        return new ResourceConfig(MovieResource.class);
    }

    @Test
    void givenCity_whenGetMovies_thenReceiveMoviesList() {
        Response response = target("/movies")
                            .queryParam("city", "Paris")
                            .request()
                            .get();
        assertEquals(200, response.getStatus());

        List<Movie> movies = response.readEntity(new GenericType<List<Movie>>() {});
        assertFalse(movies.isEmpty());
        assertEquals("Paris", movies.get(0).getTheatre().getCity());
    }
}
```
</details>

<br>

## 👥 Répartition des tâches (4 développeurs)

Pour organiser le développement à quatre personnes, la répartition suivante est proposée.  
Chaque développeur travaille sur une partie bien définie avec des interfaces claires entre les modules.



<details>
<summary>💻 <strong>Développeur 1 – Backend REST</strong></summary>

- Implémentation des services REST avec **JAX-RS/Jersey**.
- Écrit les classes Resource (**MovieResource**, **AuthResource**).
- Définit les endpoints (méthodes, URLs, format JSON).
- Utilise des données fictives au départ (listes statiques) pour tester les endpoints sans attendre la base.
- Collabore avec Dev2 pour définir l’appel à la couche DAO.
- Peut écrire des stubs ou interfaces pour DAO.

</details>



<details>
<summary>🗄 <strong>Développeur 2 – Base de Données & DAO</strong></summary>

- Conception du schéma de base de données.
- Écriture des scripts SQL (**schema.sql**, **data.sql**).
- Développement des classes DAO (**MovieDAO**, **TheatreDAO**, **UserDAO**) avec méthodes CRUD.
- Tests indépendants des DAO (via base **MySQL** ou **H2** en mémoire).
- Travaille avec Dev1 pour définir les signatures des méthodes et le format des objets (**Movie**, **Theatre**, **User**).
- Peut fournir à Dev1 un mock DAO pour avancer en parallèle.

</details>


<details>
<summary>🎨 <strong>Développeur 3 – Frontend JSP/HTML</strong></summary>

- Création des pages **JSP** et ressources statiques (**CSS/JS**).
- Pages : **login.jsp**, **addMovie.jsp**, **moviesByCity.jsp**, **movieDetails.jsp**.
- Collaboration avec Dev1 pour connaître les endpoints et formats JSON.
- Utilisation des fichiers **JSON mock** pour développer en autonomie (**movies.json**, **theatres.json**, **users.json**).
- Peut utiliser [json-server](https://www.json-server.dev/) pour simuler l’API.
- Définition de la navigation entre pages.

</details>



<details>
<summary>🧪 <strong>Développeur 4 – Tests & Intégration</strong></summary>

- Rédaction des tests unitaires (**JUnit**) pour les ressources REST et DAO.
- Utilisation de **JerseyTest** pour tester les endpoints REST.
- Création de mocks ou base **H2** pour isoler les tests.
- Configuration Maven (**pom.xml**) :
  - Dépendances : Jersey, Jackson, MySQL, JUnit.
  - Plugins : maven-war-plugin, etc.
- Documentation des instructions de build, déploiement et tests.
- Intégration des modules :
  - Vérification de la compatibilité entre DAO et REST.
  - Exécution des tests d’intégration.
  - Débogage des problèmes éventuels.

</details>



**📝 Note importante** :  
Cette séparation des rôles garantit que chacun peut avancer de manière autonome.  
Le contrat d’interface (**format JSON des échanges**, **schéma de base**) constitue le plan d’intégration.  
Une bonne communication est essentielle pour :
- Se synchroniser sur les noms des champs JSON.
- Les URL des endpoints.
- Le comportement attendu des services.

<br>

## 🏗️ Instructions de build et d’exécution

Pour construire et exécuter l’application **MovieParisApp**, suivez les étapes ci-dessous.

<br>

### 🔧 Prérequis

- **JDK 8** ou supérieur.
- **Maven 3.x**.
- **Tomcat 9** ou supérieur (configuré dans Eclipse ou disponible pour déploiement manuel).
- **MySQL 5+** avec une base nommée `movieparis` encodée en UTF-8.

<br>

### 🗄️ Configuration de la base de données

1. Importez le schéma et les données mock :
    - Exécutez `src/main/resources/db/schema.sql` puis `data.sql` sur la base `movieparis`.
2. **Note** :  
   Par défaut, `MovieDAO` utilise :
   ```text
   jdbc:mysql://localhost:3306/movieparis
   ```
avec l’utilisateur root (sans mot de passe). Adaptez ces paramètres dans `MovieDAO.java` si nécessaire.

<br>

### 🏗️ Build Maven
À la racine du projet, exécutez :
```bash
mvn clean package
```
- Compile le projet.
- Exécute les tests (si configurés).
- Produit le fichier **WAR** dans `target/` (ex : `MovieParisApp.war`).

<br>

### 📦 Dépendances principales (extrait du pom.xml) :
**Remarque** : Les dépendances avec `scope` **provided** (Servlet/JSP API) ne sont pas empaquetées car Tomcat les fournit déjà.
```xml
<dependencies>
    <!-- Jersey -->
    <dependency>
        <groupId>org.glassfish.jersey.core</groupId>
        <artifactId>jersey-server</artifactId>
        <version>2.34</version>
    </dependency>
    <dependency>
        <groupId>org.glassfish.jersey.containers</groupId>
        <artifactId>jersey-container-servlet</artifactId>
        <version>2.34</version>
    </dependency>
    <!-- Jackson -->
    <dependency>
        <groupId>org.glassfish.jersey.media</groupId>
        <artifactId>jersey-media-json-jackson</artifactId>
        <version>2.34</version>
    </dependency>
    <!-- MySQL -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.32</version>
    </dependency>
    <!-- Servlet & JSP -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>4.0.1</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>javax.servlet.jsp-api</artifactId>
        <version>2.3.3</version>
        <scope>provided</scope>
    </dependency>
    <!-- Tests -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.8.2</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```
<br>

### 🚀 Déploiement
Déployez le WAR :
- **Eclipse** : ajoutez le projet à un serveur Tomcat configuré.
- **Manuellement** : copiez `MovieParisApp.war` dans le dossier `webapps/` de Tomcat puis démarrez le serveur (par défaut sur le port 8080).

<br>

### 🌐 Exécution
#### Interface admin :

`http://localhost:8080/MovieParisApp/login.jsp`

-  Connectez-vous avec un compte de `users.json` (ex : `ugc_admin` / `secret`).
-  Redirigé vers `addMovie.jsp` après connexion.

#### Ajout de film :

- Remplissez le formulaire sur `addMovie.jsp`.
- Une requête POST est envoyée à :

`http://localhost:8080/MovieParisApp/api/movies`

#### Interface publique :
- Choisissez une ville (ex : Paris).
- Liste des films s’affiche.
- Cliquez sur un titre pour accéder à sa page détail (`movieDetails.jsp?id=...`).

`http://localhost:8080/MovieParisApp/moviesByCity.jsp`


#### API directe :
- Permet de tester l’API indépendamment (cURL, Postman...).

```text
http://localhost:8080/MovieParisApp/api/movies?city=Paris
http://localhost:8080/MovieParisApp/api/movies/1
```

### 🧪 Exécution des tests
Pour lancer les tests JUnit :
```bash
mvn test
```
- Base de test : soit MySQL, soit H2 en mémoire.
- Tous les tests devraient réussir si les composants fonctionnent correctement.



### 📄 Documentation

Le fichier README.md fournit :

- Vue d’ensemble du projet.
- Diagrammes d’architecture et de classes.
- Contrat d’API.
- Instructions de déploiement et de test.

**Objectif** :
Tout développeur peut cloner le dépôt, déployer l’application localement, travailler sur sa partie et valider l’intégration finale.









