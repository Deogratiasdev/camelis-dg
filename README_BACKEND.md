# Backend - Système d'Authentification MFA

Backend Python/FastAPI pour le système d'authentification multi-facteurs avec reconnaissance vocale et analyse IA.

## 🚀 Fonctionnalités

### 🔐 Authentification Multi-Facteurs (4 étapes)
1. **Mot de passe** - Hashage sécurisé avec bcrypt-like
2. **OTP par Email** - Codes à usage unique
3. **Reconnaissance Vocale** - Biométrie avec Vosk
4. **Analyse Comportementale** - Détection d'anomalies

### 🤖 Analyse IA Intégrée
- Détection d'anomalies en temps réel
- Prédiction de menaces
- Optimisation des paramètres de sécurité
- Alertes intelligentes

### 📊 Dashboard Admin
- Statistiques en temps réel
- Logs détaillés
- Gestion des utilisateurs
- Alertes de sécurité

## 📋 Prérequis

- Python 3.9+
- SQLite (inclus)
- Accès Internet pour les services externes (email, IA)

## 🛠️ Installation

### 1. Cloner le projet
```bash
git clone <repository-url>
cd backend
```

### 2. Créer environnement virtuel
```bash
python -m venv venv
source venv/bin/activate  # Linux/Mac
# ou
venv\Scripts\activate  # Windows
```

### 3. Installer les dépendances
```bash
pip install -r requirements.txt
```

### 4. Configurer les variables d'environnement
Copier `.env` et modifier les valeurs :
```bash
cp .env .env.local
```

Variables importantes à configurer :
- `SMTP_EMAIL` et `SMTP_PASSWORD` pour l'envoi d'OTP
- `GROQ_API_KEY` pour l'analyse IA
- `JWT_SECRET_KEY` pour la sécurité des tokens

### 5. Télécharger les modèles Vosk (optionnel)
Les modèles sont téléchargés automatiquement avec Docker, sinon :
```bash
mkdir -p models
cd models
wget https://alphacephei.com/vosk/models/vosk-model-small-fr-0.22.zip
unzip vosk-model-small-fr-0.22.zip
wget https://alphacephei.com/vosk/models/vosk-model-spk-0.4.zip
unzip vosk-model-spk-0.4.zip
```

## 🚀 Démarrage

### Développement
```bash
python main.py
```

### Production avec Docker
```bash
docker-compose up -d
```

L'API sera disponible sur `http://localhost:8000`

## 📚 Documentation API

### Routes Simplifiées (Nouveau)

#### 🔐 Authentification (`/auth/*`)
| Méthode | Route | Description |
|---------|-------|-------------|
| POST | `/auth/register` | Inscription |
| POST | `/auth/login` | Connexion (retourne JWT avec `voice_verified: false`) |
| POST | `/auth/request-otp` | Demande code OTP |
| POST | `/auth/verify-otp` | Vérification OTP |
| **GET** | **`/auth/my-security`** | **Profil + logs + alertes (fusionné)** |
| **POST** | **`/auth/verify-voice`** | **🔊 Vérification vocale - retourne nouveau token** |
| **POST** | **`/auth/update-profile`** | **✏️ Modification profil (requiert voice_verified)** |

#### 🔒 Sécurité (`/security/*`)
| Méthode | Route | Description |
|---------|-------|-------------|
| **GET** | **`/security/security-dashboard`** | **Dashboard léger (métriques + alertes)** |
| POST | `/security/behavioral/analyze` | Analyse comportementale |

#### 🤖 IA (`/ai/*`) - Séparées, appelées quand besoin
| Méthode | Route | Description |
|---------|-------|-------------|
| POST | `/ai/analyze-anomalies` | Analyse anomalies |
| POST | `/ai/predict-threats` | Prédiction menaces |
| POST | `/ai/recommendations` | Recommandations |

### Documentation interactive
- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`

Pour la documentation détaillée des routes, voir `ROUTES_DOCUMENTATION.md`

## 🔧 Configuration

### Variables d'environnement

| Variable | Description | Défaut |
|----------|-------------|--------|
| `DATABASE_URL` | URL base de données | `sqlite:///auth_system.db` |
| `JWT_SECRET_KEY` | Clé secrète JWT | **Obligatoire** |
| `JWT_EXPIRATION_HOURS` | Durée session (heures) | `24` |
| `SMTP_SERVER` | Serveur SMTP | `smtp.gmail.com` |
| `SMTP_PORT` | Port SMTP | `587` |
| `SMTP_EMAIL` | Email SMTP | **Obligatoire** |
| `SMTP_PASSWORD` | Mot de passe SMTP | **Obligatoire** |
| `OTP_EXPIRY_MINUTES` | Durée OTP (minutes) | `5` |
| `GROQ_API_KEY` | Clé API Groq | Optionnel |
| `VOICE_SIMILARITY_THRESHOLD` | Seuil similarité voix | `0.45` |
| `MAX_LOGIN_ATTEMPTS` | Max tentatives échouées | `3` |

## 🗄️ Base de données

### Tables principales
- `users` - Utilisateurs et profils
- `sessions` - Sessions actives
- `auth_logs` - Logs d'authentification
- `security_alerts` - Alertes de sécurité
- `trusted_devices` - Appareils de confiance
- `otp_codes` - Codes OTP
- `ai_analyses` - Analyses IA

### Initialisation
La base de données est créée automatiquement au premier démarrage.

## 🔒 Sécurité

### Mesures implémentées
- Hashage des mots de passe (SHA-256 + salt)
- Tokens JWT sécurisés
- Rate limiting sur les tentatives de connexion
- Verrouillage automatique des comptes
- Validation des entrées
- CORS configuré

### Recommandations
- Changer les clés secrètes par défaut
- Utiliser HTTPS en production
- Configurer un reverse proxy (nginx)
- Surveiller les logs régulièrement

## 🧪 Tests

### Tests manuels - Routes Simplifiées

```bash
# 1. INSCRIPTION
curl -X POST "http://localhost:8000/auth/register" \
  -H "Content-Type: application/json" \
  -d '{"email":"test@test.com","password":"Password123","first_name":"Test","last_name":"User"}'

# 2. LOGIN (récupère le token)
curl -X POST "http://localhost:8000/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email":"test@test.com","password":"Password123"}'

# 3. MON COMPLET (profil + logs + alertes) - UNE SEULE REQUÊTE
curl -X GET "http://localhost:8000/auth/my-security" \
  -H "Authorization: Bearer VOTRE_TOKEN"

# 4. DASHBOARD GLOBAL (léger, sans IA)
curl -X GET "http://localhost:8000/security/security-dashboard" \
  -H "Authorization: Bearer VOTRE_TOKEN"

# 5. VÉRIFICATION VOCALE (une fois par session) 🔊
curl -X POST "http://localhost:8000/auth/verify-voice" \
  -H "Authorization: Bearer VOTRE_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"passphrase":"sécurité bancaire"}'
# → Retourne un NOUVEAU token avec voice_verified: true

# 6. MODIFICATION PROFIL (requiert voice_verified) ✏️
curl -X POST "http://localhost:8000/auth/update-profile" \
  -H "Authorization: Bearer NOUVEAU_TOKEN_VOICE_VERIFIED" \
  -H "Content-Type: application/json" \
  -d '{"first_name":"Nouveau","last_name":"Nom","current_password":"Password123","new_password":"NewPass123"}'

# 7. ANALYSE IA (uniquement quand besoin)
curl -X POST "http://localhost:8000/ai/analyze-anomalies" \
  -H "Authorization: Bearer VOTRE_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"analysis_type":"anomalies","time_range":"24h"}'
```

### Tests automatisés
```bash
python -m pytest tests/
```

## 📊 Monitoring

### Logs
- Fichier: `logs/auth_system.log`
- Niveaux: INFO, WARNING, ERROR
- Rotation automatique recommandée

### Métriques
- Dashboard admin en temps réel
- Alertes automatiques
- Statistiques par étape MFA

## 🚀 Déploiement

### Docker (recommandé)
```bash
docker-compose up -d
```

### Manual
```bash
pip install -r requirements.txt
python main.py
```

### Cloud
- **Render** - Déploiement automatique depuis GitHub
- **Heroku** - Compatible avec Dynos
- **AWS/Azure/GCP** - Compatible Docker

## 🔧 Dépannage

### Problèmes courants

#### Erreur SMTP
- Vérifier configuration email
- Utiliser mot de passe d'application (Gmail)
- Vérifier ports et firewall

#### Erreur Vosk
- Télécharger les modèles manuellement
- Vérifier permissions des dossiers
- Mémoire RAM suffisante

#### Erreur IA
- Vérifier clé API Groq
- Vérifier connexion Internet
- Limiter usage pour éviter quotas

### Logs de debug
```bash
# Activer mode debug
export DEBUG=true
export LOG_LEVEL=DEBUG

# Voir logs en temps réel
tail -f logs/auth_system.log
```

## 📝 Développement

### Architecture
- **main.py** - Point d'entrée FastAPI
- **models.py** - Modèles de base de données
- **auth_routes.py** - Routes authentification
- **admin_routes.py** - Routes administration
- **voice_service.py** - Service reconnaissance vocale
- **email_service.py** - Service email/OTP
- **ai_service.py** - Service analyse IA

### Ajouter de nouvelles fonctionnalités
1. Créer le service dans `*_service.py`
2. Ajouter les routes dans `*_routes.py`
3. Mettre à jour le schéma DB si nécessaire
4. Ajouter les tests

## 📄 Licence

Projet éducatif pour soutenance - Voir agent.md pour les détails complets.

## 🤝 Support

Pour toute question ou problème :
1. Vérifier les logs
2. Consulter la documentation API
3. Vérifier la configuration `.env`
4. Créer une issue avec les détails
