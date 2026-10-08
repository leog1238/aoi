# Licences des outils proposés, et l'idée « plugin à télécharger soi-même »

De mémoire, sans vérification web : à revérifier sur le dépôt officiel de chaque projet avant de livrer (fichier LICENSE + carte du modèle pour les poids).

## Feu vert : peut être dans l'app vendue
- Silero VAD (MIT), wavesurfer.js (BSD-3), synctoolbox (MIT), Signalsmith Stretch (MIT), FCPE (MIT), basic-pitch (Apache-2.0), Demucs (MIT, code et poids htdemucs), smplr (MIT), onnxruntime (MIT), librosa / pYIN (ISC), pywebview (BSD-3), Tauri (MIT/Apache), Sparkle et WinSparkle (MIT).
- beat_this : code MIT ; vérifier que la licence des poids (checkpoints) est bien la même.

## Feu orange : possible, avec des conditions
- Verovio, peaks.js, lamejs (LGPL-3) : fichiers séparés, non fusionnés dans un bundle, remplaçables, texte de licence livré, mention dans « À propos ».
- ffmpeg : seulement une version LGPL (sans x264, x265 ni fdk-aac), en programme séparé, texte de licence livré. La version Homebrew du Mac de Léo est GPL : interdite dans le paquet.
- Échantillons CC BY (Salamander, FreePats) : l'attribution doit être visible dans l'app (« À propos »), pas seulement dans un fichier.

## Pas dans le paquet, mais sans problème (outils de développement)
- mir_eval (MIT), Playwright (Apache-2.0), pytest (MIT), ruff (MIT), TypeScript (Apache-2.0), ESLint (MIT), pre-commit (MIT), uv (MIT/Apache), GitHub Actions. Ils ne sont pas livrés au client.

## À vérifier avant usage, même interne
- Jeux de données de chercheurs : Dagstuhl ChoirSet (probablement CC BY), Choral Singing Dataset et Schubert Winterreise Dataset (licences à vérifier, certaines peut-être « non commercial »). Un « non commercial » peut interdire même un usage de test interne par une entreprise qui vend. Lire la licence de chacun ; garder seulement ceux qui l'autorisent.
- Partitions : OpenScore Lieder (CC0) = OK, même dans l'app. Corpus music21 : licences mélangées fichier par fichier. CPDL : la licence CPDL autorise la copie libre mais pas la revente ; donc lien vers le site, pas de partitions CPDL dans le paquet.

## L'idée « plugin à télécharger soi-même »

### Bonne idée pour les logiciels GPL / AGPL
Audiveris, homr, MuseScore. La ligne est tenue si les quatre conditions sont vraies :
1. l'utilisateur le télécharge depuis le site officiel de l'auteur, pas depuis un serveur de Pupitre ;
2. Pupitre marche sans lui (le plugin ajoute, il ne remplace pas une fonction vendue) ;
3. Pupitre lui parle seulement par fichiers et ligne de commande publique (pas d'import Python, pas de réparation de ses fichiers internes comme le `.omr` d'Audiveris) ;
4. Pupitre ne le modifie pas et ne le redistribue pas.
Écran type : « Pour lire une partition depuis un PDF, Pupitre peut utiliser Audiveris, un logiciel libre séparé. [Ouvrir le site d'Audiveris] ». Puis Pupitre le détecte.

### Mauvaise idée pour les modèles « non commercial »
madmom, MMS, ROSVOT, poids sans licence. Le problème n'est pas la distribution, c'est l'usage : une fonction d'un produit payant qui repose sur eux est un usage commercial, même si le client les télécharge lui-même. Le « plugin » serait une feuille de vigne. Ne pas les utiliser du tout.

### Mauvaise idée pour les modèles d'images à l'origine floue (Supra2-IMG)
Même raisonnement : le téléchargement par le client ne change rien si la fonction est vendue. Et la fonction ne sert pas le métier de Pupitre.

### Bonne idée pour la taille, quelle que soit la licence (si permissive)
Demucs, le moteur de calage complet (~1 Go) : téléchargement optionnel au premier besoin, depuis ton propre serveur, pour garder l'installeur petit. Là, ce n'est pas une question de licence (MIT autorise), c'est une question de poids.

Une heure avec un avocat spécialisé en logiciel avant la vente reste nécessaire, surtout pour Audiveris / homr.
