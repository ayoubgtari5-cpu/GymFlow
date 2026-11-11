# GymFlow – Documentation du projet

## 1. Présentation du projet

**GymFlow** est une application de gestion de salle de sport basée sur une architecture microservices.

L'objectif est de centraliser les principales activités d'une salle de sport : gestion des membres, réservations des cours, programmes d'entraînement, maintenance des équipements, suivi des performances et nutrition.

Le projet utilise une architecture distribuée afin de séparer les responsabilités métier et de faciliter la maintenance et l'évolution de l'application.

## 2. Problématique

La gestion d'une salle de sport nécessite le suivi de plusieurs activités qui sont liées entre elles.

Une gestion non centralisée peut rendre difficiles :
- Le suivi des membres.
- La réservation des cours.
- La gestion des programmes sportifs.
- Le suivi de la maintenance des machines.
- Le suivi des performances et des objectifs.
- La gestion des plans nutritionnels.

GymFlow propose une solution centralisée et organisée autour de plusieurs microservices spécialisés.

## 3. Acteurs du système

Les principaux acteurs identifiés sont :

| Acteur | Rôle |
|---|---|
| Administrateur | Gère et supervise les activités de la salle |
| Membre | Consulte les informations, réserve des cours et suit ses performances |
| Coach | Gère les programmes et accompagne les membres |
| Responsable de maintenance | Suit les opérations de maintenance des équipements |

## 4. Fonctionnalités principales

- Gestion des utilisateurs et des profils.
- Gestion des réservations des cours.
- Gestion des programmes d'entraînement.
- Gestion de la maintenance des machines.
- Suivi des performances et des objectifs.
- Gestion des plans nutritionnels.

## 5. Découpage en microservices

Le projet est organisé selon les capacités métier suivantes :

### 5.1 Microservice Utilisateurs

Responsabilités :
- Gérer les profils des utilisateurs.
- Consulter et modifier les informations personnelles.
- Gérer le statut actif ou bloqué d'un utilisateur.
- Centraliser les informations de référence des membres.

L'authentification est déléguée à Keycloak.

### 5.2 Microservice Maintenance

Responsabilités :
- Gérer les opérations de maintenance des équipements.
- Ouvrir et clôturer une opération de maintenance.
- Signaler la disponibilité ou l'indisponibilité des machines.
- Informer le service Programmes des exercices concernés.

### 5.3 Microservice Réservations

Responsabilités :
- Gérer les réservations des cours.
- Vérifier les places disponibles.
- Gérer les listes d'attente.
- Empêcher les doubles inscriptions.

Une transaction PostgreSQL permet de gérer les réservations concurrentes.

### 5.4 Microservice Programmes

Responsabilités :
- Gérer les programmes d'entraînement.
- Permettre au coach propriétaire d'affecter un programme.
- Suivre la progression des séances.
- Prendre en compte les informations provenant du service Maintenance.

### 5.5 Microservice Performances

Responsabilités :
- Enregistrer les mesures physiques.
- Calculer l'indice de masse corporelle (IMC).
- Suivre les objectifs des membres.
- Attribuer un badge lorsqu'un objectif est atteint.

Technologie prévue : NestJS et MongoDB.

### 5.6 Microservice Nutrition

Responsabilités :
- Gérer les plans nutritionnels.
- Calculer les totaux journaliers de calories et de protéines.
- Comparer les résultats aux objectifs nutritionnels.
- Informer le coach lorsqu'un objectif de performance est atteint.

**Remarque :** cette liste reprend les six services présentés dans notre support. Le nombre de microservices doit être vérifié avec la consigne de validation, qui mentionne quatre microservices et un microservice supplémentaire utilisant une technologie avancée.

## 6. Données et propriété des données

Chaque microservice possède ses propres données et sa propre base de données. Aucune base de données ne doit être partagée entre les microservices.

| Microservice | Données principales | Base prévue |
|---|---|---|
| Utilisateurs | Profils, coordonnées, statut | À préciser |
| Maintenance | Équipements, opérations de maintenance | À préciser |
| Réservations | Cours, inscriptions, listes d'attente | PostgreSQL |
| Programmes | Programmes, séances, progression | À préciser |
| Performances | Mesures physiques, IMC, objectifs, badges | MongoDB |
| Nutrition | Plans, repas, calories, protéines | À préciser |

Les bases de données des autres services doivent être précisées et justifiées dans la conception détaillée.

Les microservices ne partagent pas directement leurs tables. Ils échangent des identifiants et des données à travers des API ou des événements.

## 7. Communication entre microservices

Deux modes de communication sont prévus :

### 7.1 Communication synchrone

La communication synchrone est utilisée lorsqu'un service a besoin d'une réponse immédiate d'un autre service.

Technologie prévue : OpenFeign.

Exemples :
- Programmes consulte les informations nécessaires auprès de Utilisateurs.
- Nutrition consulte l'objectif du membre auprès de Performances.

### 7.2 Communication asynchrone

La communication asynchrone est utilisée pour transmettre des événements sans attendre une réponse immédiate.

Technologie proposée : RabbitMQ.

Exemples :
- Maintenance publie les changements de disponibilité des machines.
- Performances publie l'événement `objectif.atteint`.
- Nutrition reçoit cet événement et invite le coach à revoir le plan nutritionnel.

### 7.3 Gestion des erreurs

Quelques comportements prévus :
- HTTP 403 : action interdite, par exemple lorsqu'un coach n'est pas propriétaire du programme.
- HTTP 409 : conflit de réservation, par exemple si un membre est déjà inscrit.
- HTTP 503 : service indisponible lorsqu'un service nécessaire ne répond pas.

## 8. Architecture technique

L'architecture générale comprend :

- **Frontend :** interface utilisateur.
- **API Gateway :** point d'entrée des requêtes.
- **Eureka :** découverte des microservices.
- **Config Server :** centralisation de la configuration.
- **Keycloak :** authentification et gestion des accès.
- **Microservices :** traitement des fonctionnalités métier.
- **Bases de données :** stockage indépendant des données de chaque service.
- **Docker :** conteneurisation des composants.

Le Frontend s'authentifie via Keycloak, puis envoie les requêtes à l'API Gateway avec son jeton d'accès. La Gateway utilise Eureka pour localiser le service demandé.

## 9. Modèle de données et API REST

Chaque microservice doit disposer de son propre modèle de données et de ses propres endpoints REST.

Pour chaque service, la documentation détaillée doit préciser :
- Les entités et leurs attributs.
- Les relations entre les entités.
- La base de données choisie et sa justification.
- Les ressources et URI REST.
- Les méthodes HTTP : GET, POST, PUT, DELETE.
- Les codes de réponse HTTP prévus.

Les modèles de données et les endpoints détaillés seront ajoutés dans les documents spécifiques de chaque microservice.

## 10. Organisation du dépôt Git

```text
GymFlow/
├── Backend/
│   ├── users-service/
│   ├── maintenance-service/
│   ├── reservations-service/
│   ├── programs-service/
│   ├── performances-service/
│   └── nutrition-service/
│
├── Frontend/
│
├── Documentation/
│   ├── README.md
│   ├── Presentation/
│   └── Diagrams/
│
└── README.md
```

## 11. Conclusion

GymFlow repose sur une architecture microservices qui sépare les responsabilités métier et garantit l'indépendance des données.

La conception définit les responsabilités de chaque service, les choix technologiques et les mécanismes de communication. Cette étape constitue la base du futur développement de l'application.
