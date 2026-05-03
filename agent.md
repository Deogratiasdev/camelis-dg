# Agent - Workflow Sécurité Bancaire Multi-Facteurs

## Sommaire

- [Contexte du Projet](#contexte-du-projet)
- [Objectif](#objectif)
- [Architecture du Système](#architecture-du-système)
  - [Couches d'Authentification](#1-couches-dauthentification)
  - [Flux de Données](#2-flux-de-données---schéma-détaillé)
  - [Gestion des Anomalies](#3-gestion-des-anomalies)
- [Workflow d'Inscription](#workflow-dinscription-enrôlement)
- [Analyse des Logs par IA](#analyse-des-logs-par-intelligence-artificielle)
  - [Prompts Système](#prompts-système-recommandes)
- [Solutions Gratuites Reconnaissance Vocale](#solutions-gratuites---guide-dutilisation)
- [Configuration Cloud](#configuration-machine-légère-cloud)
- [Ce que le Jury Attend](#ce-que-le-jury-attend-vs-terminal)
- [Pages de l'Application](#pages-de-lapplication-plan-complet)
- [Stack Unifiée Finale](#stack-unifiée-finale-à-implémenter)
- [Points Clés pour la Soutenance](#points-clés-pour-la-soutenance)
- [Résumé Exécutif](#résumé-exécutif)

---

## Contexte du Projet
Projet de soutenance portant sur un système de sécurité avec authentification multi-facteurs combinant mot de passe, OTP, reconnaissance vocale et analyse comportementale.

> **Note** : Le contexte "bancaire" illustre un cas d'usage critique, mais ce système de sécurité MFA est applicable à tout domaine nécessitant une protection forte (santé, gouvernement, finance, données personnelles).

## Objectif
Développer et présenter un workflow de sécurité robuste pour protéger les comptes bancaires contre les accès non autorisés.

## Architecture du Système

### 1. Couches d'Authentification
- **Niveau 1** : Identifiant + Mot de passe (vérification serveur)
- **Niveau 2** : OTP (SMS/Email) - vérification temporelle
- **Niveau 3** : Reconnaissance vocale (biométrie)
- **Niveau 4** : Analyse comportementale (appareil, localisation, heure)

### 2. Flux de Données - Schéma Détaillé

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    WORKFLOW AUTHENTIFICATION MULTI-FACTEURS                 │
└─────────────────────────────────────────────────────────────────────────────┘

    ┌─────────────┐
    │  UTILISATEUR │
    └──────┬──────┘
           │ Ouvre l'application
           ▼
    ┌─────────────────────┐
    │  1. SAISIE ID/MDP   │◄──────────────────────────────┐
    │     (Niveau 1)      │                               │
    └──────────┬──────────┘                               │
               │ Vérification serveur                     │
               ▼                                          │
           ┌──────┐                                       │
           │ OK ? │── Non ──►┌───────────────┐           │
           └──┬───┘          │  ÉCHEC +1     │           │
              │ Oui          │  (Max 3)      │           │
              │              └───────┬───────┘           │
              │                      │                   │
              ▼                      ▼                   │
    ┌─────────────────────┐   ┌─────────────┐            │
    │  2. ENVOI OTP       │   │ BLOCAGE     │            │
    │     (SMS/Email)     │   │ COMPTE      │            │
    └──────────┬──────────┘   └─────────────┘            │
               │                                           │
               ▼                                           │
    ┌─────────────────────┐                                │
    │  SAISIE OTP         │                                │
    │  (Code 6 chiffres)  │                                │
    └──────────┬──────────┘                                │
               │ Vérification temporelle                  │
               ▼                                           │
           ┌──────┐                                        │
           │ OK ? │── Non ──► Retour étape 1 (nouveau OTP)│
           └──┬───┘                                        │
              │ Oui                                       │
              ▼                                           │
    ┌─────────────────────┐                              │
    │  3. RECONNAISSANCE  │                              │
    │     VOCALE          │                              │
    │  (Phrase secrète)   │                              │
    └──────────┬──────────┘                              │
               │ Comparaison empreinte                  │
               ▼                                         │
           ┌──────┐                                      │
           │ OK ? │── Non ──► Fallback SMS/email admin   │
           └──┬───┘                                      │
              │ Oui                                      │
              ▼                                          │
    ┌─────────────────────┐                              │
    │  4. ANALYSE         │                              │
    │  COMPORTEMENTALE    │                              │
    │  • Appareil         │                              │
    │  • Localisation     │                              │
    │  • Heure            │                              │
    └──────────┬──────────┘                              │
               │ Machine Learning                         │
               ▼                                          │
           ┌──────┐                                       │
           │ OK ? │── Non ──► ALERTE + Vérification manuelle│
           └──┬───┘                                       │
              │ Oui                                      │
              ▼                                          │
    ┌─────────────────────┐                             │
    │   ✅ ACCÈS COMPTE   │                             │
    │   Espace sécurisé   │                             │
    └─────────────────────┘                             │
                                                        │
    ┌────────────────────────────────────────────────────┘
    │  GESTION ERREURS (toutes étapes)
    │  ├── 3 échecs → Blocage temporaire
    │  ├── Anomalie → Journalisation
    │  └── Suspicion → Alertes admin
    └─────────────────────────────────────────────────────┘
```

### 3. Gestion des Anomalies
- Journalisation des tentatives suspectes
- Blocage progressif (3 échecs = alerte)
- Notifications en temps réel

## Workflow d'Inscription (Enrôlement)

### Processus d'Inscription Multi-Facteurs

```
┌─────────────────────────────────────────────────────────────────┐
│                    INSCRIPTION NOUVEL UTILISATEUR               │
└─────────────────────────────────────────────────────────────────┘

    ┌─────────────┐
    │   VISITEUR  │
    └──────┬──────┘
           │
           ▼
    ┌─────────────────────┐
    │ 1. FORMULAIRE       │
    │    INSCRIPTION      │
    │  • Nom, Prénom      │
    │  • Email, Téléphone │
    │  • Mot de passe     │
    └──────────┬──────────┘
               │ Vérification email/tel
               ▼
    ┌─────────────────────┐
    │ 2. CONFIGURATION    │
    │    AUTHENTIFICATION │
    │                     │
    │ ┌───────────────┐   │
    │ │ 2FA (Oblig.)  │   │
    │ │ • App Auth    │   │
    │ │ • SMS/Email   │   │
    │ └───────────────┘   │
    │                     │
    │ ┌───────────────┐   │
    │ │ 3. EMPREINTE  │   │
    │ │    VOCALE     │   │
    │ │ • Enregistre  │   │
    │ │   3 phrases   │   │
    │ │ • Test live   │   │
    │ └───────────────┘   │
    │                     │
    │ ┌───────────────┐   │
    │ │ 4. APPAREILS  │   │
    │ │    CONNUS     │   │
    │ │ • Enregistre  │   │
    │ │   device ID   │   │
    │ │ • Navigateur  │   │
    │ └───────────────┘   │
    └──────────┬──────────┘
               │
               ▼
    ┌─────────────────────┐
    │ ✅ COMPTE ACTIVÉ    │
    │    Premier login    │
    └─────────────────────┘
```

### Détail des Étapes d'Inscription

| Étape | Action | Données Stockées | Sécurité |
|-------|--------|------------------|----------|
| **1. Formulaire** | Création identifiants | Hash bcrypt (MDP), email chiffré | Validation email par lien |
| **2. 2FA Setup** | Choix méthode OTP | Clé secrète TOTP ou numéro SMS | QR code pour app auth |
| **3. Voix** | Enregistrement biométrique | Vecteur vocal (embedding 512-dim) | Chiffrement AES-256 |
| **4. Appareils** | Associer devices de confiance | Fingerprint (OS, navigateur, résolution) | Cookie sécurisé + localStorage |

### Stockage Sécurisé Empreinte Vocale
- **Format** : Embedding numérique (pas le fichier audio brut)
- **Chiffrement** : AES-256 avec clé dérivée du mot de passe maitre
- **Isolation** : Stockage séparé des données personnelles (RGPD)
- **Consentement** : Acceptation explicite avec révocabilité

## Analyse des Logs par Intelligence Artificielle

### Objectif
L'IA analyse les logs d'authentification pour détecter des patterns, prédire les attaques et suggérer des améliorations de sécurité en temps réel.

### Prompts Système Recommandes

#### Prompt 1 : Détection d'Anomalies
```
Tu es un expert en cybersécurité bancaire. Analyse ces logs d'authentification et identifie :
1. Les tentatives suspectes (brute-force, accès inhabituels)
2. Les patterns de comportement anormaux
3. Les failles potentielles exploitées

Format de réponse :
- [ALERTE] : Description + niveau de risque (Haut/Moyen/Faible)
- [ANALYSE] : Pattern détecté avec preuves
- [RECOMMANDATION] : Action corrective immédiate

Logs à analyser :
{{logs_json}}
```

#### Prompt 2 : Prédiction de Menaces
```
Tu es un analyste en threat intelligence. Sur la base des logs historiques :
1. Identifie les tendances d'attaques émergentes
2. Prédit les prochains vecteurs d'attaque probables
3. Propose des règles de prévention proactives

Format : Tableau avec | Menace | Probabilité | Mitigation |

Historique : {{logs_7jours}}
```

#### Prompt 3 : Optimisation Continue
```
Tu es architecte sécurité. Analyse la performance du système MFA :
1. Quels facteurs causent le plus de friction UX ?
2. Quelles étapes sont contournées ou échouent le plus ?
3. Suggère 3 améliorations concrètes avec ROI sécurité/UX

Données : {{metrics_auth}} (taux succès, temps par étape, abandons)
```

### Ce que l'IA peut faire avec les Logs

| Fonction | Description | Valeur Ajoutee |
|----------|-------------|----------------|
| **Détection temps réel** | Alertes instantanées sur anomalies | Réaction sous 1s vs 15min (humain) |
| **Correlation avancée** | Lie des événements dispersés | Détecte les attaques distribuées |
| **Prédiction** | Anticipe les attaques avant qu'elles ne surviennent | Prévention proactive |
| **Suggestions auto** | Recommande règles de sécurité | Amélioration continue sans intervention |
| **Rapports intelligents** | Synthèse actionable pour administrateurs | Décision rapide basée sur données |

### Exemple d'Amélioration Suggérée par IA

```
[ALERTE IA] Pattern détecté : Échecs OTP groupés (10+ en 2min)
↳ Source : IPs similaires (même ASN)
↳ Hypothèse : Attaque par SIM swapping coordonnée

[SUGGESTION IA] Règle à implémenter :
- Limiter OTP : max 3/envoi par 5min
- Bloquer ASN suspect après 5 échecs
- Activer vérification vocale forcée pour ces cas

[IMPACT] Réduction risque estimée : 78%
```

## Technologies Suggérées

### Stack Principale
- **Backend** : Node.js/Python avec JWT
- **Stockage** : Fichiers JSON structurés (local)
- **Analyse comportementale** : Machine Learning (scikit-learn/TensorFlow)
- **Analyse logs IA** : LLM (GPT-4/Claude) ou NLP spécialisé (Splunk AI, Azure Sentinel)
- **Stockage logs** : Fichiers JSON avec timestamps ISO

### Reconnaissance Vocale - Solutions Recommandées

| Solution | Type | Prix | Forces | Inconvénients |
|----------|------|------|--------|---------------|
| **Microsoft Azure Speaker Recognition** | Cloud API | Pay-per-use | Haute précision, SDK complet | Dépendance cloud |
| **Amazon Connect Voice ID** | Cloud API | Pay-per-use | Intégration AWS, anti-spoofing | Vendor lock-in |
| **SpeechBrain (open-source)** | Self-hosted | Gratuit | Contrôle total, privacy-friendly | Nécessite expertise ML |
| **Vosk + Custom Model** | Self-hosted | Gratuit | Léger, offline | Moins précis, dev manuel |

**Recommandation** : Azure Speaker Recognition pour POC/soutenance (rapidité), SpeechBrain pour production (souveraineté données).

#### Architecture Détection Vocale
```
Enregistrement micro → Découpage (3 phrases) → Extraction features (MFCC) 
         → Embedding 512-dim → Comparaison cosinus (seuil 0.85) → Decision
```

## Solutions Gratuites - Guide d'Utilisation

### 1. SpeechBrain (Recommandé)
Framework Python open-source basé sur PyTorch. Haute précision, pré-entraîné sur VoxCeleb.

**Installation**
```bash
pip install speechbrain
```

**Code - Enregistrement voix (inscription)**
```python
from speechbrain.pretrained import EncoderClassifier
import torch

# Charger modèle pré-entraîné
classifier = EncoderClassifier.from_hparams(
    source="speechbrain/ecapa-tdnn"
)

def enregistrer_empreinte(chemin_audio):
    """Extrait l'embedding vocal (512 dimensions)"""
    signal, fs = torchaudio.load(chemin_audio)
    embedding = classifier.encode_batch(signal)
    return embedding.squeeze().numpy()  # Vecteur 512-dim

# Sauvegarder pour utilisateur
empreinte = enregistrer_empreinte("user_phrase.wav")
np.save(f"empreintes/user_{id}.npy", empreinte)
```

**Code - Vérification voix (authentification)**
```python
from scipy.spatial.distance import cosine

def verifier_voix(audio_test, empreinte_stockee, seuil=0.15):
    """
    Compare la voix test avec l'empreinte enregistrée
    seuil: plus petit = plus strict (0.15 = 85% similarité)
    """
    emb_test = enregistrer_empreinte(audio_test)
    distance = cosine(emb_test, empreinte_stockee)
    
    return {
        "match": distance < seuil,
        "score": 1 - distance,  # 0-1, 1 = parfait
        "confiance": "haute" if distance < 0.1 else "moyenne"
    }
```

**Avantages** : Précision 99%+ sur VoxCeleb, modèle ECAPA-TDNN state-of-the-art

---

### 2. Vosk (Léger, Offline)
Reconnaissance vocale offline, rapide, fonctionne même sur Raspberry Pi.

**Installation**
```bash
pip install vosk
# Télécharger modèle: https://alphacephei.com/vosk/models
```

**Code - Identification locuteur simple**
```python
from vosk import Model, SpkModel, KaldiRecognizer
import json

# Charger modèle général + modèle locuteur
model = Model("vosk-model-fr-0.22")
spk_model = SpkModel("vosk-model-spk-0.4")

rec = KaldiRecognizer(model, 16000)
rec.SetSpkModel(spk_model)

def extraire_empreinte_vosk(audio_path):
    with open(audio_path, "rb") as f:
        data = f.read()
        rec.AcceptWaveform(data)
    
    result = json.loads(rec.FinalResult())
    # Vecteur 128 ou 256 dimensions selon modèle
    return result.get("spk", [])  # Embedding locuteur

def comparer_vosk(vec1, vec2, seuil=0.5):
    """Distance euclidienne normalisée"""
    import numpy as np
    dist = np.linalg.norm(np.array(vec1) - np.array(vec2))
    return dist < seuil
```

**Avantages** : 50MB seulement, marche offline, rapide (temps réel)

---

### 3. Mozilla DeepSpeech + pyAudioAnalysis (Combinaison)
DeepSpeech pour transcription, pyAudioAnalysis pour features vocales.

**Installation**
```bash
pip install deepspeech pyAudioAnalysis
```

**Code - Approche feature-based**
```python
from pyAudioAnalysis import ShortTermFeatures as stf
import numpy as np

def extraire_features(chemin_audio):
    """Extrait 34 features audio (MFCC, zéro-crossing, énergie...)"""
    fs, x = stf.read_audio_file(chemin_audio)
    f, fn = stf.feature_extraction(x, fs, 
                                    int(0.050*fs),  # 50ms fenêtre
                                    int(0.025*fs))  # 25ms pas
    # f = matrice (features x frames), on moyenne sur le temps
    features_moy = np.mean(f, axis=1)
    return features_moy  # Vecteur 34-dim

def similarite_features(feat1, feat2):
    """Correlation entre deux signatures vocales"""
    return np.corrcoef(feat1, feat2)[0,1]  # -1 à 1, 1 = identique

# Usage
ref = extraire_features("reference.wav")
test = extraire_features("test.wav")
score = similarite_features(ref, test)
authentifie = score > 0.80  # Seuil 80% correlation
```

**Avantages** : Très léger, comprend le français, pas de GPU requis

---

### Comparaison Pratique

| Critère | SpeechBrain | Vosk | DeepSpeech+pyAudio |
|---------|-------------|------|-------------------|
| **Précision** | 99% | 85% | 75% |
| **Taille** | 400MB | 50MB | 100MB |
| **GPU** | Recommandé | Non | Non |
| **Langue FR** | Oui | Oui | Oui |
| **Setup** | Facile | Facile | Moyen |
| **Souveraineté** | Totale | Totale | Totale |

**Conseil** : SpeechBrain si tu as un GPU/serveur, Vosk si tu veux du edge/mobile.

## Configuration Machine Légère (Cloud)

### Recommandation pour Cloud/Instance Légère

Sur une machine cloud sans GPU (t2.micro AWS, e2-medium GCP, ou VPS basique) :

| Solution | RAM | CPU | Latence | Choix |
|----------|-----|-----|---------|-------|
| **Vosk** | 200MB | 1 cœur | 200ms | **✅ Recommandé** |
| SpeechBrain CPU | 2GB | 2 cœurs | 2s | Possible mais lent |
| Azure API | 0 | 0 | 500ms | Payant mais zéro charge |

### Setup Vosk sur Cloud

**Dockerfile (léger)**
```dockerfile
FROM python:3.9-slim

WORKDIR /app

# Dépendances minimales
RUN pip install --no-cache-dir vosk numpy scipy

# Télécharger modèle Vosk FR (50MB)
RUN apt-get update && apt-get install -y wget \
    && wget https://alphacephei.com/vosk/models/vosk-model-small-fr-0.22.zip \
    && unzip vosk-model-small-fr-0.22.zip \
    && rm vosk-model-small-fr-0.22.zip \
    && apt-get remove -y wget && apt-get autoremove -y

COPY . .
CMD ["python", "app.py"]
```

**docker-compose.yml**
```yaml
version: '3.8'
services:
  auth-service:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - ./empreintes:/app/empreintes  # Persiste données
    environment:
      - VOSK_MODEL_PATH=/app/vosk-model-small-fr-0.22
      - SEUIL_VOIX=0.45
    # Limites ressources (cloud léger)
    deploy:
      resources:
        limits:
          cpus: '1.0'
          memory: 512M
```

**Script FastAPI (app.py)**
```python
from fastapi import FastAPI, UploadFile, HTTPException
from vosk import Model, SpkModel, KaldiRecognizer
import numpy as np
import json

app = FastAPI()

# Chargement unique au démarrage
model = Model("vosk-model-small-fr-0.22")
spk_model = SpkModel("vosk-model-spk-0.4")

@app.post("/inscription/{user_id}")
async def inscription(user_id: str, audio: UploadFile):
    """Enregistre empreinte vocale"""
    data = await audio.read()
    
    rec = KaldiRecognizer(model, 16000)
    rec.SetSpkModel(spk_model)
    rec.AcceptWaveform(data)
    
    result = json.loads(rec.FinalResult())
    empreinte = result.get("spk")
    
    if not empreinte:
        raise HTTPException(400, "Voix non détectée")
    
    # Sauvegarde
    np.save(f"empreintes/{user_id}.npy", empreinte)
    return {"status": "ok", "dimensions": len(empreinte)}

@app.post("/auth/{user_id}")
async def authentifier(user_id: str, audio: UploadFile, seuil: float = 0.45):
    """Vérifie empreinte vocale"""
    # Charge empreinte stockée
    try:
        empreinte_ref = np.load(f"empreintes/{user_id}.npy")
    except:
        raise HTTPException(404, "Utilisateur inconnu")
    
    # Extrait empreinte test
    data = await audio.read()
    rec = KaldiRecognizer(model, 16000)
    rec.SetSpkModel(spk_model)
    rec.AcceptWaveform(data)
    
    result = json.loads(rec.FinalResult())
    empreinte_test = result.get("spk")
    
    if not empreinte_test:
        return {"auth": False, "raison": "voix_non_detectee"}
    
    # Comparaison
    dist = np.linalg.norm(np.array(empreinte_test) - empreinte_ref)
    authentifie = dist < seuil
    
    return {
        "auth": authentifie,
        "distance": float(dist),
        "seuil": seuil,
        "confiance": "haute" if dist < 0.3 else "moyenne" if dist < 0.5 else "faible"
    }
```

**Test avec curl**
```bash
# Inscription
curl -X POST -F "audio=@phrase.wav" http://localhost:5000/inscription/user123

# Auth
curl -X POST -F "audio@test.wav" "http://localhost:5000/auth/user123?seuil=0.45"
```

### Coût Cloud Estimé (Vosk)

| Provider | Instance | RAM/CPU | Coût/mois | Usage |
|----------|----------|---------|-----------|-------|
| AWS t3.micro | 1GB/1vcpu | ~15€ | Développement |
| Scaleway DEV1-S | 2GB/2vcpu | ~5€ | Test léger |
| OVH VPS Starter | 2GB/1vcpu | ~4€ | Production mini |
| Heroku (dyno) | 512MB | Gratuit (sleep) | Demo/PoC |

> **Tips** : Vosk marche très bien sur Heroku gratuit (512MB) pour une démo de soutenance.

## Ce que le Jury Attend (vs Terminal)

### ❌ Terminal Seul - À Éviter
```bash
$ python auth.py --user toto --voice test.wav
Authentification: OK
```
**Problème** : Peu démonstratif, jury non-technique ne comprend pas, pas d'UX visible.

### ✅ Site Web Foncier - Recommandé

Le jury veut voir :

| Élément | Pourquoi ça impressionne | Technologie |
|---------|--------------------------|-------------|
| **Interface utilisateur** | Démontre les 4 étapes MFA visuellement | React/Vue.js + API |
| **Dashboard admin** | Logs IA en temps réel, graphiques | Chart.js/D3.js |
| **Notifications temps réel** | Alertes instantanées (toast) | WebSocket |
| **Animations fluides** | Transitions entre étapes MFA | CSS/Framer Motion |
| **Responsive** | Fonctionne sur téléphone (démo vocale) | PWA |

### Architecture Recommandée pour Soutenance

```
Frontend (React)          Backend (FastAPI)         Services
┌──────────────┐         ┌──────────────┐          ┌─────────┐
│  Login Form  │ ──────► │  Auth API    │ ◄────── │  Vosk   │
│  OTP Input   │         │  MFA Logic   │          │  (voix) │
│  Mic Button  │ ──────► │  Log Storage │          └─────────┘
└──────────────┘         └──────────────┘          ┌─────────┐
       │                        │                  │  IA     │
       ▼                        ▼                  │  Logs   │
┌──────────────┐         ┌──────────────┐          └─────────┘
│  Dashboard   │ ◄────── │  SSE/WebSocket│
│  (real-time) │         │  (streaming)  │
└──────────────┘         └──────────────┘
```

### Stack Soutenance "Coup de Coeur"

**Frontend**
```bash
# React + TypeScript + Tailwind (rapide, beau, moderne)
npx create-react-app frontend --template typescript
npm install @headlessui/react chart.js axios
```

**Backend**
```python
# FastAPI + Socket.io pour temps réel
pip install fastapi[all] python-socketio redis
```

### Pages de l'Application (Plan Complet)

1. **Page d'Accueil** (`index.html`)
   - Présentation du système MFA
   - Bouton "Se connecter" / "S'inscrire"
   - Design minimaliste, ultra clean

2. **Page Inscription** (`signup.html`)
   - Formulaire : email, mot de passe
   - Enregistrement voix (3 phrases)
   - Enregistrement appareil de confiance
   - Feedback visuel par étape

3. **Page Login MFA** (`login.html`) - 4 étapes
   - Étape 1 : Identifiant + Mot de passe
   - Étape 2 : Saisie OTP (envoyé par email)
   - Étape 3 : Reconnaissance vocale (microphone)
   - Étape 4 : Analyse comportementale (auto)
   - Progress bar (1/4 → 4/4)
   - Feedback visuel par étape (✓/✗)

4. **Page User** (`user.html`) - Après auth
   - Profil utilisateur
   - Historique des connexions
   - Statistiques sécurité
   - Bouton déconnexion

5. **Page Config** (`config.html`)
   - Modifier mot de passe
   - Mettre à jour empreinte vocale
   - Gérer appareils de confiance
   - Paramètres 2FA (activer/désactiver)

6. **Page Logs** (`logs.html`)
   - Tableau des logs d'authentification
   - Filtres par date, statut, utilisateur
   - Coloration : succès (vert), échec (rouge), anomalie (orange)
   - Export CSV

7. **Page IA Analyse** (`ai-analysis.html`)
   - Interface pour demander analyse à l'IA
   - Entrée : logs ou description de scénario
   - Sortie : alertes, recommandations, suggestions
   - Historique des analyses IA

### Timing Démo (10 minutes)

| Minute | Action | Page |
|--------|--------|------|
| 0-2 | Inscription nouvel utilisateur | Signup |
| 2-5 | Login MFA complet (4 étapes) | Login |
| 5-8 | Dashboard temps réel | Admin |
| 8-10 | Simulation attaque + alerte IA | Logs |

### Déployer Gratuitement (pour jury)

| Service | Usage | URL |
|---------|-------|-----|
| Vercel | Frontend React | https://mon-projet.vercel.app |
| Render | Backend FastAPI | https://mon-api.onrender.com |
| Local | Stockage JSON | Fichiers structurés locaux |

> **Conseil crucial** : Avoir le site **déployé et accessible** avant la soutenance. Le jury préfère une URL fonctionnelle à du localhost.

## Architecture Proposée (Validée)

### Stack Complète Recommandée

| Composant | Service | Pourquoi | Coût |
|-----------|---------|----------|------|
| **Frontend** | Cloudflare Pages | CDN global, SSL auto, gratuit | Gratuit |
| **Backend** | Render (Python) | FastAPI support natif, déploiement auto | Gratuit |
| **Analyse IA** | Groq API | LLM ultra-rapide (Mixtral, Llama), pas cher | ~0.001$/1K tokens |
| **Email OTP** | Python `smtplib` / SendGrid | Simple, fiable, gratuit (100/jour SendGrid) | Gratuit |
| **Stockage** | Fichiers JSON | Structuré, lisible, pas de dépendance | Gratuit |

---

## Stack Unifiée Finale (À Implémenter)

### Architecture Unique et Cohérente

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         STACK MFA SÉCURITÉ                              │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ┌──────────────┐         ┌──────────────────┐         ┌──────────────┐  │
│  │   FRONTEND   │ ──────► │     BACKEND      │ ◄──── │   SERVICES   │  │
│  │              │  HTTPS  │                  │         │              │  │
│  │ Cloudflare   │         │ Render (FastAPI) │         │ • Vosk       │  │
│  │ Pages        │ ◄────── │                  │ ──────► │   (voix)     │  │
│  │ (React)      │  JSON   │ • Auth MFA       │         │ • Groq       │  │
│  └──────────────┘         │ • Analyse logs   │ ◄────── │   (logs IA)  │  │
│                           │ • OTP smtplib    │         │              │  │
│                           └────────┬─────────┘         └──────────────┘  │
│                                    │                                     │
│                                    ▼                                     │
│                           ┌──────────────┐                               │
│                           │  JSON        │                               │
│                           │  (fichiers)   │                               │
│                           └──────────────┘                               │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Stack Détaillée (Une Seule)

| Couche | Technologie | Service Cloud | Rôle |
|--------|------|---------|------|
| **Frontend** | HTML + CSS + JS (vanilla, design minimaliste, ultra clean, fluide) | **Cloudflare Pages** | Interface MFA (4 étapes), Dashboard |
| **Backend** | Python FastAPI | **Render** | API auth, analyse, notifications |
| **Stockage** | **Fichiers JSON** | **Local sur Render** | Users, empreintes, logs (fichiers structurés) |
| **Reconnaissance vocale** | Vosk (Python) | **Même Render** | Extraction/Comparaison empreintes |
| **Analyse IA** | Groq API | **API externe** | Anomaly detection, suggestions |
| **Email OTP** | smtplib (std lib) | **Via backend** | Envoi codes (accepte spambox) |
| **Stockage fichiers** | **Disque local** | **Render** | Audio temporaire, empreintes `.npy` |

### Pourquoi Cette Stack Unifiée ?

1. **Cloudflare Pages** : Gratuit, CDN rapide, intégration Git
2. **Render** : Python natif, déploiement auto depuis GitHub
3. **JSON** : Stockage fichiers structurés, zéro configuration, lisible pour soutenance
4. **Vosk** : Sur Render (même instance), pas de service externe
5. **Groq** : Seul service payant (usage faible = quasi gratuit)
6. **smtplib** : Pas de dépendance externe, bibliothèque standard

### Flux de Données Unifié

1. INSCRIPTION
User (Cloudflare) → POST /inscription → Render (FastAPI)
                                         ↓
                                    ┌──────────┐
                                    │  Vosk    │ ← Enregistre voix
                                    └────┬─────┘
                                         ↓
                                    JSON (save empreinte)

2. AUTHENTIFICATION MFA
User (Cloudflare) → POST /auth/step1 (ID/MDP)
                  ← 200 OK
                  → POST /auth/step2 (OTP) → smtplib envoie email
                  ← 200 OK
                  → POST /auth/step3 (voix) → Vosk compare
                  ← 200 OK
                  → POST /auth/step4 (analyse) → ML local
                  ← 200 OK + Token JWT

3. ANALYSE IA (async)
Render (cron) → Récupère logs → Groq analyse → Sauvegarde alertes
                                        ↓
                              WebSocket → Dashboard temps réel
```

### Code JSON (Backend) - Simple

```python
import json
import numpy as np
from pathlib import Path

# Stockage JSON (crée les fichiers s'ils n'existent pas)
data_dir = Path("data")
data_dir.mkdir(exist_ok=True)

# Fichiers de données
users_file = data_dir / "users.json"
sessions_file = data_dir / "sessions.json"

# Charger les données
def load_json(file_path):
    if file_path.exists():
        with open(file_path, 'r') as f:
            return json.load(f)
    return []

def save_json(file_path, data):
    with open(file_path, 'w') as f:
        json.dump(data, f, indent=2)

# Exemple d'utilisation
users = load_json(users_file)
new_user = {
    "id": "user123",
    "email": "user@example.com",
    "password_hash": "sha256_hash",
    "voice_embedding": [0.1, 0.2, 0.3],
    "created_at": "2024-01-01T00:00:00Z"
}
users.append(new_user)
save_json(users_file, users)
```

# Stockage empreinte (JSON)
embedding_json = json.dumps(empreinte.tolist())
users = load_json(users_file)
for user in users:
    if user['id'] == user_id:
        user['voice_embedding'] = json.loads(embedding_json)
        break
save_json(users_file, users)

# Lecture empreinte
users = load_json(users_file)
for user in users:
    if user['id'] == user_id:
        empreinte = np.array(user['voice_embedding'])
        break
```

### Pourquoi Groq ?

Groq est une API LLM avec des **LPU (Language Processing Units)** propriétaires :
- **Vitesse** : 500 tokens/sec (vs 50 pour OpenAI)
- **Prix** : 10x moins cher que GPT-4
- **Modèles** : Mixtral 8x7B, Llama 3 70B, Gemma
- **Usage** : Parfait pour analyse logs en temps réel

**Exemple code Groq (Python)**
```python
from groq import Groq

client = Groq(api_key="gsk_...")

def analyser_logs_ia(logs_json):
    completion = client.chat.completions.create(
        model="mixtral-8x7b-32768",
        messages=[{
            "role": "system",
            "content": """Tu es un expert cybersécurité. Analyse ces logs et détecte anomalies.
            Réponds en JSON: {"alertes": [], "risque": "haut|moyen|faible", "actions": []}"""
        }, {
            "role": "user",
            "content": f"Logs: {logs_json}"
        }],
        temperature=0.1,
        response_format={"type": "json_object"}
    )
    return json.loads(completion.choices[0].message.content)
```

### Email OTP - Solutions Python

**Option 1 : smtplib (Gratuit, ton propre SMTP)**
```python
import smtplib
from email.mime.text import MIMEText

def envoyer_otp_smtp(destinataire, code_otp):
    """Utilise Gmail, Outlook, ou ton serveur SMTP"""
    msg = MIMEText(f"Votre code OTP : {code_otp}")
    msg['Subject'] = 'Code de vérification'
    msg['From'] = 'ton-email@gmail.com'
    msg['To'] = destinataire
    
    with smtplib.SMTP_SSL('smtp.gmail.com', 465) as server:
        server.login('ton-email@gmail.com', 'mot-de-passe-app')
        server.send_message(msg)
    return True
```
> Note : Nécessite un "mot de passe d'application" si Gmail (pas ton MDP normal)

**Option 2 : SendGrid (Recommandé, 100 emails/jour gratuit)**
```python
from sendgrid import SendGridAPIClient
from sendgrid.helpers.mail import Mail

def envoyer_otp_sendgrid(destinataire, code_otp):
    sg = SendGridAPIClient("SG.xxx...")  # API key
    mail = Mail(
        from_email='auth@ton-projet.com',
        to_emails=destinataire,
        subject='Code de vérification MFA',
        html_content=f'<h1>{code_otp}</h1><p>Ce code expire dans 5 minutes.</p>'
    )
    response = sg.send(mail)
    return response.status_code == 202
```

**Option 3 : Resend (Moderne, excellente délivrabilité)**
```python
import resend

resend.api_key = "re_..."

def envoyer_otp_resend(destinataire, code_otp):
    r = resend.Emails.send({
        "from": "auth@ton-domaine.com",
        "to": destinataire,
        "subject": "Code de vérification",
        "html": f"<h2>{code_otp}</h2>"
    })
    return r["id"] is not None
```

### Comparaison Email

| Service | Prix | Facilité | Fiabilité | Limite |
|---------|------|----------|-----------|--------|
| **smtplib (Gmail)** | Gratuit | Moyen | Moyen (spambox) | 100/jour |
| **SendGrid** | Gratuit (100/j) | Facile | Excellente | 100/jour |
| **Resend** | Gratuit (100/j) | Très facile | Excellente | 100/jour |
| **Mailgun** | Payant | Facile | Excellente | Illimité |

**Recommandation** : SendGrid ou Resend pour la soutenance (déliverabilité garantie).

## Points Clés pour la Soutenance

### Forces du Projet
1. Défense en profondeur (4 facteurs)
2. Détection proactive des fraudes
3. Expérience utilisateur fluide malgré la sécurité

### Défis à Anticiper
1. **Confidentialité** : Stockage sécurisé des empreintes vocales (RGPD)
2. **Disponibilité** : Fallback si reconnaissance vocale échoue
3. **Performance** : Latence acceptable malgré les vérifications multiples

### Questions Probables du Jury
- Comment gérer les faux positifs en reconnaissance vocale ?
- Quelle stratégie de chiffrement pour les données sensibles ?
- Comment scaler le système avec millions d'utilisateurs ?

## Recommandations Techniques

### Améliorations à Implémenter
1. **Session Management** : Timeout après inactivité, tokens révocables
2. **Audit Trail** : Logs immuables pour investigations
3. **Rate Limiting** : Protection brute-force sur chaque étape
4. **Zero Trust** : Vérification continue même après connexion

### Sécurité Supplémentaire
- Chiffrement AES-256 pour données au repos
- TLS 1.3 pour communications
- HSM (Hardware Security Module) pour clés sensibles

## Plan de Présentation (15 min)
1. **Introduction** (2 min) : Contexte et enjeux
2. **Architecture** (4 min) : Les 4 couches de sécurité
3. **Démo** (5 min) : Parcours utilisateur complet
4. **Sécurité** (3 min) : Gestion des anomalies et conformité
5. **Q&A** (1 min)

## Glossaire Technique
- **OTP** : One-Time Password
- **MFA** : Multi-Factor Authentication
- **Biométrie** : Caractéristiques physiques/ comportementales uniques
- **Anomalie** : Déviation du comportement habituel

---

## Résumé Exécutif

### Projet en 3 Phrases
Système d'authentification multi-facteurs (4 niveaux) combinant mot de passe, OTP, reconnaissance vocale et analyse comportementale, avec IA intégrée pour détection proactive des menaces. Stack technique minimaliste : HTML + CSS + JS (vanilla) sur Cloudflare Pages, FastAPI + JSON + Vosk sur Render. Coût total : **gratuit** (sauf usage Groq négligeable).

### Stack Finale (6 Composants)

| Couche | Tech | Service | Rôle |
|--------|------|---------|------|
| Frontend | HTML + CSS + JS | **Cloudflare Pages** | Interface MFA 4 étapes |
| Backend | Python FastAPI | **Render** | API auth, OTP, analyse |
| Database | Fichiers JSON | **Local** | Users, logs, empreintes |
| Voix | Vosk | **Même Render** | Reconnaissance biométrique |
| IA | Groq API | **Externe** | Analyse logs, suggestions |
| Email | smtplib | **Backend** | OTP (accepte spambox) |

### Points Forts pour le Jury

✅ **Souveraineté** : Tout local (sauf Groq), données sur disque  
✅ **Simplicité** : Fichiers JSON, pas de PostgreSQL complexe  
✅ **Innovation** : IA temps réel pour analyse comportementale  
✅ **Performance** : Vosk 200ms latence sur instance légère  
✅ **Coût** : Gratuit pour démo soutenance

### Livrables Attendus

1. **Frontend déployé** sur Cloudflare (URL accessible)
2. **Backend** sur Render avec JSON + Vosk
3. **Démo 10 min** : Inscription → Auth MFA (4 étapes) → Dashboard IA

### Commandes Clés (Mémo)

```bash
# Deploy frontend
cd frontend && npm run build && npx wrangler pages deploy dist

# Deploy backend
git push origin main  # Render auto-deploy

# Test API
curl -X POST https://ton-api.onrender.com/auth/voice -F "audio=@test.wav"
```

---

*Document préparé pour la soutenance - Version finale unifiée*
