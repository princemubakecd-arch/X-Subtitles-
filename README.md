# X-Subtitles AI — projet Android

Application Android native en Kotlin : lecteur vidéo Media3 + serveur IA FastAPI. Le prototype génère les sous-titres après analyse de la vidéo (pas en direct).

## Fonctions
- Sélection et lecture de vidéos locales.
- Lecture de liens média directs compatibles (MP4, WebM, HLS/M3U8 et DASH/MPD selon la source).
- Envoi du média au serveur IA.
- Extraction audio avec FFmpeg.
- Reconnaissance vocale japonaise avec faster-whisper.
- Traduction des segments en français et création d'une piste WebVTT synchronisée.

## 1. Démarrer le serveur IA
Prérequis : Python 3.11+, FFmpeg installé et une machine avec assez de mémoire (le modèle Whisper peut être lourd).

```bash
cd server
pip install -r requirements.txt
uvicorn main:app --host 0.0.0.0 --port 8000
```

Pour un modèle moins lourd :
```bash
WHISPER_MODEL=base uvicorn main:app --host 0.0.0.0 --port 8000
```

Le serveur et le téléphone doivent pouvoir communiquer. Dans l'application, renseigne l'adresse du serveur : par exemple `http://192.168.1.10:8000` si le serveur tourne sur un ordinateur du même Wi-Fi, ou une adresse HTTPS publique si le serveur est hébergé. N'utilise pas `10.0.2.2` sur un téléphone physique : cette adresse sert à l'émulateur Android.

**Important :** le serveur fourni est un prototype de développement. Ne l'expose pas publiquement sans authentification, limites de taille/durée, protection SSRF, quotas, nettoyage des fichiers et HTTPS. Les liens vidéo doivent être des URL directes accessibles ; aucun DRM ni contrôle d'accès n'est contourné.

## 2. Construire l'APK
Prérequis : Android Studio, JDK 17 et Android SDK (API 35).

1. Ouvre le dossier `X-Subtitles-AI` dans Android Studio.
2. Attends la synchronisation Gradle.
3. Choisis **Build > Build Bundle(s) / APK(s) > Build APK(s)**.
4. L'APK debug sera dans `app/build/outputs/apk/debug/app-debug.apk`.

### Compilation automatique avec GitHub Actions
Le dossier `.github/workflows` contient un workflow de compilation. Après avoir envoyé ce projet dans un dépôt GitHub, ouvre l'onglet **Actions**, lance le workflow **Build Android APK** et récupère `X-Subtitles-AI-debug-apk` dans les artefacts de l'exécution terminée.

## Limites connues
- Ce prototype analyse d'abord toute la vidéo : les sous-titres ne sont pas instantanés pendant la lecture.
- Le traitement IA s'effectue sur le serveur, pas entièrement hors ligne sur le téléphone.
- La lecture en ligne concerne les URL média directes compatibles, pas les pages de plateformes qui exigent une connexion ou un lecteur propriétaire.
- `deep-translator` est utilisé pour le prototype ; une version publique devrait intégrer un fournisseur de traduction fiable avec gestion des erreurs et des quotas.
- Utilise uniquement des vidéos que tu as le droit de traiter.
