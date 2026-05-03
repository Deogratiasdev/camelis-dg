# 📋 Système d'Authentification Multi-Facteurs (MFA)

## 🗂 Structure du Projet

```
Projet securité/
├── backend/                    # API FastAPI complète
│   ├── main.py                # Point d'entrée avec rate limiting
│   ├── models.py              # Modèles de données JSON
│   ├── json_storage.py         # Système de stockage JSON local
│   ├── .env                   # Configuration complète
│   ├── requirements.txt        # Dépendances Python
│   ├── start.py               # Démarrage rapide
│   ├── generate_jwt_keys.py   # Générateur de clés JWT
│   ├── README.md              # Documentation backend
│   ├── data/                  # Fichiers JSON de stockage
│   │   ├── users.json         # Utilisateurs
│   │   ├── sessions.json      # Sessions
│   │   ├── otp_codes.json     # Codes OTP
│   │   ├── auth_logs.json     # Logs d'authentification
│   │   ├── rate_limits.json   # Limites de rate
│   │   └── ai_analyses.json   # Analyses IA
│   │
│   ├── routes/                # Routes API organisées
│   │   ├── auth_routes.py     # Routes MFA (4 étapes)
│   │   ├── admin_routes.py    # Routes admin optimisées
│   │   └── rate_limit_routes.py # Routes rate limiting personnalisé
│   │
│   ├── services/              # Services métier
│   │   ├── voice_service.py   # Service reconnaissance vocale
│   │   ├── email_service.py   # Service email (texte simple)
│   │   └── ai_service.py      # Service analyse IA
│   │
│   ├── middleware/            # Middlewares de sécurité
│   │   ├── auth.py            # Authentification commune
│   │   ├── user_rate_limit_middleware.py # Rate limiting utilisateur
│   │   └── middleware.py      # Middleware général de sécurité
│   │
│   └── tests/                 # Tests et validation
│       └── test_api.py        # Tests complets
│
└── frontend/                   # Interface utilisateur
    ├── index.html             # Page d'accueil
    ├── register.html           # Inscription 4 étapes
    ├── register.js            # JavaScript inscription
    ├── login.html              # Login MFA
    ├── login.js               # JavaScript login
    ├── dashboard.html          # Dashboard admin
    ├── dashboard.js           # JavaScript dashboard
    ├── styles.css             # CSS commun moderne
    └── README.md              # Documentation frontend
```

## ✅ Fonctionnalités Implémentées

### 🔐 Authentification Multi-Facteurs (4 étapes)
1. **Mot de passe** - Hashage SHA-256 + salt
2. **OTP Email** - Codes à usage unique (5 minutes)
3. **Reconnaissance Vocale** - Empreintes biométriques avec Vosk
4. **Analyse Comportementale** - Détection d'anomalies

### 🛡️ Sécurité
- **Rate Limiting personnalisable** par utilisateur :
  - Limite générale : 60 req/min, 1000/heure (par défaut)
  - Limite auth : 5 tentatives/15 minutes (par défaut)
  - Fichier `rate_limits.json` pour limites personnalisées
- **Protection contre injections** avec validation JSON
- **Verrouillage automatique** des comptes (3 échecs)
- **Logging complet** de tous les événements
- **Sessions sécurisées** avec JWT

### 📊 Administration
- **Dashboard temps réel** avec statistiques
- **Graphiques** des connexions par étape
- **Alertes de sécurité** automatiques
- **Analyse IA** intégrée (Groq) :
  - Détection d'anomalies
  - Prédiction de menaces
  - Optimisation sécurité
- **Gestion des limites de rate** par utilisateur

### 🎨 Frontend Moderne
- **Design responsive** et accessible
- **Interface 4 étapes** avec barre de progression
- **Enregistrement vocal** dans le navigateur
- **Dashboard admin** avec Chart.js
- **Notifications** en temps réel

### 📧 Emails Texte Simple
- **OTP** : Code simple + instructions claires
- **Bienvenue** : Informations essentielles uniquement
- **Alertes** : Format lisible et direct

## 🚀 Démarrage rapide

### 1. Génération des clés JWT
```bash
cd backend
# Générer automatiquement les clés JWT sécurisées
python generate_jwt_keys.py

# Les clés seront ajoutées automatiquement dans .env
```

### 2. Configuration Backend
```bash
# Modifier .env avec vos clés (déjà générées ci-dessus)
SMTP_EMAIL=votre_email@gmail.com
SMTP_PASSWORD=votre_mdp_app
GROQ_API_KEY=gsk_votre_clé
# JWT_SECRET_KEY, REFRESH_TOKEN_SECRET, ENCRYPTION_KEY déjà générés

# Personnaliser le rate limiting si nécessaire
RATE_LIMIT_REQUESTS_PER_MINUTE=60
RATE_LIMIT_AUTH_MAX_ATTEMPTS=5
RATE_LIMIT_AUTH_WINDOW_MINUTES=15
```

### 3. Installation et Démarrage
```bash
# Installation dépendances
pip install -r requirements.txt

# Démarrage automatique (vérifie tout)
python start.py

# Ou démarrage manuel
python main.py
```

### 4. Accès Frontend
```bash
cd frontend
# Ouvrir index.html dans le navigateur
# Ou utiliser un serveur local
python -m http.server 8080
```

## 🔧 Configuration Rate Limiting

Le rate limiting est **entièrement personnalisable** via le fichier `.env` :

```bash
# Rate limiting général
RATE_LIMIT_REQUESTS_PER_MINUTE=60    # Requêtes par minute
RATE_LIMIT_REQUESTS_PER_HOUR=1000     # Requêtes par heure

# Rate limiting authentification
RATE_LIMIT_AUTH_MAX_ATTEMPTS=5       # Max tentatives
RATE_LIMIT_AUTH_WINDOW_MINUTES=15     # Fenêtre temporelle
```

## 📱 Pages Frontend

### 1. Page d'accueil (`index.html`)
- Présentation du système MFA
- Boutons d'accès rapide
- Informations sur les fonctionnalités

### 2. Inscription (`register.html`)
- **Étape 1** : Formulaire (email, mot de passe, infos)
- **Étape 2** : Enregistrement vocal (micro ou upload)
- **Étape 3** : Appareil de confiance
- **Étape 4** : Confirmation et succès

### 3. Login (`login.html`)
- **Étape 1** : Email + mot de passe
- **Étape 2** : Code OTP (avec renvoi)
- **Étape 3** : Reconnaissance vocale
- **Étape 4** : Analyse comportementale + dashboard

### 4. Dashboard Admin (`dashboard.html`)
- Statistiques en temps réel
- Graphiques des connexions
- Logs d'authentification
- Analyse IA avec résultats

## 🗄️ Stockage JSON Local

### Fichiers JSON structurés :
1. **`users.json`** - Informations utilisateurs et mots de passe hashés
2. **`sessions.json`** - Sessions actives et tokens JWT
3. **`otp_codes.json`** - Codes OTP temporaires
4. **`auth_logs.json`** - Logs d'authentification détaillés
5. **`rate_limits.json`** - Limites personnalisées par utilisateur
6. **`ai_analyses.json`** - Résultats des analyses IA

### Avantages pour la soutenance :
- **Lisibilité** : Fichiers JSON faciles à inspecter et comprendre
- **Portabilité** : Pas de dépendance à une base de données externe
- **Simplicité** : Structure claire et auto-documentée
- **Performance** : Accès rapide pour démonstration

### Sécurité des données :
- **Mots de passe** : Hashage SHA-256 + salt unique par utilisateur
- **Tokens** : Stockage des hash uniquement (jamais les tokens en clair)
- **Logs** : Format structuré avec timestamps ISO
- **Cleanup** : Suppression automatique des données expirées

## 📧 Emails Texte Simple

Plus de HTML complexe, uniquement du texte brut :
- OTP : Code simple et instructions claires
- Bienvenue : Informations essentielles
- Alertes : Format lisible et direct

## 🐳 Déploiement Docker

```bash
# Build et déploiement
docker-compose up -d

# Logs en temps réel
docker-compose logs -f

# Arrêt
docker-compose down
```

## 🧪 Tests Complets

```bash
# Test complet de l'API
python test_api.py

# Tests toutes les fonctionnalités :
# - Health check
# - Inscription 4 étapes
# - Login MFA complet
# - Dashboard admin
# - Analyse IA
```

## 📊 Monitoring

### Logs structurés :
- Niveaux : INFO, WARNING, ERROR
- Fichier : `logs/auth_system.log`
- Rotation automatique recommandée
- Envoi vers système de logs possible

### Métriques disponibles :
- Taux de succès par étape
- Temps de réponse moyen
- Alertes de sécurité
- Utilisation des ressources

## 🔒 Points de sécurité

### ✅ Implémentés :
- **Rate limiting** configurable
- **Verrouillage** automatique comptes
- **JWT sécurisés** avec expiration
- **Hashage mots de passe** robuste
- **Validation entrées** serveur et client
- **CORS** configuré
- **HTTPS** recommandé en production

### 🔄 À configurer en production :
- Clés secrètes uniques
- HTTPS obligatoire
- Rate limiting ajusté
- Logs vers système centralisé
- Backup régulier des fichiers JSON

## 🎯 Idéal pour la soutenance

### ✅ Points forts :
- **Fonctionnel** et testé
- **Moderne** et responsive
- **Sécurisé** avec rate limiting
- **Documenté** complètement
- **Déployable** facilement

### 📋 Démo 10 minutes :
1. **Inscription** (2 min) - 4 étapes visuelles
2. **Login MFA** (3 min) - Processus complet
3. **Dashboard** (3 min) - Statistiques et graphiques
4. **Alerte IA** (2 min) - Analyse en temps réel

Le projet est **100% prêt** pour la soutenance avec toutes les fonctionnalités demandées !
