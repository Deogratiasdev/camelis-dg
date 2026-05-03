# 🚀 Guide de Déploiement Client

## 📋 Configuration Système Requise

### Matériel Minimum
- **RAM** : 512 MB (recommandé 1GB)
- **Disque** : 2 GB libres
- **Processeur** : 1.5 GHz dual-core

### Logiciel
- **Python** : 3.8+ (recommandé 3.13)
- **OS** : Windows 10+, Ubuntu 18.04+, macOS 10.14+

---

## ⚡ Installation Rapide (5 minutes)

### 1. Téléchargement et Préparation
```bash
# Cloner le projet
git clone <repository_url>
cd "Projet securité"

# Accéder au backend
cd backend
```

### 2. Installation Automatisée
```bash
# Script d'installation complet
powershell -ExecutionPolicy Bypass -File install.ps1
```

### 3. Configuration
```bash
# Éditer le fichier .env
notepad .env

# Configurer au minimum :
VOSK_MODEL_PATH=models/vosk-model-small-fr-0.22
VOICE_LIGHTWEIGHT_MODE=true
GROQ_API_KEY=votre_clé_groq
SMTP_EMAIL=votre_email@gmail.com
SMTP_PASSWORD=votre_mdp_app
```

### 4. Démarrage
```bash
# Démarrer le serveur
python main.py

# Accéder à l'API : http://localhost:8000
# Documentation : http://localhost:8000/docs
```

---

## 🛠️ Installation Manuelle Détaillée

### Étape 1 : Environnement Python
```bash
# Créer l'environnement virtuel
python -m venv venv

# Activer l'environnement
# Windows
venv\Scripts\activate
# Linux/Mac
source venv/bin/activate

# Mettre à jour pip
python -m pip install --upgrade pip
```

### Étape 2 : Dépendances
```bash
# Installer les packages requis
pip install fastapi uvicorn python-multipart
pip install python-jose[cryptography] passlib[bcrypt]
pip install pyotp python-dotenv
pip install vosk soundfile numpy
pip install requests groq structlog
```

### Étape 3 : Modèles Vosk
```bash
# Créer le dossier des modèles
mkdir models
cd models

# Télécharger le modèle français (150MB)
Invoke-WebRequest -Uri "https://alphacephei.com/vosk/models/vosk-model-small-fr-0.22.zip" -OutFile "vosk-model-small-fr-0.22.zip"

# Décompresser
Expand-Archive -Path "vosk-model-small-fr-0.22.zip" -DestinationPath "."

# Nettoyer
Remove-Item "vosk-model-small-fr-0.22.zip"

# Retour au dossier backend
cd ..
```

### Étape 4 : Dossiers de Données
```bash
# Créer les dossiers nécessaires
mkdir data
mkdir logs
mkdir uploads
```

### Étape 5 : Configuration
```bash
# Générer les clés JWT
python generate_jwt_keys.py

# Configurer le fichier .env
copy .env.example .env
notepad .env
```

**Configuration minimale du .env :**
```env
# Mode léger pour RAM 512MB
VOICE_LIGHTWEIGHT_MODE=true
VOSK_MODEL_PATH=models/vosk-model-small-fr-0.22
VOSK_SPEAKER_MODEL_PATH=
VOICE_SIMILARITY_THRESHOLD=0.45

# Email (obligatoire pour OTP)
SMTP_EMAIL=votre_email@gmail.com
SMTP_PASSWORD=votre_mdp_app
SMTP_SERVER=smtp.gmail.com
SMTP_PORT=587

# API IA (obligatoire)
GROQ_API_KEY=gsk_votre_clé_groq
GROQ_MODEL=mixtral-8x7b-32768
```

---

## 🧪 Vérification Post-Installation

### Test de Santé
```bash
# Vérifier que le serveur fonctionne
curl http://localhost:8000/health

# Réponse attendue : {"status": "healthy", "timestamp": "..."}
```

### Test Complet de l'API
```bash
# Lancer les tests automatisés
python tests/test_api.py

# Résultat attendu : Tous les tests passés ✅
```

### Test Manuel
```bash
# Test d'inscription
curl -X POST "http://localhost:8000/auth/register" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "email=test@example.com&password=SecurePass123&first_name=Test&last_name=User"

# Test de documentation
# Ouvrir : http://localhost:8000/docs
```

---

## 🔧 Optimisation pour 512MB RAM

### Configuration Optimale
```env
# .env optimisé pour faible RAM
VOICE_LIGHTWEIGHT_MODE=true
VOICE_SIMILARITY_THRESHOLD=0.45
RATE_LIMIT_REQUESTS_PER_MINUTE=30
RATE_LIMIT_REQUESTS_PER_HOUR=500
```

### Surveillance Mémoire
```bash
# Sur Windows
tasklist | findstr python

# Sur Linux/Mac
ps aux | grep python
```

### Gestion Automatique
Le système gère automatiquement :
- **Chargement** des modèles Vosk uniquement lors de l'utilisation
- **Déchargement** après 5 minutes d'inactivité
- **Nettoyage** mémoire avec garbage collector

---

## 🌐 Configuration Production

### HTTPS avec Certbot
```bash
# Installation de Certbot
pip install certbot

# Génération certificat
certbot certonly --standalone -d votre-domaine.com

# Configuration HTTPS dans main.py
```

### Reverse Proxy Nginx
```nginx
server {
    listen 80;
    server_name votre-domaine.com;
    
    location / {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### Docker (Optionnel)
```dockerfile
FROM python:3.13-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .
EXPOSE 8000

CMD ["python", "main.py"]
```

---

## 📊 Monitoring et Maintenance

### Logs en Temps Réel
```bash
# Windows
Get-Content logs\auth_system.log -Wait

# Linux/Mac
tail -f logs/auth_system.log
```

### Statistiques d'Utilisation
```bash
# Vérifier l'API
curl http://localhost:8000/admin/stats

# Monitoring mémoire
curl http://localhost:8000/health
```

### Sauvegarde Automatique
```bash
# Script de sauvegarde quotidien
# backup.ps1
$date = Get-Date -Format "yyyy-MM-dd"
Copy-Item data\*.json "backup\$date\" -Force
```

---

## 🚨 Dépannage

### Problèmes Communs

#### "ModuleNotFoundError"
```bash
# Solution
pip install -r requirements.txt
```

#### "Port 8000 déjà utilisé"
```bash
# Solution
netstat -ano | findstr :8000
taskkill /PID <PID> /F
```

#### "Erreur 503 Service Unavailable"
```bash
# Vérifier les modèles
dir models

# Vérifier les permissions
icacls data /grant Everyone:F
```

#### "RAM insuffisante"
```bash
# Activer le mode léger
# Dans .env : VOICE_LIGHTWEIGHT_MODE=true

# Redémarrer
python main.py
```

### Support Avancé

#### Logs Détaillés
```bash
# Activer le mode debug
# Dans .env : DEBUG=true

# Voir les logs d'erreur
Select-String "ERROR" logs\auth_system.log
```

#### Performance
```bash
# Test de charge
python tests/load_test.py

# Optimisation automatique
python optimize.py
```

---

## 📞 Support et Documentation

### Documentation Complète
- **Routes API** : `ROUTES_DOCUMENTATION.md`
- **Commandes** : `COMMANDES.md`
- **Guide Projet** : `README_PROJET.md`
- **Workflow Agent** : `agent.md`

### Contact Support
1. **Vérifier les logs** : `logs/auth_system.log`
2. **Lancer les tests** : `python tests/test_api.py`
3. **Consulter la documentation** : `ROUTES_DOCUMENTATION.md`

### Mises à Jour
```bash
# Mettre à jour le projet
git pull origin main

# Mettre à jour les dépendances
pip install -r requirements.txt --upgrade

# Redémarrer le service
python main.py
```

---

## ✅ Checklist Déploiement

- [ ] Python 3.13 installé
- [ ] Environnement virtuel créé
- [ ] Dépendances installées
- [ ] Modèles Vosk téléchargés
- [ ] Dossiers créés (data, logs, uploads)
- [ ] Fichier .env configuré
- [ ] Clés JWT générées
- [ ] Serveur démarré
- [ ] Tests passés
- [ ] Documentation accessible

**Une fois cette checklist complétée, votre système MFA est opérationnel !** 🎉
