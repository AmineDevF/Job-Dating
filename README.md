# Application Job Dating YouCode

## Contexte Général

YouCode organise des événements de Job Dating afin de connecter ses apprenants avec des entreprises partenaires.  
Cette application web a pour objectif de centraliser la gestion des annonces d’emploi, des entreprises et des apprenants, tout en offrant un espace simple et performant pour la consultation des offres.

L’application est développée en PHP en s’appuyant sur un framework MVC minimaliste existant.

---

## Infrastructure Technique Existante

Le projet repose sur un framework personnalisé comprenant :

- Architecture MVC avec séparation Front Office / Back Office
- Système de routing personnalisé
- Base de données  mySQL (PostgreSQL bonus) (requêtes préparées via PDO)
- Moteur de templates Twig
- Système d’authentification et gestion sécurisée des sessions
- Protection CSRF sur les formulaires
- Sécurisation contre XSS et SQL Injection
- Validation des données côté serveur
- Autoloading via Composer

---

## Rôles Utilisateurs

### Administrateur
Responsable de la gestion complète de la plateforme :
- Gestion des annonces d’emploi
- Gestion des entreprises partenaires
- Consultation des apprenants inscrits
- Archivage et restauration des annonces
- Accès au dashboard et aux statistiques

### Apprenant
Étudiant YouCode à la recherche d’opportunités professionnelles :
- Consultation des offres d’emploi actives
- Recherche et filtrage des annonces
- Consultation des informations des entreprises

---

## Fonctionnalités Principales

## Module 1 : Authentification et Accès

### Connexion Administrateur
- Connexion via email et mot de passe
- Accès réservé aux utilisateurs avec le rôle `admin`
- Protection CSRF
- Limitation des tentatives de connexion
- Création de session sécurisée
- Redirection vers le dashboard
- Expiration de session après 2 heures d’inactivité

### Connexion Apprenant
- Connexion via email et mot de passe
- Accès uniquement au front office
- Redirection vers la liste des offres
- Séparation stricte des sessions admin / apprenant

### Déconnexion
- Destruction complète de la session
- Invalidation du token CSRF
- Redirection vers la page de connexion
- Accès aux pages protégées bloqué après déconnexion

---

## Module 2 : Dashboard Administrateur

### Statistiques Globales
- Nombre d’annonces actives (`deleted = false`)
- Nombre d’annonces archivées
- Nombre d’entreprises partenaires
- Nombre d’apprenants inscrits
- Données calculées dynamiquement depuis mySQL (PostgreSQL bonus)

### Annonces Récentes
- Affichage des 3 dernières annonces actives
- Tri par date de création décroissante
- Accès rapide aux détails

### Actions Rapides
- Création d’annonce
- Ajout d’entreprise
- Accès direct aux fonctionnalités principales

---

## Module 3 : Gestion des Annonces

### Création d’Annonce
- Champs : titre, entreprise, type de contrat, localisation, image , description, compétences 
- Validation complète côté serveur
- Insertion sécurisée en base  mySQL (PostgreSQL bonus)
- Protection XSS / SQL Injection
- Statut actif par défaut (`deleted = false`)

### Consultation des Annonces
- Liste des annonces actives
- Chargement optimisé avec jointures  mySQL (PostgreSQL bonus)
- Tri par date de création
- Actions : modifier / archiver

### Modification
- Formulaire pré-rempli
- Mise à jour sécurisée en base
- Conservation de la date de création
- Ajout automatique de la date de mise à jour

### Archivage (Soft Delete)
- Passage de `deleted = true`
- Retrait immédiat du front office
- Apparition dans la section archives
- Mise à jour automatique des statistiques

### Restauration
- Retour de `deleted = true` à `false`
- Réintégration dans les annonces actives
- Données conservées sans modification

---

## Module 4 : Gestion des Entreprises

### Ajout d’Entreprise
- Nom, secteur, localisation, email unique, téléphone
- Validation stricte des données
- Avatar généré automatiquement
- Insertion sécurisée en base  mySQL (PostgreSQL bonus)

### Consultation
- Affichage en grille responsive
- Informations complètes de chaque entreprise
- Actions : modifier / supprimer

### Modification
- Mise à jour des informations
- Unicité de l’email garantie
- Mise à jour automatique des annonces associées

### Suppression
- Suppression définitive (hard delete)
- Gestion des annonces associées :
  - Suppression en cascade ou
  - Blocage si des annonces existent

---

## Module 5 : Gestion des Apprenants

### Consultation
- Liste complète des apprenants
- Tableau avec nom, email, promotion, spécialisation
- Données issues des relations mySQL (PostgreSQL bonus)
- Consultation uniquement (pas de CRUD)

---

## Module 6 : Front Office – Espace Apprenant

### Consultation des Offres
- Affichage des annonces actives uniquement
- Tri par date de publication
- Informations complètes de l’offre et de l’entreprise
- Interface responsive et moderne

### Recherche
- Recherche en temps réel (AJAX)
- Recherche sur titre, entreprise et description
- Résultats dynamiques sans rechargement

### Filtres
- Filtre par entreprise (entreprises ayant des annonces actives)
- Filtre par type de contrat
- Filtres combinables
- Mise à jour dynamique des résultats

---

## 🗂️ Structure MVC Proposée

```text
job_dating/
├── public/
│   ├── index.php                # Point d’entrée de l’application
│   ├── .htaccess                # Réécriture des URLs
│   └── assets/
│       ├── css/
│       ├── js/
│       └── images/
│
├── app/
│   ├── controllers/
│   │   ├── front/               # Front Office (Apprenants)
│   │   │   ├── AuthController.php
│   │   │   ├── JobController.php
│   │   │   └── CompanyController.php
│   │   │
│   │   └── back/                # Back Office (Administrateurs)
│   │       ├── AuthController.php
│   │       ├── DashboardController.php
│   │       ├── AnnouncementController.php
│   │       ├── CompanyController.php
│   │       └── StudentController.php
│   │
│   ├── models/
│   │   ├── User.php              # Admin & Apprenant
│   │   ├── Announcement.php
│   │   ├── Company.php
│   │   └── Student.php
│   │
│   ├── views/
│   │   ├── front/
│   │   │   ├── auth/
│   │   │   │   └── login.twig
│   │   │   └── jobs/
│   │   │       └── index.twig
│   │   │
│   │   └── back/
│   │       ├── auth/
│   │       │   └── login.twig
│   │       ├── dashboard/
│   │       │   └── index.twig
│   │       ├── announcements/
│   │       │   ├── index.twig
│   │       │   └── archived.twig
│   │       ├── companies/
│   │       │   └── index.twig
│   │       └── students/
│   │           └── index.twig
│   │
│   └── core/
│       ├── Router.php            # Gestion des routes
│       ├── Controller.php        # Controller parent
│       ├── Model.php             # Model parent
│       ├── View.php              # Rendu Twig
│       ├── Database.php          # Connexion PostgreSQL (PDO)
│       ├── Auth.php              # Authentification & rôles
│       ├── Validator.php         # Validation des données
│       ├── Security.php          # CSRF, XSS, protections
│       └── Session.php           # Gestion des sessions
│
├── config/
│   ├── config.php                # Configuration globale
│   └── routes.php                # Définition des routes Front / Back
│
├── logs/                          # Logs applicatifs (bonus)
│
├── vendor/                        # Dépendances Composer
│
├── .env                           # Variables d’environnement
├── composer.json
└── .gitignore

```


---

## Contraintes de Sécurité

- Protection CSRF sur tous les formulaires
- Validation systématique des entrées utilisateur
- Échappement des données affichées (XSS)
- Requêtes préparées  mySQL (PostgreSQL / bonus) (PDO)
- Séparation stricte des accès admin / apprenant
- Nettoyage complet des sessions à la déconnexion
