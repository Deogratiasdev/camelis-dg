# Documentation des Routes API d'Authentification

## Overview
API d'authentification simple avec vérification OTP et JWT.

## Routes d'Authentification

### 1. Inscription
**Endpoint:** `POST /auth/register`

**Description:** Crée un nouveau compte utilisateur.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "SecurePass123",
  "first_name": "John",
  "last_name": "Doe"
}
```

**Response:**
```json
{
  "id": "uuid-generated",
  "email": "user@example.com",
  "first_name": "John",
  "last_name": "Doe",
  "message": "Inscription réussie. Veuillez vérifier votre compte avec le code OTP."
}
```

### 2. Demande OTP
**Endpoint:** `POST /auth/request-otp`

**Description:** Demande un code OTP pour vérifier le compte.

**Request Body:**
```json
{
  "email": "user@example.com"
}
```

**Response:**
```json
{
  "message": "Code OTP envoyé par email",
  "otp_for_testing": "123456"  // Uniquement pour les tests
}
```

### 3. Vérification OTP
**Endpoint:** `POST /auth/verify-otp`

**Description:** Vérifie le code OTP et active le compte.

**Request Body:**
```json
{
  "email": "user@example.com",
  "code": "123456"
}
```

**Response:**
```json
{
  "message": "Compte vérifié avec succès"
}
```

### 4. Connexion
**Endpoint:** `POST /auth/login`

**Description:** Authentifie l'utilisateur et retourne un token JWT.

**Important:** Le compte doit être vérifié avec OTP avant de pouvoir se connecter.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "SecurePass123"
}
```

**Response:**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user_id": "uuid-generated",
  "email": "user@example.com",
  "message": "Connexion réussie"
}
```

### 5. Informations Utilisateur
**Endpoint:** `GET /auth/me`

**Description:** Récupère les informations de l'utilisateur connecté.

**Headers:**
```
Authorization: Bearer <token_jwt>
```

**Response:**
```json
{
  "id": "uuid-generated",
  "email": "user@example.com",
  "first_name": "John",
  "last_name": "Doe",
  "is_verified": true,
  "created_at": "2026-04-27T15:26:08.01174"
}
```

### 6. Logs Utilisateur
**Endpoint:** `GET /auth/my-logs`

**Description:** Récupère les logs d'authentification de l'utilisateur connecté.

**Headers:**
```
Authorization: Bearer <token_jwt>
```

**Query Parameters (optionnels):**
- `limit` : Nombre maximum de logs à retourner (défaut: 50)
- `event_type` : Filtrer par type d'événement (`register`, `request_otp`, `verify_otp`, `login`, `access_me`, `access_logs`)

**Response:**
```json
{
  "user_id": "uuid-generated",
  "email": "user@example.com",
  "logs": [
    {
      "id": "log-uuid",
      "timestamp": "2026-04-27T15:36:47.394378",
      "event_type": "login",
      "success": true,
      "risk_level": "low",
      "ip_address": "127.0.0.1",
      "country": "Local",
      "device": {"os": "Windows", "browser": "Chrome"},
      "details": "Connexion réussie: user@example.com",
      "session_id": "session-uuid"
    }
  ],
  "total_count": 1,
  "filters": {
    "limit": 50,
    "event_type": "login"
  }
}
```

### 7. Résumé de Sécurité
**Endpoint:** `GET /auth/my-security-summary`

**Description:** Récupère un résumé de sécurité personnel avec score et recommandations.

**Headers:**
```
Authorization: Bearer <token_jwt>
```

**Response:**
```json
{
  "user_id": "uuid-generated",
  "email": "user@example.com",
  "security_summary": {
    "total_events": 10,
    "failed_attempts": 2,
    "success_rate": 80.0,
    "high_risk_events": 1,
    "unique_ips": 2,
    "unique_countries": 1,
    "unique_devices": 1,
    "recent_activity_24h": 5,
    "last_activity": "2026-04-27T15:36:47.394378",
    "security_score": 75
  },
  "recommendations": [
    "Plusieurs tentatives de connexion échouées détectées. Changez votre mot de passe."
  ]
}
```

### 8. Alertes de Sécurité
**Endpoint:** `GET /auth/security-alerts`

**Description:** Récupère les alertes de sécurité récentes (admin uniquement).

**Headers:**
```
Authorization: Bearer <token_jwt>
```

**Query Parameters (optionnels):**
- `hours` : Période en heures (défaut: 24)
- `min_risk_level` : Niveau de risque minimum (`low`, `medium`, `high`, `critical`) (défaut: `medium`)

**Response:**
```json
{
  "alerts": [
    {
      "id": "alert-uuid",
      "timestamp": "2026-04-27T15:36:47.394378",
      "event_type": "login",
      "risk_level": "high",
      "ip_address": "192.168.1.100",
      "country": "Unknown",
      "details": "Mot de passe incorrect: user@example.com"
    }
  ],
  "total_count": 1,
  "filters": {
    "hours": 24,
    "min_risk_level": "medium"
  },
  "summary": {
    "critical": 0,
    "high": 1,
    "medium": 0
  }
}
```

## Rate Limiting

### Limites par défaut (configurées via .env):
- **Requêtes par heure:** 1000 (RATE_LIMIT_REQUESTS_PER_HOUR)
- **Tentatives d'authentification:** 5 (RATE_LIMIT_AUTH_MAX_ATTEMPTS)
- **Fenêtre d'authentification:** 15 minutes (RATE_LIMIT_AUTH_WINDOW_MINUTES)

### Headers de Rate Limiting:
- `X-RateLimit-Limit`: Limite autorisée
- `X-RateLimit-Remaining`: Requêtes restantes
- `X-RateLimit-Reset`: Timestamp de réinitialisation
- `X-RateLimit-Type`: Type de limite ("default" ou "personalized")

## Flow d'Authentification Complet

1. **Inscription** → Créer un compte (loggué avec risque)
2. **Demande OTP** → Obtenir un code de vérification (loggué)
3. **Vérification OTP** → Activer le compte (loggué avec risque)
4. **Connexion** → Obtenir un token JWT (loggué avec risque)
5. **Utilisation** → Utiliser le token pour accéder aux routes protégées (loggué)
6. **Consultation** → Voir ses logs d'authentification (loggué)
7. **Sécurité** → Consulter son résumé de sécurité (loggué)
8. **Alertes** → Voir les alertes de sécurité (admin, loggué)

## Logging et Sécurité

### Système de Logging Essentiel
Toutes les routes sont logguées dans `data/auth_logs.json` avec les informations essentielles :
- **ID unique** : UUID pour chaque événement
- **Timestamp** : Date/heure précise
- **Event Type** : Type d'événement
- **User ID** : Identifiant de l'utilisateur
- **Success** : Succès/échec (booléen)
- **Risk Level** : Niveau de risque calculé automatiquement (low/medium/high/critical)
- **IP Address** : Adresse IP du client
- **Country** : Pays de l'IP (géolocalisation basique)
- **Device** : OS et navigateur extraits intelligemment
- **Session ID** : Lien avec la session active
- **Details** : Description concise et pertinente

### Types d'événements loggués :
- `register` : Inscription d'utilisateur
- `request_otp` : Demande de code OTP
- `verify_otp` : Vérification OTP
- `login` : Connexion avec JWT
- `access_me` : Consultation du profil
- `access_logs` : Consultation des logs
- `access_security` : Consultation du résumé de sécurité
- `access_alerts` : Consultation des alertes

### Niveaux de risque automatiques :
- **Critical** : Événements de sécurité critiques
- **High** : Mot de passe incorrect, OTP invalide/expire
- **Medium** : Email inexistant, compte non vérifié, token invalide
- **Low** : Connexions réussies normales, consultations

### Sécurité

- **Hashing des mots de passe:** SHA256
- **Tokens JWT:** Expiration 24 heures
- **OTP:** Validité 10 minutes
- **Rate Limiting:** Protection contre les attaques par force brute
- **Vérification obligatoire:** Impossible de se connecter sans vérification OTP

## Configuration

Variables d'environnement importantes:
- `JWT_SECRET_KEY`: Clé secrète pour les tokens JWT
- `RATE_LIMIT_REQUESTS_PER_HOUR`: Limite de requêtes par heure
- `RATE_LIMIT_AUTH_MAX_ATTEMPTS`: Tentatives d'authentification max
- `RATE_LIMIT_AUTH_WINDOW_MINUTES`: Fenêtre de temps pour les tentatives

## Stockage

Toutes les données sont stockées dans des fichiers JSON:
- `data/users.json`: Utilisateurs
- `data/otp_codes.json`: Codes OTP
- `data/sessions.json`: Sessions JWT
- `data/rate_limits.json`: Limites personnalisées (futures)
- `data/auth_logs.json`: Logs d'authentification et d'accès

## Exemples d'utilisation

### Flow complet avec logging :
```bash
# 1. Inscription (loggué)
POST /auth/register
{
  "email": "user@example.com",
  "password": "SecurePass123",
  "first_name": "John",
  "last_name": "Doe"
}

# 2. Demande OTP (loggué)
POST /auth/request-otp
{
  "email": "user@example.com"
}

# 3. Vérification OTP (loggué)
POST /auth/verify-otp
{
  "email": "user@example.com",
  "code": "123456"
}

# 4. Connexion (loggué)
POST /auth/login
{
  "email": "user@example.com",
  "password": "SecurePass123"
}

# 5. Consultation des logs (loggué)
GET /auth/my-logs?event_type=login&limit=10
Authorization: Bearer <token_jwt>
```

### Filtrage des logs :
```bash
# Tous les logs de l'utilisateur
GET /auth/my-logs

# Uniquement les logs de connexion
GET /auth/my-logs?event_type=login

# Limité à 20 logs
GET /auth/my-logs?limit=20

# Logs de connexion limités à 10
GET /auth/my-logs?event_type=login&limit=10
```