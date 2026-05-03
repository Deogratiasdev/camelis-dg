# 🛡️ Security Web - Workflow d'Authentification

> Document pour soutenance - Architecture et flux de l'application

---

## 📊 Architecture Globale

```
┌─────────────────────────────────────────────────────────────────┐
│                      ARCHITECTURE SECURITY WEB                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────┐      HTTP/API       ┌──────────────┐         │
│  │              │ ◄─────────────────► │              │         │
│  │   FRONTEND   │      (Port 8000)     │   BACKEND    │         │
│  │  HTML/CSS/JS │                      │   FastAPI    │         │
│  │  (Port 5500) │                      │   (Python)   │         │
│  │              │                      │              │         │
│  └──────────────┘                      └──────┬───────┘         │
│         │                                       │               │
│         │ LocalStorage                           │ JSON Files    │
│         ▼                                       ▼               │
│  ┌──────────────┐                      ┌──────────────────┐      │
│  │   Token JWT  │                      │   data/users.json │      │
│  │   User Info  │                      │   data/auth_logs  │      │
│  │   pendingEmail│                     │   data/otp_codes  │      │
│  └──────────────┘                      │   data/sessions   │      │
│                                         └──────────────────┘      │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🔐 Flux d'Inscription

```
┌─────────┐     ┌──────────────┐     ┌─────────────────┐     ┌────────────┐
│  User   │────►│ register.html│────►│ POST /register  │────►│  Backend   │
│         │     │              │     │                 │     │            │
└─────────┘     └──────────────┘     └─────────────────┘     └─────┬──────┘
                                                                    │
                                                                    ▼
┌─────────┐     ┌──────────────┐     ┌─────────────────┐     ┌────────────┐
│  User   │◄────│ otp-verify   │◄────│ POST /request-otp │◄────│ Création   │
│ Vérifié │     │   .html      │     │  (Email Brevo)   │     │  Compte    │
└─────────┘     └──────────────┘     └─────────────────┘     │is_verified │
       ▲                                                      │   = false  │
       │                                                      └────────────┘
       │
       └──────── POST /verify-otp ────► OTP validé ────► is_verified = true
```

### Étapes détaillées :

1. **Formulaire d'inscription** (`register.html`)
   - Saisie : Prénom, Nom, Email, Mot de passe
   - Validation côté client (8 caractères min)

2. **Envoi au Backend** (`POST /auth/register`)
   - Hashage SHA-256 du mot de passe
   - Création utilisateur avec `is_verified: false`
   - Retour : ID utilisateur, message de succès

3. **Génération OTP** (automatique après register)
   - `POST /auth/request-otp`
   - Email envoyé via Brevo
   - Stockage OTP dans `data/otp_codes.json`

4. **Vérification OTP** (`otp-verify.html`)
   - Saisie code 6 chiffres
   - `POST /auth/verify-otp`
   - Si validé : `is_verified: true`
   - Redirection vers `login.html`

---

## 🔑 Flux de Connexion

```
┌─────────┐     ┌──────────────┐     ┌─────────────────┐     ┌────────────┐
│  User   │────►│  login.html  │────►│  POST /login    │────►│  Backend   │
│         │     │              │     │   email/pass    │     │            │
└─────────┘     └──────────────┘     └─────────────────┘     └─────┬──────┘
                                                                    │
                                                          ┌─────────┴────────┐
                                                          │                  │
                                                          ▼                  ▼
                                                    ┌──────────┐      ┌──────────┐
                                                    │is_verified│      │ Password │
                                                    │  = false  │      │  Wrong   │
                                                    └─────┬─────┘      └────┬─────┘
                                                          │                  │
                    ┌─────────────────────────────────────┘                  │
                    │                                                        │
                    ▼                                                        ▼
            ┌──────────────┐                                       ┌──────────────┐
            │  otp-verify    │                                       │ Erreur 401   │
            │  ?unverified=1  │                                       │ Identifiants │
            │                │                                       │ incorrects   │
            └──────────────┘                                       └──────────────┘
                    │
                    │ (OTP validé)
                    ▼
            ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
            │   Token JWT  │────►│ dashboard    │────►│  Navigation  │
            │   stocké LS  │     │   .html      │     │  Responsive  │
            └──────────────┘     └──────────────┘     └──────────────┘
```

### Étapes détaillées :

1. **Formulaire de connexion** (`login.html`)
   - Email + Mot de passe
   - Toggle afficher/masquer mot de passe

2. **Vérification Backend** (`POST /auth/login`)
   ```python
   Vérifie:
   ├── User existe ?
   ├── is_verified == True ? (sinon 403)
   └── Password hash match ?
   ```

3. **Si non vérifié (403)**
   - Stockage email dans `localStorage.pendingEmail`
   - Redirection vers `otp-verify.html?unverified=true`
   - Message : "Votre compte doit être vérifié"

4. **Si connexion réussie (200)**
   - Token JWT généré (valide 24h)
   - Stockage : `localStorage.token` + `localStorage.user`
   - Redirection vers `dashboard.html`

---

## 🏗️ Structure des Données

### Utilisateur (users.json)
```json
{
  "id": "uuid",
  "email": "user@example.com",
  "password_hash": "sha256_hash",
  "first_name": "Jean",
  "last_name": "Dupont",
  "created_at": "2024-01-15T10:30:00",
  "is_verified": true,
  "failed_attempts": 0,
  "locked_until": null
}
```

### Session JWT
```json
{
  "user_id": "uuid",
  "exp": "2024-01-16T10:30:00",
  "iat": "2024-01-15T10:30:00"
}
```

### Log d'authentification
```json
{
  "id": "uuid",
  "timestamp": "2024-01-15T10:30:00",
  "event_type": "login",
  "user_id": "uuid",
  "success": true,
  "risk_level": "low",
  "ip_address": "192.168.1.1",
  "device": {"browser": "Chrome", "os": "Windows"},
  "details": "Connexion réussie"
}
```

---

## 🛡️ Mécanismes de Sécurité

```
┌─────────────────────────────────────────────────────────┐
│              COUCHES DE SÉCURITÉ                        │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  1️⃣  AUTHENTIFICATION                                   │
│      └── SHA-256 + Salt implicite (email)              │
│                                                         │
│  2️⃣  VÉRIFICATION COMPTE                               │
│      └── OTP Email (6 chiffres, 10 min)               │
│                                                         │
│  3️⃣  SESSIONS                                           │
│      └── JWT (HS256, 24h expiration)                   │
│                                                         │
│  4️⃣  RATE LIMITING                                      │
│      └── Limitation par IP et par utilisateur          │
│                                                         │
│  5️⃣  LOGGING                                            │
│      └── Tous les événements traçables                 │
│                                                         │
│  6️⃣  ANALYSE IA                                         │
│      └── Détection anomalies via Groq API              │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 📱 Navigation Responsive

```
Desktop (> 900px)              Mobile (< 900px)
┌────────────────────┐        ┌────────────────────┐
│  🔒 Security Web   │        │                    │
│  ┌──┬──┬──┬──┐     │        │     Contenu        │
│  │🏠│👤│📋│🧠│  🚪│        │                    │
│  └──┴──┴──┴──┘     │        ├────────────────────┤
│                    │        │🏠  👤  📋  🧠  🚪   │
│     Contenu        │        └────────────────────┘
│                    │
└────────────────────┘
```

---

## 🔧 API Endpoints Principaux

| Méthode | Route | Description |
|---------|-------|-------------|
| POST | `/auth/register` | Inscription nouvel utilisateur |
| POST | `/auth/login` | Connexion (JWT) |
| POST | `/auth/request-otp` | Demande code OTP |
| POST | `/auth/verify-otp` | Validation code OTP |
| GET | `/auth/my-security` | Profil + logs + alertes |
| POST | `/auth/update-profile` | Mise à jour profil |
| GET | `/security/logs` | Logs d'authentification |
| POST | `/security/ai/analyze-anomalies` | Analyse IA anomalies |
| POST | `/security/ai/predict-threats` | Prédiction menaces |
| POST | `/security/ai/recommendations` | Recommandations IA |

---

## 📦 Stack Technique

| Couche | Technologie |
|--------|-------------|
| Frontend | HTML5, CSS3, Vanilla JS |
| Backend | Python, FastAPI |
| Stockage | JSON (fichiers locaux) |
| Email | Brevo API |
| IA | Groq API (Mixtral) |
| Auth | JWT (PyJWT) |

---

## ✅ Points Forts pour Soutenance

1. **Souveraineté des données** - Tout en local (JSON)
2. **Sécurité multicouche** - MFA 2 facteurs (password + OTP)
3. **UX moderne** - Interface responsive, animations fluides
4. **Analyse IA** - Détection anomalies temps réel
5. **Logging complet** - Traçabilité tous les événements
6. **Gratuit / Open Source** - Stack 100% free

---

*Document généré pour soutenance - Security Web*
