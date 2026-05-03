# 📋 Commandes Complètes du Projet MFA

## 🚀 Installation et Configuration

### 1. Création de l'environnement virtuel
```bash
# Windows
python -m venv venv
venv\Scripts\activate

# Linux/Mac
python3 -m venv venv
source venv/bin/activate
```

### 2. Installation des dépendances
```bash
pip install -r requirements.txt
```

### 3. Téléchargement des modèles Vosk
```bash
# Créer le dossier models
mkdir models
cd models

# Télécharger le modèle français principal
Invoke-WebRequest -Uri "https://alphacephei.com/vosk/models/vosk-model-small-fr-0.22.zip" -OutFile "vosk-model-small-fr-0.22.zip"

# Décompresser
Expand-Archive -Path "vosk-model-small-fr-0.22.zip" -DestinationPath "."

# Nettoyer
Remove-Item "vosk-model-small-fr-0.22.zip"

# Retour au dossier backend
cd ..
```

### 4. Création des dossiers nécessaires
```bash
mkdir data
mkdir logs
mkdir uploads
```

### 5. Génération des clés JWT
```bash
python generate_jwt_keys.py
```

### 6. Configuration du fichier .env
```bash
# Éditer le fichier .env avec les valeurs suivantes :
VOSK_MODEL_PATH=models/vosk-model-small-fr-0.22
VOSK_SPEAKER_MODEL_PATH=
VOICE_LIGHTWEIGHT_MODE=true
VOICE_SIMILARITY_THRESHOLD=0.45

# Email SMTP
SMTP_EMAIL=votre_email@gmail.com
SMTP_PASSWORD=votre_mdp_app

# API Groq
GROQ_API_KEY=gsk_votre_clé_groq
```

## 🖥️ Démarrage du Serveur

### Démarrage normal
```bash
python main.py
```

### Démarrage avec vérification complète
```bash
python start.py
```

### Démarrage en mode production
```bash
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

## 🧪 Tests

### Test complet de l'API
```bash
python tests/test_api.py
```

### Test individuel des endpoints
```bash
# Health check
curl http://localhost:8000/health

# Documentation Swagger
# Ouvrir dans le navigateur : http://localhost:8000/docs
```

## 📁 Gestion des Fichiers

### Structure des dossiers
```
backend/
├── data/                  # Fichiers JSON de stockage
│   ├── users.json
│   ├── sessions.json
│   ├── otp_codes.json
│   ├── auth_logs.json
│   ├── rate_limits.json
│   └── ai_analyses.json
├── models/               # Modèles Vosk
│   └── vosk-model-small-fr-0.22/
├── logs/                 # Logs d'application
├── uploads/              # Fichiers uploadés
└── .env                  # Configuration
```

### Nettoyage des données de test
```bash
# Supprimer les fichiers JSON de test
Remove-Item data\*.json

# Recréer les dossiers vides
mkdir data
mkdir logs
mkdir uploads
```

## 🔧 Maintenance

### Mise à jour des dépendances
```bash
pip install --upgrade pip
pip install -r requirements.txt --upgrade
```

### Sauvegarde des données
```bash
# Copier les fichiers JSON
Copy-Item data\*.json backup\ -Recurse -Force

# Compresser la sauvegarde
Compress-Archive backup\ backup_$(Get-Date -Format "yyyy-MM-dd").zip
```

### Vérification de l'utilisation mémoire
```bash
# Sur Windows
tasklist | findstr python

# Sur Linux/Mac
ps aux | grep python
```

## 🌐 Déploiement Client

### Pour le client/déploiement

#### 1. Prérequis système
- **RAM** : 512 MB minimum
- **Disque** : 2 GB libres
- **Python** : 3.8+ (recommandé 3.13)
- **OS** : Windows 10+, Linux, macOS

#### 2. Commandes d'installation complètes
```bash
# 1. Télécharger le projet
git clone <repository_url>
cd "Projet securité"

# 2. Aller dans le backend
cd backend

# 3. Créer l'environnement
python -m venv venv
venv\Scripts\activate

# 4. Installer les dépendances
pip install -r requirements.txt

# 5. Créer les dossiers
mkdir models
mkdir data
mkdir logs
mkdir uploads

# 6. Télécharger le modèle Vosk
cd models
Invoke-WebRequest -Uri "https://alphacephei.com/vosk/models/vosk-model-small-fr-0.22.zip" -OutFile "vosk-model-small-fr-0.22.zip"
Expand-Archive -Path "vosk-model-small-fr-0.22.zip" -DestinationPath "."
Remove-Item "vosk-model-small-fr-0.22.zip"
cd ..

# 7. Générer les clés JWT
python generate_jwt_keys.py

# 8. Configurer .env (manuellement)
# Éditer le fichier .env avec vos configurations

# 9. Démarrer le serveur
python main.py
```

#### 3. Vérification post-installation
```bash
# Vérifier que le serveur fonctionne
curl http://localhost:8000/health

# Lancer les tests
python tests/test_api.py
```

## 🚨 Dépannage

### Problèmes courants

#### ModuleNotFoundError
```bash
# Réinstaller les dépendances
pip install -r requirements.txt

# Vérifier l'environnement virtuel
which python
```

#### Erreur 503 Service Unavailable
```bash
# Vérifier que les modèles sont téléchargés
dir models

# Vérifier les permissions
icacls data /grant Everyone:F
```

#### Problèmes de mémoire
```bash
# Mode léger activé par défaut
# Vérifier .env : VOICE_LIGHTWEIGHT_MODE=true

# Redémarrer le serveur
python main.py
```

#### Port déjà utilisé
```bash
# Trouver le processus sur le port 8000
netstat -ano | findstr :8000

# Tuer le processus
taskkill /PID <PID> /F
```

## 📊 Monitoring

### Logs en temps réel
```bash
# Windows
Get-Content logs\auth_system.log -Wait

# Linux/Mac
tail -f logs/auth_system.log
```

### Statistiques du système
```bash
# Utilisation CPU/Mémoire
Get-Counter '\Processor(_Total)\% Processor Time'
Get-Counter '\Memory\Available MBytes'
```

## 🔐 Sécurité

### Configuration production
```bash
# Générer de nouvelles clés JWT
python generate_jwt_keys.py

# Mettre à jour les clés dans .env
# Changer les mots de passe par défaut
# Configurer HTTPS en production
```

### Audit des logs
```bash
# Voir les tentatives de connexion
Select-String "login" logs\auth_system.log

# Voir les erreurs
Select-String "ERROR" logs\auth_system.log
```

---

## 📞 Support

### En cas de problème
1. Vérifier les logs : `logs\auth_system.log`
2. Lancer les tests : `python tests/test_api.py`
3. Vérifier la configuration : `.env`
4. Consulter la documentation : `ROUTES_DOCUMENTATION.md`

### Contact
- Documentation complète : `ROUTES_DOCUMENTATION.md`
- Guide du projet : `README_PROJET.md`
- Agent workflow : `agent.md`
