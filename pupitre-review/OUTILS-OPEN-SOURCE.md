# Outils open source utiles pour Pupitre (8 oct 2026)

Licences citées de mémoire : à revérifier sur le dépôt de chaque projet avant de livrer. Règle : rien de GPL/AGPL/NC dans le paquet vendu.

## 1. Ce qui débloque le plus, tout de suite

| Outil | Licence | Pour quoi | Comment |
|---|---|---|---|
| **Dagstuhl ChoirSet**, **Choral Singing Dataset**, **Schubert Winterreise Dataset** | CC BY (à vérifier) | Une vérité de calage écrite par des chercheurs, pas par l'aligneur ni par Léo. Chœurs et voix solistes avec partition alignée. | Ne pas livrer : seulement pour mesurer. Remplace les références « faites à l'œil par Claude » dans `tools/tests/fixtures/calage_refs/`. |
| **mir_eval** | MIT | Métriques standard (écart d'onset, précision dans ±50 ms) au lieu des règles maison qui changent. | Appeler `mir_eval.onset` / `alignment` dans `bench_calage.py`. |
| **Silero VAD** | MIT | Savoir où la voix chante vraiment. C'est la brique du contrôle « la partition continue après la fin de l'enregistrement ». | Petit modèle ONNX ; donne t_première_voix / t_dernière_voix pour l'objet `quality`. |
| **MuseScore 4** (programme externe) | GPL | Corriger la partition dans un vrai éditeur au lieu d'en construire un. | Bouton « Corriger dans MuseScore » : ouvre le MusicXML, Pupitre relit le fichier enregistré. Installé par l'utilisateur, comme Audiveris. |
| **OpenScore (Lieder, quatuors…)**, corpus **music21**, **CPDL** | CC0 / BSD / licence CPDL | Partitions numériques propres : le chemin principal au lieu de l'OCR. | « Trouver cette partition » : liens préremplis, ou petite bibliothèque CC0 livrée avec l'app. |
| **wavesurfer.js** (plugin Regions) | BSD-3 | Exactement l'interaction « d'ici à là, c'est cette phrase » : régions glissables sur la forme d'onde. | Variante 2 de « Caler par phrases ». Peut aussi remplacer peaks.js (LGPL). |

## 2. Garder (déjà bons choix)

synctoolbox (MIT, cœur d'align3) · Verovio (LGPL, fichier séparé ; utiliser sa **timemap** MIDI au lieu de recalculer les temps) · Signalsmith Stretch (MIT) · beat_this (MIT) · FCPE (MIT) · basic-pitch (Apache, déjà en ONNX) · Demucs (MIT, mais optionnel) · smplr (MIT) · échantillons CC0.

## 3. Réduire la taille et le support

| Outil | Licence | Pour quoi | Comment |
|---|---|---|---|
| **onnxruntime** | MIT | Retirer PyTorch (des centaines de Mo) du paquet. | Exporter beat_this et FCPE en ONNX ; basic-pitch l'est déjà. Demucs reste en téléchargement optionnel. |
| **Web Audio `decodeAudioData` / WebCodecs** | (navigateur) | Décoder mp3/m4a sans ffmpeg pour l'écoute. | ffmpeg (build **LGPL** par système) seulement pour l'analyse. |
| **pYIN de librosa** | ISC | Hauteur chantée sans modèle de 99 Mo (remplace CREPE). | `librosa.pyin` pour le contrôle de couverture. |
| **uv** + **python-build-standalone** | MIT/Apache, MPL | Installer le « calage automatique » au premier besoin, hors de l'installeur. | Wheels préconstruites par plateforme ; « Installer le calage (≈1 Go) ». |
| **pywebview** ou **Tauri** | BSD / MIT-Apache | Une coque Mac **et** Windows au lieu de la coque Swift Mac seule. | pywebview si on garde Python ; Tauri + Python en « sidecar » sinon. |
| **Sparkle** (Mac) / **WinSparkle** (Windows) | MIT | Mises à jour, absentes aujourd'hui. | Flux appcast signé ; obligatoire avant de vendre. |

## 4. Rendre les preuves honnêtes

| Outil | Licence | Pour quoi | Comment |
|---|---|---|---|
| **Playwright** (`toHaveScreenshot`) | Apache-2.0 | Remplacer « headless, 0 erreur JS » par une comparaison d'images. | Une capture de référence par écran, validée une fois par Léo. Chromium est déjà installé. |
| **pytest** + **ruff** | MIT | Une seule commande de tests, un linter Python. | `tools/tests/unit/` sans données de Léo. |
| **TypeScript en `checkJs`** + **ESLint** | Apache / MIT | Typer `modApi` sans réécrire en TS. | `// @ts-check` + JSDoc ; `tsc --noEmit`. |
| **GitHub Actions** (macOS + Windows) | — | Prouver que ça démarre sur Windows, chaque jour. | `run_all.py` + démarrage de `serve.py`. |
| **pre-commit** | MIT | Bloquer `/Users/`, les imports interdits, les commentaires datés. | Hooks simples avant chaque commit. |

## 5. À ne pas utiliser
Whisper / WhisperX pour aligner des paroles chantées (mauvais sur le chant) · modèles MMS, madmom, ROSVOT (non commerciaux) · oemer, SMT (inutilisables) · homr importé en Python (AGPL) · Spleeter (moins bon que Demucs) · tout modèle génératif d'images.
