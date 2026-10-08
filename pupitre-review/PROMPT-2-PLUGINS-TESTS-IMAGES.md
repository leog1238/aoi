# PROMPT 2 — Plugins, vrais tests, pochettes IA, optimisation (complète et corrige le PROMPT 1)

> Léo : colle ce texte **après** `PROMPT-POUR-L-IA-DE-PUPITRE.md`, dans la même session.
> Là où les deux se contredisent, **celui-ci gagne** : ce sont tes décisions du 8 octobre au soir.

---

## A. Les nouvelles décisions de Léo (elles remplacent le PROMPT 1 sur ces points)

1. **Pupitre se vend une fois (« one shot »), pas en abonnement.** Il doit être *ultra beau*, simple à caler, et meilleur que les autres apps qui sont laides.
2. **Plugins à télécharger : oui.** Léo préfère que l'utilisateur télécharge 2 ou 3 plugins excellents plutôt que de se limiter à des outils gratuits médiocres embarqués.
3. **Une IA d'image générative pour les pochettes : oui**, avec l'effet de touches impressionnistes (le pinceau maison, `app/peintre-moteur.js`). Le PROMPT 1 disait « abandonné » : c'est annulé. Elle revient **en plugin**, avec les règles de la section C.
4. **Les versions et sauvegardes restent sacrées** : `tools/versions.py` avant et après chaque étape, et une sauvegarde des données hors de l'app (PROMPT 1, étape 7).

Ce qui ne change pas : la bêta reste « le Lecteur » sur Mac ; tout ce qui est **dans** le paquet reste vendable ; l'app ne dit jamais « calé » sans mesure ; une seule session sur les fichiers produit.

---

## B. Le système de plugins (à construire après les étapes 1 à 4 du PROMPT 1)

### B.1 Pourquoi
Le paquet de base reste petit, beau, sûr et 100 % vendable. Les gros moteurs (calage automatique, lecture de partition, images IA) vivent à côté, se téléchargent au besoin, et peuvent être remplacés par un meilleur sans toucher à l'app. C'est aussi ce qui règle la question AGPL pour Audiveris et homr (voir `LICENCES-ET-PLUGINS.md`).

### B.2 Les plugins proposés (3 au départ, pas plus)
| Plugin | Ce qu'il apporte | Source | Licence / règle |
|---|---|---|---|
| **Calage automatique** | Le calcul complet (beat_this, FCPE, basic-pitch, synctoolbox, Demucs seulement si licence confirmée), ≈ 1 Go | ton propre serveur | Uniquement des briques vérifiées permissives (fichier `docs/LICENCES.md`). Pupitre marche sans : calage par phrases à la main. |
| **Lecture de partition** | Photo/PDF → brouillon MusicXML | Audiveris depuis **son site officiel** (installé par l'utilisateur), ou ReadScoreLib si Léo achète une licence (alors intégré, pas plugin) | AGPL : lancé par ligne de commande, jamais importé, jamais modifié, jamais redistribué par nous. Toujours « brouillon à vérifier ». |
| **Pochettes IA** | Images générées puis repeintes par notre pinceau | modèle téléchargé (section C) | Licence commerciale vérifiée du modèle ; marquage « image générée ». |
| *(optionnel, sans téléchargement par nous)* **MuseScore** | Corriger une partition dans un vrai éditeur | site officiel | GPL, programme séparé, Pupitre ne fait qu'ouvrir un fichier. |

### B.3 Comment le construire
- Dossier `Pupitre-data/plugins/<nom>/` avec un `plugin.json` : `{name, version, sha256 de chaque fichier, taille, licence, url_source, url_licence, entrée (commande ou module), compatible_app: ">=1.0"}`.
- Un seul module serveur `tools/plugins.py` : `list()`, `status(name)` (absent / téléchargement / prêt / cassé), `install(name)` (télécharge, vérifie le sha256, décompresse dans un dossier temporaire puis renomme : jamais de plugin à moitié installé), `remove(name)` (vers la Corbeille), `run(name, args)` (sous-processus, délai maximum, journal).
- Un seul écran « Plugins » dans Réglages : nom, une phrase de ce qu'il apporte, taille, licence (lien), bouton Installer / Retirer, état. Pas d'autre endroit.
- Quand une fonction a besoin d'un plugin absent : une phrase calme + un bouton (« Le calage automatique demande un module de 1 Go. Installer » / « Caler à la main »). Jamais une erreur technique.
- L'app ne télécharge **jamais** un logiciel AGPL/GPL elle-même : elle ouvre le site officiel, puis détecte le programme installé.
- Tests : `tools/tests/unit/plugins_test.py` avec un faux plugin de 1 Ko : installation, mauvais sha256 refusé, coupure au milieu (rien de cassé), retrait, app qui démarre sans aucun plugin.

### B.4 Idée commerciale à présenter à Léo (pas à construire)
Avec une vente unique, les mises à jour et le support ne rapportent rien. Option à proposer : l'app de base en achat unique, et un plugin premium payant une fois (par exemple la lecture de partition si ReadScoreLib est licencié). Léo décide.

---

## C. Les pochettes IA, proprement

### C.1 Ce qui reste notre force
Le pinceau maison (`app/peintre-moteur.js`, notre code + toiles CC0) est ce qui rend les pochettes belles et uniques. Le modèle IA ne fournit qu'une **ébauche** que le pinceau repeint. Donc le modèle doit être **remplaçable** : Pupitre ne dépend pas d'un modèle précis.

### C.2 Choisir le modèle (protocole, pas de choix au feeling)
1. **Candidats** (licences à **lire sur la carte du modèle** avant tout test ; ce qui suit est de mémoire, non vérifié) :
   - Supra2-IMG (l'actuel, Apache-2.0, 116 Mo, entraîné sur des images FLUX : vérifier quelle variante de FLUX et ses conditions) ;
   - FLUX.1-schnell (Apache-2.0, très bon mais lourd, plusieurs Go même compressé) ;
   - Stable Diffusion 1.5 / ses versions distillées rapides (licence OpenRAIL-M : commercial autorisé avec restrictions d'usage) ;
   - Stable Diffusion 3.5 Medium (licence communautaire Stability : gratuite en commercial sous un seuil de chiffre d'affaires) ;
   - tout autre modèle dont la carte dit explicitement « usage commercial autorisé ».
   **Exclus d'office** : licence « non commercial », licence absente, FLUX.1-dev comme modèle livré.
2. **Critères mesurés** pour chaque candidat, sur le Mac de Léo et sur un Mac Intel ou un PC ordinaire : taille du téléchargement, mémoire, temps pour 4 images 256 px, et **beauté après le pinceau**.
3. **Beauté = test à l'aveugle** : 12 titres de pièces réels, 4 images par modèle, toutes repeintes par le même pinceau, mélangées, numérotées ; Léo (et une deuxième personne) note chaque image 1 à 5 sans savoir quel modèle l'a faite. Le gagnant est celui qui a la meilleure note **par Mo téléchargé** à beauté égale.
4. Écrire le résultat dans `rapports/pochettes-modeles-<date>.md` : tableau, images, notes, licence avec lien et date de lecture.

### C.3 Règles du plugin « Pochettes IA »
- Téléchargé à la demande, jamais dans le paquet de base ; sans lui, le pinceau seul fait des pochettes (c'est déjà le cas).
- Marquage : métadonnées de l'image **et** mention visible discrète « image générée » dans le panneau de la pochette (exigence de transparence européenne pour les contenus générés) ; le choix « sans IA » reste à un clic.
- Interrupteur pour désactiver (déjà présent) ; rien n'est envoyé sur internet.
- Prompts fabriqués par Pupitre à partir du titre (dictionnaire maison) ; pas de noms d'artistes vivants ni de styles d'artistes vivants dans les prompts.
- La réserve de 16 images IA livrée en secours : vérifier quel modèle l'a produite ; si sa licence n'est pas claire, la régénérer avec le modèle choisi.

---

## D. Apprendre vraiment de ses essais (le point le plus important de ce prompt)

Le projet a produit énormément de rendus, de bancs et de pages de comparaison, et en a peu appris. Raisons : pas de question écrite avant, pas de point de comparaison fixe, plusieurs choses changées à la fois, des chiffres sans taille d'échantillon, des règles écrites après avoir vu les résultats, et personne qui écoute les échecs. Désormais, **chaque essai suit ce carnet**, dans `rapports/essais/<date>-<nom>.md`, rempli **avant** de lancer :

```
# Essai : <nom>
Question (une seule) : …
Pourquoi c'est important pour un client : …
Ce que je change (UNE variable) : …
Point de comparaison (la version actuelle, figée, avec son numéro de version) : …
Données : ensemble de RÉGLAGE = … ; ensemble de CONTRÔLE (jamais regardé avant la fin) = …
Mesure (script + fonction) : …   Taille de l'échantillon (n) : …
Ma prédiction (chiffre) : …
Règle de décision écrite AVANT : « je garde si … ; je jette si … ; sinon je ne conclus pas »
--- après ---
Résultat réglage : …   Résultat contrôle : …
Les 5 pires cas, regardés ET écoutés un par un, rangés par cause : …
Décision (selon la règle, pas selon l'envie) : …
Ce que j'ai appris que je ne savais pas avant : …
```

Règles :
- **Une variable à la fois.** Si tu changes deux choses, tu n'apprends rien sur aucune.
- **L'ensemble de contrôle est regardé une fois**, à la fin. Chaque regard est noté (le banc le fait déjà : garder).
- **Pas de conclusion sous n = 30 cas** pour une proportion ; dis « pas assez de données ».
- **Les pires cas sont la vraie récolte** : les classer par cause (rentrée de chœur, point d'orgue, dérive, fin, partition plus longue…) apprend plus que le pourcentage moyen.
- **Écouter.** Pour le calage, chaque essai se termine par l'écoute de 3 passages par un humain (Léo ou toi via un extrait envoyé à Léo dans la Loupe).
- **Un rendu sans question n'est pas un essai.** Ne plus produire de pages de comparaison « pour voir ».
- **Garder les échecs** : un essai négatif bien écrit vaut autant qu'un positif ; il évite de le refaire.

---

## E. Les jeux de données : comment s'en servir concrètement

### E.1 Vérités externes pour le calage
1. **Schubert Winterreise Dataset** (CC BY 3.0 confirmé, Zenodo 3968389) : voix soliste + piano, plusieurs interprétations, partitions, et annotations de temps. Pour Pupitre : le cas « soliste / rubato » qui a cassé le Verdi.
   - Écrire `tools/bench/datasets/winterreise.py` : télécharge, convertit la partition en `data.json` Pupitre (via `build_piece.py`) et les annotations en liste `(q, t)` de vérité.
   - Lancer le pipeline sans aucun réglage spécial, mesurer avec **mir_eval** (écart des débuts de notes / de mesures), par interprétation, avec n.
   - Classer les pires passages par cause. C'est la base de l'état « refus » (PROMPT 1, étape 4).
2. **Dagstuhl ChoirSet** et **Choral Singing Dataset** (licences à lire sur leurs pages Zenodo avant de télécharger) : chœur multipiste. Pour Pupitre : vérifier que le calage marche quand **plusieurs** voix chantent, ce que la vérité actuelle (une seule voix de Léo) ne couvre pas.
3. Un **seul tableau** pour tout : `rapports/calage-externe-<date>.md` (pièce, n notes, % à 50/100/200 ms, pires causes). Ce tableau devient le chiffre officiel du calage. Les anciens chiffres sont retirés.

### E.2 Vos propres jeux de test, qui servent à quelque chose
- **Jeu d'or humain** (`tools/tests/fixtures/or/`) : 10 pièces maximum, choisies pour **varier** (chœur a cappella, chœur + orgue, chœur + orchestre, soliste + piano, pièce avec reprises, enregistrement live, piste d'exercice). Pour chacune : 20 à 40 phrases posées par Léo à l'oreille (avec l'outil phrases), et c'est tout. Pas besoin de toutes les notes.
- **Cas pièges synthétiques** (le meilleur outil du projet, `calage_warp.py`, à généraliser) : prendre un enregistrement juste et le déformer de façon **connue** : couper la fin (partition plus longue), ajouter une intro, un point d'orgue de 4 s, ralentir de 20 %, répéter un couplet, transposer. On connaît la bonne réponse sans aucune vérité humaine. Chaque cas doit soit être calé juste, soit **refusé avec la bonne phrase**. Un cas qui passe faux sans refus = bug grave.
- **Jeu de non-régression** : les 12 pièces de Léo restent, mais seulement pour vérifier qu'on ne recule pas (plancher gelé), plus pour décider.
- **Lecture de partition** : 30 mesures du Verdi vérifiées par Léo + 2 pages de chœur, notes ET syllabes ; et un sous-ensemble du **Sheet Music Benchmark** (mesure OMR-NED) si on compare des lecteurs.

### E.3 Ce qu'il ne faut plus faire avec les données
- Écrire une référence soi-même en regardant la sortie de la machine.
- Mesurer seulement les notes que la machine a déjà bien placées.
- Mélanger ensemble de réglage et de contrôle.
- Livrer dans l'app un fichier venant d'un jeu de données de recherche.

---

## F. « Ultra beau » et « simple à caler » : comment le rendre vérifiable

- **Un système de design** dans `app/tokens.css` : couleurs (clair et nuit), une échelle typographique (5 tailles maximum), espacements (multiples de 4 px), rayons, ombres, durées d'animation. Tout le CSS doit utiliser ces variables. Un test compte les couleurs et tailles écrites en dur : le nombre ne peut que baisser.
- **Références** : avant de redessiner un écran, Léo choisit 3 apps qu'il trouve belles ; tu en tires 5 règles écrites, appliquées, et vérifiées sur capture.
- **Captures de référence** (Playwright) en clair et en nuit, à 1440 et 760 px : aucune régression visuelle non voulue.
- **Simple à caler = chronométré** : temps pour caler une pièce de 4 minutes par phrases, par Léo puis par un choriste. Objectif < 10 min. Chaque changement de l'écran de calage doit faire baisser ce temps ou le nombre d'erreurs.
- **Compteur de simplicité** (`tools/simplicity_report.py`) : commandes visibles, mots à l'écran ; plafonds écrits dans `docs/BETA-26-OCT.md`.

---

## G. Optimisation (mesurer avant d'optimiser)

1. **Un script de mesure** `tools/bench/machine.py` : une pièce de 4 minutes, du dépôt au premier son, temps par étape, mémoire maximale ; lancé sur le Mac de Léo **et** sur une machine ordinaire. Résultats dans `rapports/perf-<date>.md`.
2. **Objectifs** à afficher dans le rapport : ouverture de l'app < 2 s ; ouverture d'une pièce < 1 s ; premier son < 300 ms après lecture ; calage automatique d'une pièce de 4 min < 3 min sur un Mac ordinaire ; mémoire < 1,5 Go.
3. **Outils** : panneau Performance de Chrome (front), **py-spy** (profileur Python, sans modifier le code) pour le pipeline.
4. **Pistes, dans l'ordre du gain probable** : ne pas lancer Demucs par défaut ; un seul détecteur de hauteur (FCPE ou pyin, pas CREPE en plus) ; Verovio rendu par page et non toute la pièce ; pianos « compact » par défaut, échantillons chargés à la demande ; cache des analyses par empreinte de fichier (déjà là : garder) ; plus tard, ONNX à la place de PyTorch.
5. Chaque optimisation = un essai du carnet (section D), avec avant/après sur le même script.

---

## H. Versions et sauvegardes (rappel, non négociable)

- `python3 tools/versions.py save "avant <étape>"` et `save "<étape> faite"` à chaque étape, plus un `git tag etape-<n>`.
- Avant toute modification de données de Léo (corrections, partitions, bibliothèque) : copie dans `history/`, jamais d'effacement.
- La sauvegarde complète hors de l'app (PROMPT 1, étape 7) passe **avant** le système de plugins.
- Si un essai abîme quelque chose : `versions.py restore`, puis noter dans le carnet ce qui s'est passé.

---

## I. Ordre global, mis à jour

1. PROMPT 1, étapes 0 à 4 (vérité à l'écran, réduction, tests, calage honnête).
2. Section E.2 « cas pièges synthétiques » et E.1 Winterreise : les vérités externes, avant de toucher davantage au calage.
3. PROMPT 1, étapes 5 (phrases) et 7 (sauvegarde).
4. Section B : système de plugins, avec le plugin « Calage automatique » d'abord.
5. Section C : choix du modèle de pochettes par test à l'aveugle, puis plugin « Pochettes IA ».
6. Section F : système de design et captures ; section G : mesure de performance.
7. PROMPT 1, étapes 8 à 10 (licences, paquet signé, test humain).

À chaque étape : carnet d'essai si c'est une expérience, version avant/après, rapport honnête à Léo (ce qui ne marche pas d'abord).
