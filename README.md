# MovieParisApp – Application Web de gestion de films (Paris)

## Présentation du projet

**MovieParisApp** est une application web (Java EE) qui offre des services REST pour gérer la programmation de films dans les cinémas parisiens, à l’image du site AlloCiné. 

Le projet permet aux cinémas (admins) de se connecter et d’ajouter de nouveaux films avec tous leurs détails (titre, durée, langue, sous-titres éventuels, réalisateur, acteurs principaux, âge minimum) ainsi que la plage de diffusion (dates de début/fin, jours de la semaine et horaire des séances). Ces informations incluent également la ville où le film est projeté (via l’adresse du cinéma).

Côté utilisateur public, l’application offre une page en libre accès (sans login) pour lister les films par ville donnée et consulter le détail de chaque film (synopsis complet, horaires, cinéma, etc.).

Le projet est développé en Java avec le framework **JAX-RS** (implémentation Jersey) pour les services REST et la bibliothèque **Jackson** pour la gestion du format JSON, le tout déployé sur un conteneur **Tomcat**. Les données sont persistées dans une base **MySQL** (possibilité d’utiliser HSQLDB en mémoire selon les besoins).

L’application suit une architecture **MVC** avec un frontend **JSP/HTML** pour l’interface utilisateur et un backend RESTful pour la logique métier, ce qui permet de bien séparer la présentation du traitement. Le build utilise **Maven** (packaging WAR) afin de faciliter l’intégration continue et la gestion des dépendances.

## Architecture du système

L’architecture est organisée en trois couches principales :
1. **Interface web (JSP)**
2. **Services REST (JAX-RS)**
3. **Base de données MySQL**

Les administrateurs de cinémas interagissent via des pages JSP (login et ajout de film) qui envoient des requêtes HTTP vers les endpoints REST correspondants (par ex. formulaire de login envoyant une requête POST sur `/api/login`).

Les utilisateurs publics accèdent à des pages JSP (liste des films, détails) qui déclenchent des appels REST (AJAX ou server-side) vers l’API pour récupérer les données des films.

Le backend REST (déployé sur Tomcat via Jersey) traite les requêtes, interroge la base de données MySQL à l’aide de la couche **DAO**, puis renvoie les réponses en **JSON** (sérialisées par **Jackson**).


