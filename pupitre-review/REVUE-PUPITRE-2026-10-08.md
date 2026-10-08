# Revue hostile de Pupitre — 8 octobre 2026

*Revue externe, faite sur le zip `Pupitre-pour-une-IA.zip` (701 fichiers, 24 Mo décompressés) selon le brief `00-LIS-MOI-D-ABORD.md`. Le relecteur n'a pas construit l'app, ne l'a pas lancée, n'a rien écouté. Il a lu le code, les rapports, le journal, les données d'exemple et les 35 captures. Chaque constat porte un chemin, un numéro de ligne et une étiquette : `[verified]` = vu dans le fichier cité ; `[inferred]` = découle de ce qui a été vu ; `[guess]` = plausible, non vérifié. Pour les faits trouvés sur le web : `[verified-web]` avec l'adresse.*

## Comment cette revue a été faite

- **Lecture directe par le relecteur principal** : brief, historique, inventaire, règles (`code/CLAUDE.md`), reprise, fin du journal, `confidence.py`, `pipeline.py` (mesure `within50`, garde), `serve.py` (réseau, écritures), `library.js` (mots « calé »), `build_product.py`, `omr.py`, `anchors.py`, `structure_piece.py`, `raccourcir_piece.py`, `POUR-UNE-IA.md`, `app.js` (chargeur de modules, forme), 4 captures (07, 13, 17, 23), les deux MusicXML du Verdi (comptés par script), `library.json`.
- **24 lecteurs spécialisés** (un par domaine du brief, plus : le cas Verdi, les 35 images, les chiffres de lecture, les chiffres de calage, l'honnêteté de `01-HISTOIRE`, les maquettes, le marché, les fonctions secondaires, les pochettes, la documentation), chacun avec accès complet au zip.
- **Vérification contradictoire** : chaque constat de sévérité moyenne ou plus a été rouvert par un vérificateur indépendant chargé de le réfuter (deux pour les constats critiques ou élevés : l'un contrôle les citations ligne par ligne, l'autre l'importance). Les constats réfutés sont listés à part ; ceux qui ont été affaiblis le disent.
- **12 agents « questions »**, un par question de la section 6 du brief.
- **Un critique de complétude**, puis des enquêtes sur les trous qu'il a trouvés.
- **Trois juges** (vente, utilisateur, ingénierie) ont classé les constats ; le classement final est celui du relecteur principal.

## Limites, à lire avant le reste

- **Pas d'exécution.** Rien n'a tourné : ni l'app, ni les tests, ni le pipeline. Les affirmations sur ce que l'app « fait » sont des lectures de code.
- **Pas de son.** Aucune mesure de calage n'a été refaite. Nous jugeons la *méthode* de mesure, pas le résultat.
- **Manquent au zip** : `.git` et `versions/`, les bibliothèques `vendor/`, les poids, les audios, les fixtures de vérité (`calage_bench.json`), le PDF Verdi complet, les blobs de données des maquettes. Là où cela limite un jugement, c'est dit.
- **Pour aller plus loin, il faudrait** : une machine Windows ordinaire avec le dépôt complet ; un enregistrement et une partition qui ne soient pas de Léo ; une heure avec un choriste débutant devant l'app ; l'historique git (pour dater ce qui est mort) ; les fichiers de vérité du calage (pour recalculer les chiffres).

## Couverture réelle de cette revue (à lire avant de me croire)

Le plan prévoyait 24 lecteurs, 12 agents « questions », une vérification contradictoire de chaque constat, un critique de complétude et un jury. Le budget de session a imposé une coupe en cours de route. Ce qui a réellement été fait :

| Domaine | Qui l'a lu | Profondeur |
|---|---|---|
| Calage et signal de confiance | lecteur dédié + relecteur principal | forte (17 + 11 constats) |
| Caler par mots / par phrases | lecteur dédié | forte (19 constats) |
| Honnêteté des tests | lecteur dédié | forte (18 constats) |
| Sécurité des données | lecteur dédié | forte (17 constats) |
| Questions 1, 2, 7, 8 du brief | agents dédiés | forte |
| Produit, UX, lecture OMR, Relire par une IA, architecture, licences, empaquetage, performance, méthode, documentation, images, fonctions secondaires | relecteur principal seul (lecture directe, scripts, 4 captures) | moyenne : constats sûrs mais moins nombreux |
| Questions 3 à 6 et 9 à 12 | relecteur principal seul | moyenne |
| Vérification contradictoire des constats des lecteurs | **non faite** (coupée pour le budget), sauf les points que le relecteur principal a recontrôlés lui-même (marqués ci-dessous) | — |

Conséquence : les constats des quatre lecteurs portent leur propre étiquette `[verified]` (ils ont ouvert les fichiers), mais n'ont pas été réfutés par un second agent. J'ai recontrôlé moi-même, ligne par ligne, les constats qui portent le classement final (section « Top 15 »). Les autres sont cités tels quels ; un numéro de ligne peut être décalé de quelques lignes.

Les constats bruts (82, avec preuves, détails et actions) sont dans `pupitre-review/constats.json` à côté de ce rapport : c'est le fichier à donner aux agents.

---

## Le verdict en une page

1. **Le mot « calé » que l'app affiche ne repose sur rien.** `[verified]` La carte « Ton enregistrement de « X » est calé. » s'affiche dès que le job est fini, quel que soit le résultat (`code/app/library.js:1444`). Le pourcentage « des attaques de ta voix tombent juste » exclut du calcul les notes que la carte place là où on ne les entend pas (`code/tools/pipeline.py:907`), donc un calage faux en bloc obtient un bon score. Le seul outil de confiance (`confidence.py`) est **désactivé par défaut** (`pipeline.py:112`). Et par construction, l'aligneur doit caser toute la partition dans l'enregistrement : le Verdi écrasé n'est pas un bug, c'est le comportement nominal (`code/tools/align3.py:302`, `:370`).
2. **La lecture de partition n'est pas un produit, et ne le sera pas le 26 octobre.** `[verified]` Sur le seul cas hors chœur, les paroles sont du bruit à l'écran après la « réparation » (image 07 : « Mi pu-n'a Mïngiunsc di ('a -carmi »), Emilia a 13 syllabes pour 328 notes dans le fichier réparé, les deux seuls moteurs qui marchent sont AGPL, et le « 29/29 » est mesuré contre une référence écrite par l'agent qui a écrit les règles, dont le hold-out exclut par code l'unique mesure à triolet (`rapports/verdi-lecture-parfaite-2026-10-08/scripts/holdout.py:43`).
3. **Le produit vendable n'existe pas.** `[verified]` `build_product.py` est Mac Apple Silicon seulement (`:132`, `:271` cible `arm64-apple-macosx12.0`) et copie un Python **sans** `site-packages` (`:199`) : ni numpy, ni torch, ni Verovio côté serveur. Aucun build Windows n'existe ; `build_check_windows.py` est une liste de contrôle. Audiveris est cherché dans `~/Applications` et `/Applications` seulement (`code/tools/omr.py:34-36`).
4. **Les preuves sont fabriquées par les mêmes mains que le code.** `[verified]` Les seules références de calage du zip ont été « faites à l'œil par Claude » à partir de la sortie de l'aligneur qu'elles jugent (`code/tools/tests/fixtures/calage_refs/bach.json:58`). Neuf « tests » n'ont aucune assertion. « 548/548 » est ≈ 73 assertions × 13 partitions de Léo, dont le code est comparé à sa propre sortie (`code/tools/tests/score_edits_test.mjs:16`). « Headless, 0 erreur JS » : chaque écriture reçoit `{}` 200 (`code/tools/simplicity_report.py:90`), le son est interdit, personne ne regarde.
5. **La méthode produit plus vite qu'elle ne vérifie.** `[verified]` Le 8 octobre, 6 des 12 dernières entrées du journal portent sur les pochettes peintes (`code/claude/HANDOFF.md:556-567`), le jour où lecture, paroles et calage ont été trouvés faux sur le Verdi. Les documents de pilotage (`STATUS.md`, `TODO-LEO.md`) datent du 24 septembre. `code/CLAUDE.md` dit encore « Made by and for Léo » et « Pas encore de vraie app » pendant que le brief annonce une bêta payante dans 18 jours.

Ce qui mérite vraiment d'être gardé : l'écran d'exercice (image 17) est calme et bon ; `serve.py` est sérieux (127.0.0.1, contrôle Host/Origin, écritures atomiques, historique) ; « Une portée par voix » et « Raccourcir » font un essai à blanc et basculent en copie quand c'est risqué ; `calage_warp.py` (test sans vérité par déformation connue) et les recettes gelées avant mesure sont les deux bonnes idées de méthode du projet ; la discipline « nothing deleted » est presque tenue (voir section 6 pour les exceptions).

---

## 1. Produit

*Lu par le relecteur principal (brief, VISION, ETAT, NAVIGATION, REPRISE, STATUS, TODO-LEO, index.html, app.js, inventaire, rapports regard-neuf) et par l'agent de la question 8.*

### Ce qui est vraiment bon (à garder)
- **Le job central est juste et déjà fait pour Léo** : voir sa ligne sur la partition, l'entendre au piano par-dessus un vrai enregistrement, boucler, ralentir. L'image 17 le montre en un écran lisible. `[verified]`
- **Le `.pupitre`** (une pièce préparée, exportable, réimportable, avec droits par audience) est le bon objet de livraison pour un chœur : une personne prépare, les autres reçoivent. `[verified]` `code/tools/api_library.py:711-830`.
- **Le lexique « un mot par idée » et « pas de toast de succès »** sont des règles produit rares et bonnes. `[verified]` `code/CLAUDE.md:9`.

### Ce qui est faible
- **Cinq produits cohabitent** dans `index.html` (150 `<button>`, 16 modules nommés dans `MODULE_NAMES`, `code/app/app.js:8143`) : (1) le lecteur de pièces préparées ; (2) l'atelier de calage ; (3) la chaîne de lecture de partition (photos, PDF, passes à la main, relecture par IA) ; (4) un studio (Pianiste, pistes d'exercice, vidéo, impression, export mix, Chanter/Entraînement) ; (5) la décoration (pochettes peintes, petite IA, rosaces). Seul le (1) est prêt. `[verified]`
- **Le public n'est pas choisi** : `CLAUDE.md` parle des ténors d'Éolides, `VISION.md` des chœurs, le brief des solistes débutants, le 8 octobre d'un opéra. Chaque public change la réponse sur la lecture et le calage.
- **La décision la plus importante n'est pas prise** : qui prépare les pièces ? Si c'est le client (PDF + rip), le produit n'existe pas (sections 3 et 4). Si c'est Léo ou un préparateur, le produit existe presque aujourd'hui.

### Ce qui est faux, survendu ou du travail perdu
- **« Mac et Windows, 100 % hors ligne, bêta le 26 octobre »** : aucune de ces trois promesses n'est tenable avec le code du zip (section 9). `[verified]`
- **34 pièces dans `library.json` d'exemple**, dont `asdf` et deux copies du Verdi : la bibliothèque de test de Léo est devenue la bibliothèque de référence. `[verified]` `donnees-exemple/formats/library.json`.
- **Le bouton « Relire par une IA » est au niveau de « Ouvrir cette pièce »** dans le dossier d'une pièce (image 23) alors que la fonction n'a jamais tourné avec une vraie réponse d'IA (`01-HISTOIRE §3.2`). `[verified]`
- **La ligne « ESSAI · Petite pochette · Peinte »** est visible dans le vrai classeur (image 23) : un interrupteur de maquette dans le produit. `[verified]`

### Ce que je ferais à la place
- **Définir la bêta du 26 octobre comme « le Lecteur »** : ouvrir un `.pupitre` préparé par Léo, choisir sa voix, écouter, boucler, ralentir, Ma ligne, et un calage à la main minimal. C'est la réponse de l'agent de la question 8 et la mienne. Écrire `code/docs/BETA-26-OCT.md` : ≤ 12 commandes sur l'écran d'exercice, ≤ 8 dans le panneau, gel de toute nouveauté.
- **Rétablir le mode Chanteur par défaut** (`setAppMode`, `code/app/app.js:8146` ; le forçage « atelier » à `:7233` saute) et cacher l'atelier derrière un seul bouton.
- **Sortir du build** (`APP_SKIP`, `code/tools/build_product.py:56`) : `lire/`, `lecture-main*`, `lecture-pass-*`, `relire-*`, `iareview*`, `portees.js`, `raccourcir.js`, `sanspartition.js`, `peintre*`, `classeur-pochettes.*`, `video.js`, `train.js` (Chanter réduit à « une prise »), `pianiste*` sauf « Partition ».
- **Remplacer « Ajouter une pièce » par « Recevoir un .pupitre »** dans la bêta (`code/app/library.js`, branche `add`, lignes ~583-1061) ; garder le chemin « MusicXML propre + enregistrement » pour Léo.

### Dans quel ordre, et quoi arrêter
1. Décision écrite « bêta = Lecteur » (une heure). 2. Build réduit et testé sur un Mac vierge (un jour). 3. Préparer 5 à 8 pièces du répertoire du chœur en `.pupitre` (c'est le vrai travail de la bêta). **Arrêter** : maquettes, pochettes, Pianiste, Chanter, lecture OMR, « Raccourcir », « Une portée par voix », Relire par une IA, jusqu'au 27 octobre.

---

## 2. UX pour non-musiciens et non-techniciens

*Lu par le relecteur principal (welcome.js, index.html, library.js, dropimport.js, LEXIQUE, images 07, 13, 17, 23) et par les agents des questions 2 et 4.*

### Ce qui est vraiment bon
- L'écran d'exercice (image 17) : une partition, ma voix en bleu, trois curseurs « Ce que j'entends », un transport. Un débutant comprend quoi faire. `[verified]`
- Le principe « Voilà ce que j'ai compris » (écouter trois extraits avec sa ligne au piano, dire « ça suit bien / pas tout à fait ») est la bonne forme de contrôle humain. `[verified]` `code/app/library.js:1497-1540`.

### Ce qui est faible
- **Le vocabulaire** : « calage », « recaler », « repères », « mot posé », « note sûre », « passe à la main », « une portée par voix », « raccourcir la partition » coexistent. Le LEXIQUE fait 57 Ko : un lexique de 57 Ko n'est pas « un mot par idée ». `[verified]` `code/docs/LEXIQUE.md`.
- **Le dossier d'une pièce** (image 23) : 19 commandes selon l'agent, dont « Sur cette pièce », « Un autre détail », des « ? » à côté de « ténor » et de « Partition ». Léo lui-même : « plus très logique ». `[verified]`
- **Les états d'erreur et de doute n'existent pas** : rien ne dit « la partition est plus longue que l'enregistrement », « je n'entends pas ta voix ici », « ces paroles sont illisibles ». La seule fonction qui compare les durées (`api_library.py:538-560`) est court-circuitée pour align3 (`:549`), à sens unique (`:555`) et son résultat n'est lu par aucun fichier de `app/`. `[verified]`
- **Les trois fenêtres d'écoute sont début / milieu / fin de ma voix selon la carte** (`library.js:1549`), pas les endroits douteux ; sur le Verdi la fenêtre « fin » tombe dans l'Ave Maria comprimé après que la voix s'est tue. `[verified]`

### Ce qui est faux, survendu ou du travail perdu
- **« Lue à 81 % sûre »** (image 13, maquette) : le chiffre vient d'un compte de mesures qui ne tombent pas juste et de paroles « bizarres » ; ce n'est pas une précision, et la capture montre à côté une lecture illisible (« . 1101173 », « ro'' e.s'pl''essz'0,,e »). Le bouton « L'IA relit les 114 endroits · ≈ 0,40 € » promet un service qui n'a jamais tourné. `[verified]`
- **« Caler avec des mots »** : la bulle dit « chaque mot déplace seulement sa note » (`code/app/reperes.js:1557`) alors que depuis le 8 octobre un mot à plus de 0,75 s emporte toute la suite (`code/tools/anchors.py:557-583`). Un mot écarté par le serveur disparaît sans message (`anchors.py:160` ; `dropped` jamais lu dans `app/`). `[verified]`
- **Les maquettes « Caler par phrases »** (images 14-16) acceptent des phrases dans le désordre et des blocs qui se chevauchent (`code/app/maquettes/caler-phrases/caler-phrases.js:77`, `:153`). La variante 3 est fabriquée à partir de repères d'une expérience hors zip, coupés au nombre de caractères (`rapports/caler-phrases-2026-10-08/scripts/build.py:84`). `[verified]`

### Ce que je ferais à la place
- **Trois mots d'état, pas plus**, calculés d'un seul objet `quality` (section 4) : « Vérifié par toi », « À vérifier », « Faux par endroits ». Jamais « Bien calé » sur la foi de 45 s.
- **Une seule interaction de calage pour un débutant** : la variante 1 « Au fil de l'écoute » (la phrase en grand, Espace quand elle commence, tenir Espace = elle finit), avec la bande de la variante 2 uniquement en retouche. C'est la réponse de l'agent de la question 4 et je la partage. Pas de variante 3 avant qu'une confiance par phrase existe.
- **Le texte des paroles vient de l'utilisateur** (il colle le livret), jamais de l'OCR : Pupitre coupe en phrases sur la ponctuation et ne compte que les syllabes.
- **Messages d'impossibilité, pas de doute** : « L'enregistrement s'arrête avant la fin de la partition : dis-moi où » est une phrase que l'app doit savoir dire. Aujourd'hui la carte n'a même pas de champ pour « hors enregistrement » (`anchors.py:224-226`).

### Dans quel ordre, et quoi arrêter
1. Retirer les mots « calé » non prouvés (une demi-journée). 2. Mode Chanteur par défaut et dossier de pièce à 7 commandes (maquette « Affiche », `code/app/maquettes/classeur-piece/`). 3. Variante 1 des phrases dans l'app, pas dans une maquette. **Arrêter** : « Caler avec des mots » (le retirer de la surface client), les « ? » d'aide, la ligne « ESSAI ».

---

## 3. Lecture de partition (OMR), Relire par une IA, le cas Verdi

*Lu par le relecteur principal (omr.py, structure_piece.py, raccourcir_piece.py, rapports OMR, POUR-UNE-IA.md, MusicXML Verdi comptés par script, pages 02/04/14 regardées) et par l'agent de la question 1 ; les lecteurs OMR, Relire, Verdi et « chiffres de lecture » ont été coupés pour le budget ; le lecteur « tests » a couvert les chiffres.*

### Ce qui est vraiment bon
- **Les rapports disent eux-mêmes la vérité** : « « Parfait » n'est pas prouvé et n'est pas atteignable par la lecture locale seule » (`rapports/verdi-lecture-parfaite-2026-10-08/RESULTATS.md:140-150`) ; « no sellable local reader exists » (`rapports/omr-local-2026-10-08`). Le problème n'est pas l'honnêteté des rapports, c'est que personne n'en tire la conséquence produit. `[verified]`
- **La règle « montrer sa propre photo avec les notes lues dessus »** (apprise le 5 octobre) est juste. `[verified]` `01-HISTOIRE §3.2`.
- **« Raccourcir la partition »** : coupe en texte, mêmes octets pour chaque mesure gardée, essai à blanc, refus en place dès qu'une correction dépasse la coupe. `[verified]` `code/tools/raccourcir_piece.py:15-27`, `:180-236`.

### Ce qui est faible
- **Tout le mérite de « Une portée par voix » est de mise en page.** Le fichier réparé passe de 6 à 3 parties, mais Voix 1 passe de 541 à 842 notes, Voix 2 (Emilia) garde 13 syllabes sur 515, et une mesure apparaît (308 → 309) sans explication. `[verified]` comptage script sur `donnees-exemple/verdi-otello/lecture-APRES_une-portee-par-voix.musicxml`. `decide()` n'a de barrière que sur les notes (`code/tools/lecture/structure.py:518`) ; `lyrics_dropped` est compté (`:853`) et ne bloque jamais.
- **4 709 lignes d'heuristiques** dans `tools/lecture/strips.py` et 2 828 dans `merge.py`, réglées sur 6 pièces de chœur et un Verdi. Le rapport le dit : « Un seul PDF, scanné propre : rien ne dit que les mêmes chiffres tiennent sur une photo ou une autre édition » (`RESULTATS.md:170-178`). `[verified]`
- **`tools/lecture/` n'est pas un paquet** : il est atteint par `sys.path.insert` (`code/tools/api_library.py:118`, `:706`, `:1453`, `:3220`, `:4205`) et par sous-processus (`code/tools/omr.py:258`). `[verified]`

### Ce qui est faux, survendu ou du travail perdu
- **« 29 mesures justes sur 29 »** : référence transcrite par l'agent, « NOT checked by Léo » (`rapports/verdi-lecture-parfaite-2026-10-08/scripts/ref.py:1`, `holdout.py:4`) ; 15 des 29 ont servi à écrire les règles ; le hold-out de 15 mesures contenait une mesure à triolet, retirée par le filtre `page <= 14` (`holdout.py:43`), d'où « 14/14 sans triolet ». Le piano n'est pas évalué. Les paroles ne sont jamais comptées. **Chiffre à supprimer des documents.** `[verified]`
- **« Le lecteur local 74-81 % »** : la métrique est « mesure juste dans les quatre voix », sans paroles ni rythme interne vérifiés par un humain ; la vérité est une « partition finie à la main » dont on ne sait pas qui l'a finie (`rapports/omr-local-2026-10-08/RESULTATS.md`). `[inferred]`
- **« Relire par une IA »** : testé avec une réponse fabriquée par l'agent (55/56), jamais avec une vraie IA ; les tests font répondre au faux modèle la vérité elle-même (`code/tools/tests/ai_read_test.py:272`, `ai_package_test.py:31`). Le document que le **client** doit coller (`code/docs/POUR-UNE-IA.md`, 6 933 mots) décrit Léo par son prénom, son chœur, ses concerts et l'adresse de son serveur local (`:19-22`). Ce n'est pas un document produit. `[verified]`
- **Le travail du 8 octobre sur la lecture** (structure, raccourcir, règles, écoute) a produit des outils corrects sur un cas et aucune amélioration de ce que le client voit : image 07. `[verified]`

### Ce que je ferais à la place (réponse à la question 1)
- **Ne pas promettre la lecture automatique des notes d'une page entière.** Sur la boîte : « Ta partition en MusicXML (ton chef, CPDL, IMSLP, MuseScore.com) et ton enregistrement. Si tu n'as qu'un PDF, Pupitre t'aide à trouver l'édition numérique, ou te laisse travailler sur la page elle-même. »
- **Chemin principal = fichier numérique** : « Ajouter une pièce » accepte `.musicxml/.mxl/.mscz/.mid` d'abord ; « Lire les notes depuis un PDF » devient « Brouillon (à vérifier) » derrière Réglages. Brancher `tools/scorefinder/sources.py` comme bouton « Trouver cette partition » (liens préremplis, pas de scraping).
- **Sans fichier numérique : la page comme partition** (question 2, section 13) : la photo redressée, la ligne chantée et les phrases par-dessus. Pas de notes.
- **Garder la chaîne OMR comme outil interne de Léo** pour préparer des `.pupitre`, avec une métrique syllabe→note avant toute nouvelle règle, et une relecture humaine systématique de ce qu'il livre.
- **Relire par une IA** : ne garder la fonction que si elle tourne une fois pour de vrai (ChatGPT, Claude, Gemini, comptes gratuits, paquet Verdi) et que les opérations renvoyées sont comptées : JSON valide oui/non, opérations acceptées, opérations justes sur 20 vérifiées à l'œil. Réécrire `POUR-UNE-IA.md` en 600 mots impersonnels, données avant consignes.
- **Opéra et solistes : hors périmètre déclaré.** Le Verdi est un bon cas de test interne (vieille gravure, deux langues, didascalies) et un très mauvais test de ce qu'un choriste apportera.

### Dans quel ordre, et quoi arrêter
1. Changer la promesse et le wizard (un jour). 2. Supprimer « 29/29 » et « Lue à N % sûre » des docs et maquettes. 3. Gel de `tools/lecture/` (plus de règles, plus de nuits de retuning). **Arrêter** : les expériences « lecture parfaite », l'écoute pour corriger la lecture (7 justes sur 21, puis 6/8 sur une règle écrite après coup : `rapports/verdi-lecture-parfaite-2026-10-08/ecoute/e3b_regle2.py:1`), les moteurs alternatifs (oemer, SMT, Transcoda).

---

## 4. Calage et signal de confiance

*Lu par le lecteur « calage », le lecteur « phrases » et le relecteur principal (qui a recontrôlé chaque ligne citée dans le Top 15).*

### Ce qui est vraiment bon
- **Le modèle de chemin d'align3** (gaps audio sans partition, skips de partition sans chant, reprises, transposition) est une vraie idée. `[verified]` `code/tools/align3.py:601-660`.
- **La règle « pas de voix = jamais douteuse »**, appliquée partout. `[verified]` `code/tools/confidence.py:15`, `pipeline.py:840`.
- **Rien n'est écrasé** : un calage accepté ou recalé envoie l'ancien dans `calage/history/` avec un nom unique. `[verified]` `code/tools/serve.py:622-632`.
- **La garde du banc** (un run avec une étape en échec n'est pas compté) après quatre jours de basic-pitch en panne. `[verified]` `code/tools/tests/bench_garde_test.py`, `pipeline.py:226-231`.
- **`calage_warp.py`** : tester sans vérité humaine en déformant un enregistrement de façon connue. La meilleure idée de test du projet. `[verified]`

### Ce qui est faible
- **Le « contrôle mesure par mesure » compare le calage à lui-même** : la carte finale est la fusion dont le prior est align3, et les temps battus sont accrochés à ±0,2 s d'align3 ; `check()` mesure l'écart entre la carte et sa propre source. Il ne peut pas voir une panne globale. `[verified]` `code/tools/pipeline.py:176`, `:825-840`, `code/tools/fusion.py:153`.
- **Cinq « confiances » incompatibles** : `agreement`, `check.doubtful`, `barConfidence` (1,0 autour d'un mot posé et 0,6 par défaut : fabriquée, `code/tools/anchors.py:251-256`), `confidence` (lue par `recaler.py:497`, jamais écrite par le pipeline), `bar_cost`, plus les bandes rouges calculées dans le navigateur. `[verified]`
- **`bar_regular` efface le chemin fin à l'intérieur de chaque mesure** (`code/tools/bar_regular.py:32`) : réglé pour des pistes d'exercice à pulsation fixe, pas pour un soliste. `[verified]`
- **Le jeu de vérité** : 11 pistes Chord Perfect de la même messe + un Loch, une voix, un canal voix propre fourni. Pour les notes non corrigées, la « vérité » **est** la carte machine sur disque (`code/tools/bench_calage.py:113-135`). Dire « 2 099 notes de vérité » est une surenchère ; la vérité humaine, ce sont les notes avec `fix`. `[verified]`
- **Les textes montrés pendant le calcul** nomment BS-RoFormer (retiré le 7 octobre) et « deux calages indépendants » (faux). `[verified]` `code/tools/pipeline.py:135`, `:161`.

### Ce qui est faux, survendu ou du travail perdu
- **« Ton enregistrement est calé. »** dès que le job finit (`code/app/library.js:1444`) ; « Bien calé » = les trois oui/non de l'utilisateur sur 45 s (`:1532`, `:1618`), stockés comme verdict (`code/tools/api_library.py:2400`). `[verified, recontrôlé]`
- **`within50` est auto-sélectif** : une note dont la hauteur médiane n'est pas à ±0,7 demi-ton de la note attendue dans la fenêtre prédite est exclue (`pipeline.py:907-908` « not clearly heard: no judgement ») ; le projet le sait depuis le 26 septembre (`code/tools/tests/calage_truth.py:3-4`) et le chiffre pilote pourtant « Recommandé : garder le nouveau » et la phrase de l'oiseau (`library.js:1521`, seuils 85/70). `[verified, recontrôlé]`
- **`confidence.py` est éteint** (`pipeline.py:112` `SHIFT_SCAN = False`, `:829`), sa docstring décrit un autre algorithme que son code (`confidence.py:7-13` contre `:120-160`), et `01-HISTOIRE` le présente comme un outil qui « n'a pas attrapé » le Verdi. `[verified, recontrôlé]`
- **align3 doit atteindre la dernière frame de partition à la dernière frame audio** (`align3.py:370-371`) et ne peut sauter de la partition que là où les voix se taisent (`:622`) : une partition qui chante après la fin de l'enregistrement est **toujours** comprimée. Le Verdi « presque tout faux » est le comportement prévu. `[verified]`
- **Le pipeline ne refuse jamais pour cause de qualité** : seules les étapes `required` qui plantent donnent `failed` (`pipeline.py:489`, `:981`). `[verified]`
- **« Caler avec des mots »** : `FAR_S = 0,75` est un nombre posé entre 0,59 (Loch, mot juste) et 0,57 / 1,0 (Verdi, calage perdu) (`anchors.py:499`) ; son « jamais pire » ne peut pas se déclencher, puisque la carte est construite pour passer par chaque mot (`anchors.py:599-600`, `:551`, `:588`) ; trois moteurs cohabitent dans `anchors.py` dont un seul est vivant (`:52`, `:369`, `:653`) ; `reperes.js` garde ~800 lignes d'ancien panneau derrière `OLD_PANEL` (`code/app/reperes.js:54`, `:452`). Les mots posés vivent dans `localStorage` (`reperes.js:339`), hors de `Pupitre-data`. Un recalage par mots remplace toutes les barres posées à la main (`reperes.js:1251`). `[verified]`
- **« Caler par phrases »** : aucun code ne relie un intervalle [qDébut, qFin] → [t0, t1] à la carte (`rapports/caler-phrases-2026-10-08/RESULTATS.md:70` est de la prose) ; une phrase posée dans la maquette ne connaît ni sa première note, ni sa dernière, ni sa voix, et le `bar` exporté est faux pour la 2e phrase d'un même segment (`code/app/maquettes/caler-phrases/caler-phrases.js:78`, `:411`). **La seule interaction que Léo sait faire n'existe pas dans le produit.** `[verified]`

### Ce que je ferais à la place (réponse à la question 3)
- **Un seul objet `quality`** écrit par `pipeline.check` et exposé par `api_library.progress` : `{coverage: {t_first_voice, t_last_voice, t_first_sung_note, t_last_sung_note}, bars: [{n, state: ok|unsure|bad|silent, why}], refuse: null | phrase}`. Supprimer `barConfidence`, `bar_cost`, `sync.confidence`, les seuils 85/70, le mot `within50` de toute phrase.
- **Trois conditions de refus**, en français, avant d'afficher quoi que ce soit : (1) la partition chantée dépasse la dernière voix détectée de plus de 20 s, ou l'inverse ; (2) le tempo moyen d'une suite de 8 mesures chantées sort de [0,4 ; 2,5] × la médiane ; (3) moins de 60 % des notes de ma voix ont leur hauteur entendue à l'endroit prévu (le `tot/total` que `rate()` calcule déjà sans le montrer).
- **Un état terminal dans `align3.gdtw`** : autoriser la fin du chemin à n'importe quelle frame de partition ≥ dernière voix détectée, avec un coût de queue ; et `t = null` dans `sync.measures` pour les mesures hors enregistrement, dessinées en gris.
- **Trois fenêtres d'écoute choisies par `quality`** (la pire mesure chantée, la première rentrée après un solo, la fin réelle des voix), pas début/milieu/fin.
- **Phrases, pas mots** : `tools/phrases.py` avec une seule fonction `phrase_map(data, sync, phrases, duration) -> (map, quality)`, deux ancres dures par phrase (q de la première note chantée → t0, q + len de la dernière → t1), monotonie obligatoire, ancienne carte remise à l'échelle à l'intérieur d'une phrase, mesures non couvertes à `null`. Les phrases posées vont dans `corrections/<rec>.json` (qui a déjà historique et empreinte), pas dans `localStorage`.
- **Banc honnête** : séparer `notes_fixed` (vérité humaine) de `notes_untouched` (accord machine) dans `bench_calage.evaluate`, publier les deux avec n ; ajouter au corpus synthétique `score_longer`, `rec_longer`, `solo_rubato`, `piano_reduction` ; seuils gelés dans `fixtures/bench_floor.json` avec `sys.exit(1)` sous le plancher.

### Dans quel ordre, et quoi arrêter
1. Retirer « est calé » / « Bien calé » / `within50` de l'écran (un jour, sans risque). 2. Contrôle de couverture + refus (deux jours). 3. `phrase_map` + variante 1 dans l'app (trois jours). 4. État terminal d'align3 (après la bêta). **Arrêter** : « Caler avec des mots » côté client, `recaler.py` tant que `confidence` n'est pas écrit, les nouveaux seuils devinés, les options de recherche dans le pipeline livré (24 options de spec, `pipeline.py:728`).

---

## 5. Architecture et santé du code

*Lu par le relecteur principal (app.js, index.html, serve.py, paths.py, api_library.py, build, docs/MODULES, REVUE.md) et par l'agent de la question 7. Les lecteurs « front », « back », « maquettes » et « docs » ont été coupés.*

### Ce qui est vraiment bon
- **`serve.py`** : `ThreadingHTTPServer(('127.0.0.1', port))` (`code/tools/serve.py:755`), contrôle Host/Origin contre le DNS rebinding (`:460-474`), écritures `tmp + os.replace` (`:189-192`, `:745-748`), historique du store et des corrections, verrou (`:125`). Mieux que la plupart des serveurs locaux d'amateurs. `[verified]`
- **Le mécanisme de modules** (`loadModule`, `modApi`, `code/app/app.js:8142-8175`) est sain : les modules ne touchent qu'une API. `[verified]`
- **Les `api_*.py` rechargés à chaud** sont un confort de développement défendable tant qu'ils sont derrière un drapeau en production (ils ne le sont pas, voir plus bas).

### Ce qui est faible
- **`app.js`** : 8 521 lignes, 670 fonctions, 250 `addEventListener`, 69 `innerHTML =`, 236 commentaires datés « (7 Oct) … », 200 lignes de plus de 200 caractères (12 au-delà de 400). L'historique est écrit dans le code au lieu de git. `[verified, comptages grep]`
- **`api_library.py`** : 4 432 lignes, une chaîne `if sub ==` pour le routage (`:2230-2647` selon l'agent Q7), `types.SimpleNamespace(**globals())` passé aux outils (`:2282`). `[verified]`
- **`tools/`** : 84 000 lignes de Python dont, par analyse statique des imports depuis les points d'entrée produit (`serve`, `pipeline`, `omr`, `api_*`, `launch`, `versions`, `build_product`), **71 modules / 30 600 lignes sont atteignables et 151 modules / 47 300 lignes ne le sont pas** (`tools/lecture/` compte pour 36 900 de ces lignes, mais il est atteint par `sys.path` et sous-processus, donc ce chiffre est une borne haute). Les plus gros fichiers non atteignables : `nelson_voices.py` (872), `bench_calage.py`, `synth_corpus.py`, `simplicity_report.py`, `make_portable.py`, `migrate_to_data.py`. `[inferred, script du relecteur]`
- **Doublons** : `peintre-moteur.js` en trois copies (une identique, une ancienne de 278 lignes) ; trois règles différentes « le plus récent gagne » (corrections, score_edits, `api.files` : `code/app/app.js:4959`, `:7925`, `:8251`) ; 15 définitions locales de read/write JSON (agent Q7). `[verified]`
- **Code mort dans le produit** : la branche rubberband (`app.js:506`, `:526-529`, moteur forcé `'ss'` à `:511`), `/api/tools` (`serve.py:515-535`), l'iframe de migration, `OLD_PANEL`. `[verified]`
- **Chemins Mac en dur dans 15 fichiers de `tools/`** contre la règle n° 2 de `CLAUDE.md`. `[verified]`
- **Aucun `requirements.txt`, aucun lock, aucune CI, aucun lint.** `[verified]`

### Ce qui est faux, survendu ou du travail perdu
- **`docs/MODULES.md`** décrit 5 modules ; `MODULE_NAMES` en nomme 16. **`CONTEXTE-COMPLET.md` §5** cite encore BS-RoFormer, madmom, rubberband. `[verified]`
- **`STATUS.md` et `TODO-LEO.md` datent du 24 septembre** et contredisent le brief (« rien de visible ne change avant les concerts », « ton outil, montré aux ténors »). `[verified]`
- **28 dossiers de maquettes, 21 453 lignes**, copiées depuis l'app et vers l'app, exclues du build (`APP_SKIP`) mais servies par le serveur de développement et lues par le code réel (`classeur-piece` lit `../classeur-pochettes/`, `HANDOFF.md:565`). `[verified]`

### Ce que je ferais à la place (réponse à la question 7)
- **Ni réécriture, ni « freeze and ship »** : refactoriser sur place, mais seulement ce qui bloque la vente. Minimum avant toute vente : (1) `tools/tests/run_all.py` portable (node + python, code de sortie) ; (2) `requirements.txt` + lock ; (3) une CI GitHub Actions macOS + Windows qui lance `run_all.py`, `py_compile`, `node --check`, et démarre `serve.py` ; (4) `serve.py` derrière `main()` avec le rechargement à chaud sous `PUPITRE_DEV=1` ; (5) `tools/tool.py` (`tool('ffmpeg')` → `bin/<os>/` puis PATH) et `tools/jsonio.py` ; (6) table de routes dans `api_library.py` ; (7) suppression du code mort et des doublons listés ci-dessus.
- **Après la bêta** : extraire d'`app.js`, un par PR, le piano-roll du Calage (`:4686-6806`), le moteur audio, puis `wire()` ; `// @ts-check` + typedef JSDoc sur `modApi` ; esbuild dans `build_product.py`.
- **Règle « `app.js` ne peut que rétrécir »** vérifiée par un test (compte de lignes figé).

### Dans quel ordre, et quoi arrêter
1. `run_all.py` + CI (un jour, la plus forte valeur). 2. Code mort et doublons (une demi-journée). 3. `main()` dans `serve.py`. **Arrêter** : les commentaires datés dans le code, les maquettes dans `app/`, les sessions parallèles sur `app.js`.

---

## 6. Sécurité des données

*Lu par le lecteur « données » et le relecteur principal.*

### Ce qui est vraiment bon
- **Écritures atomiques partout où ça compte**, copie dans `history/` avant remplacement, conflit entre deux fenêtres détecté (409, `X-Base-Saved`), `BroadcastChannel` entre onglets. `[verified]` `code/tools/serve.py:162-192`, `:701-750`.
- **`score-edits.js`** fait vraiment suivre les corrections de notes (« notes perdues »), avec des tests qui testent quelque chose. `[verified]`
- **« Une portée par voix » et « Raccourcir »** : essai à blanc dans un bac à sable, refus en place dès qu'une correction dépasse, copie sinon, rien dans `corrections/` ni `score_edits/`. `[verified]` `code/tools/structure_piece.py:7-22`, `raccourcir_piece.py:15-27`.
- **`userdata.js`** : fusion par clé horodatée avec pierres tombales, réessai hors ligne. `[verified]`

### Ce qui est faible
- **Aucune copie hors du Mac.** `mirror()` dépend d'un `tools/backup_dir.txt` absent ; `TODO-LEO.md:8` et `STATUS.md:114` le confirment vide depuis le 26 septembre ; même activé, il ne copie que le dernier état (`serve.py:101-119`). `[verified]`
- **« Sauvegarder » (export `.pupitre`) oublie `store/reglages`** : retouches du piano, notes ≠, boucles, mix, transposition n'y sont pas (`code/tools/api_library.py:711-830`, `reglages` : 0 occurrence). `[verified]`
- **Les prises de « Chanter » et de « Mes prises » vivent dans IndexedDB** (jusqu'à 300 Mo, `code/app/train.js:2700-2703`) : un autre port de serveur = une autre origine = plus de prises. `[verified]`
- **L'identifiant d'une pièce est le slug du titre OCR tronqué à 40 caractères, à vie** (`api_library.py:259-262`) : le Verdi s'appelle `suspended-in-front-of-a-statue-of-the-vi` dans tous les fichiers de l'utilisateur. `unique_id` évite les collisions (`:397-405`), pas l'absurdité. `[verified]`

### Ce qui est faux, survendu ou du travail perdu
- **« Rien n'est jamais effacé »** (`code/docs/DONNEES.md:12`, `CLAUDE.md`) : l'historique des corrections est tronqué à 1 000 par `os.remove` (`serve.py:742-744`) alors que `pushToDisk` sauve 400 ms après chaque geste (`app.js:4906-4930`) ; « Retirer » un enregistrement déplace ses fichiers dans `removed/` sans chemin de retour dans l'app (`serve.py:655-665`) ; `versions.py restore` sans `--corrections` écrase quand même `recordings.json` et `pieces/data` (`code/tools/versions.py:43-44`). `[verified]`
- **« Union des edits par id »** (`DONNEES.md:23`) : les corrections de partition sont envoyées entières sans `X-Base-Rev` (`app.js:7924-7927`) ; dernier écrivain gagne. `[verified]`
- **« Une portée par voix » en mode `replace` choisi d'office quand « sûr »** (`structure_piece.py:688`), mais « sûr » ne lit jamais `store/reglages/` (clés de voix et de notes de l'ancienne partition : `pianiste.edit`, `altNotes`, `otherVoices`, `passages`) (`:400-436`). `[verified]`
- **`recordings.json` a deux écrivains, deux verrous, un seul historique**, et un fichier illisible est réécrit vide (`serve.py:282-297`, `:641` ; `api_library.py:2752`). `[verified]`
- **Un test rouge sur l'intégrité des corrections est « connu » depuis le 1er octobre** et inscrit comme normal dans le skill des agents (`code/claude/skills/pupitre-tester/SKILL.md:26`, `code/tools/tests/app_flow_post.js:56`). `[verified]`

### Ce que je ferais à la place
- **Une vraie sauvegarde** : « Sauvegarder tout » dans Mon classeur = un `.pupitre` de session complet (réglages inclus) dans un dossier choisi ; miroir configuré par défaut dans `Pupitre-data-backup/` à côté de l'app, avec l'historique.
- **Supprimer l'effacement à 1 000** ; compacter en déplaçant (jamais `os.remove`).
- **Un seul écrivain pour `recordings.json`** (le store, avec historique).
- **Prises en fichiers** via le serveur (`Pupitre-data/prises/<pièce>/`), IndexedDB en cache.
- **`X-Base-Rev` + union par id** pour `score_edits`, comme `userdata.js` le fait déjà.
- **`calage_risks` lit `store/reglages/`** avant d'autoriser `replace`.
- **Décider le test rouge** en une heure : corriger ou changer l'attente avec un commentaire daté.
- **Identifiants** : `p_` + 12 hex du sha256 de l'original ; le titre reste un nom d'affichage.

### Dans quel ordre, et quoi arrêter
1. Sauvegarde complète + miroir par défaut (un jour). 2. Test rouge décidé. 3. Effacement à 1 000 et `recordings.json`. **Arrêter** : d'écrire « jamais » dans un document tant qu'un `os.remove` existe ; de lancer un second serveur sur le même `Pupitre-data` (verrous par processus, `SKILL.md:22`).

---

## 7. Honnêteté des tests

*Lu par le lecteur « tests » (61 fichiers ouverts) et le relecteur principal.*

### Ce qui est vraiment bon
- **`pupitre_pkg_test.py`** : bac à sable réel, hook d'audit qui liste toute écriture hors du bac (`sys.addaudithook`). `[verified]`
- **Les recettes figées avant de mesurer** (`RECETTE-AB.md`), entraînement / contrôle à l'aveugle, journal des regards sur le contrôle. `[verified]`
- **`calage_warp.py`**, **`bench_garde_test.py`**, **`raccourcir_test.py`** (contrôles au byte près), **`peindre_test.py`** et **`userdata_test.mjs`** (cas de panne testés pour de vrai). `[verified]`

### Ce qui est faible
- **Neuf « tests » n'ont aucune assertion** (`calage_truth.py:423`, `calage_warp.py:343`, `calage_anchors_test.py:110`, `recaler_truth.py:136`, `calage_confidence_test.py`, `sanspartition_truth.py`, `choir_timed_check.py`, `copier_mesure.mjs`, `run_checks.py`) : des bancs qui impriment, sans seuil ni code de sortie. C'est ainsi que quatre jours de basic-pitch en panne sont passés. `[verified]`
- **32 fichiers sur 61 lisent `Pupitre-data`, `pieces.js`, des fixtures absentes du zip ou de l'audio acheté** ; 6 exigent `jsc` (macOS) ; 2 cherchent Chrome dans `~/Library/Caches` avec un filtre `mac_arm`. La suite ne tourne nulle part ailleurs. `[verified]` `calage_truth.py:57`, `pupitre_pkg_test.py:194`, `:559`.
- **Des tests vérifient la présence de chaînes dans le code source** au lieu de comportements (`run_bugs_test.py:88-90`, `vitesse_test.py:128-132`). `[verified]`
- **Le monkey-patching efface l'intégration là où elle compte** : `raccourcir_test.py:160-162`, `:230-242` remplace `create_piece`, `store_original`, `write_json_atomic`. `[verified]`
- **L'analyse d'image est testée sur des rectangles noirs et une page tournée de 4°** (`structure_test.py:49-82`, `import_test.py:100-114`). `[verified]`

### Ce qui est faux, survendu ou du travail perdu
- **« Headless, 0 erreur JS »** (129 lignes du journal selon le lecteur, 72 par mon grep) : exceptions non attrapées et `console.error` comptées pendant que chaque POST/PUT reçoit `{}` 200 (`simplicity_report.py:85-90`) ou est rejeté (`groupe_calage_headless.mjs:64`, `:78`), `play()` interdit (`:73`). Ne prouve ni l'écran, ni le son, ni la sauvegarde. `[verified]`
- **« 548/548 », « pkg 1490 ok »** : produits assertions × pièces de Léo ; `score_edits_test.mjs:16-30` compare les refs construites par `buildScoreModel` au modèle produit par le même appel ; `pupitre_pkg_test` réimporte ce que le même code vient d'exporter. `[verified]`
- **Aucune sortie attendue du zip n'a été validée par un humain** : refs de calage « made by eye (Claude) … started from the new aligner coarse result » (`fixtures/calage_refs/bach.json:58`, `mozart.json:88`) ; référence Verdi « NOT checked by Léo » ; « truth » d'`anchors_words` = hauteur détectée par la machine (`anchors_words_test.py:9`). `[verified]`
- **Deux assertions affaiblies pour passer** : un `||` qui rend le test presque toujours vrai (`score_edits_test.mjs:60`), un champ supprimé pour que l'égalité tienne (`raccourcir_test.py:207-211`). `[verified]`
- **Un seuil deviné gravé dans un test** : `anchors_far_words_test.py:80-81` fige `FAR_S`, « un choix … pas écouté » (`HANDOFF.md:560`). `[verified]`

### Ce que je ferais à la place
- **Deux dossiers** : `tools/tests/unit/` (ne lit que des fixtures publiques livrées : Verdi 3 pages, Mozart, Bach, synthétiques) et `tools/bench/` (tout ce qui a besoin de `Pupitre-data`). `node` partout, plus de `jsc`.
- **Un « jeu d'or humain » en une soirée** : Léo valide avec la Loupe 30 mesures chantées du Verdi (image + notes + syllabes, oui/non) ; 10 mesures de calage du Mozart tapées à l'oreille par deux personnes ; ces fichiers deviennent les seules références citées.
- **Chaque banc a un plancher gelé** (`fixtures/bench_floor.json`) et `sys.exit(1)` dessous.
- **Interdire dans `CLAUDE.md` le mot « vérifié »** sans l'un de ces trois artefacts : une capture comparée à une référence, un fichier d'écoute humaine signé et daté, ou un test unitaire avec sortie attendue d'origine humaine. Dans le journal, écrire « N assertions distinctes × M pièces », jamais le produit.
- **Compter les syllabes par partie** dans `structure.py decide()` (refus si une voix chantée perd > 3 %).

### Dans quel ordre, et quoi arrêter
1. `run_all.py` + planchers (un jour). 2. Jeu d'or humain (une soirée de Léo). 3. Séparation unit/bench. **Arrêter** : citer « 0 erreur JS » comme preuve ; compter les bancs comme tests ; écrire une règle après avoir vu les cas puis la noter sur ces cas.

---

## 8. Licences et exposition juridique

*Lu par le relecteur principal (LICENCES.txt, audits, inventaire, omr.py, build_product.py, labo/mini-ia, api_peindre.py en partie). Je ne suis pas juriste ; le lecteur « licences » a été coupé. Les affirmations de droit sont `[inferred]`.*

### Ce qui est vraiment bon
- **L'audit du 7 octobre** a retiré madmom, les poids « unwa », MMS, rubberband-web, l'orgue CC BY-SA, et `build_product.py` refuse par nom les composants non vendables (`NON_SELLABLE_NAME`, `:68`) et audite le paquet. C'est sérieux. `[verified]`
- **Audiveris appelé en sous-processus avec `-batch -export`** et seul son MusicXML lu (`omr.py:305-312`) : c'est la bonne forme. `[verified]`

### Ce qui est faible
- **homr est importé par pip dans `.venv-omr` et lancé par Python**, pas installé par l'utilisateur (`03-INVENTAIRE §5`, `deux_lecteurs.py`). Aujourd'hui c'est un composant de l'app. `[verified]`
- **Des imports GPL / non commerciaux restent dans l'arbre** : `tools/madmom_beats.py:10` (madmom, CC BY-NC-SA, atteignable par option depuis `pipeline.py`), `tools/render_plugin.py:19` (pedalboard, GPL-3.0). La barrière est une liste de chaînes de caractères, pas une séparation du code. `[verified]`
- **ffmpeg** : sur le Mac de Léo c'est la build GPL de Homebrew ; `bin/mac/` et `bin/windows/` n'existent pas ; ~57 fichiers l'appellent sans fonction commune (agent Q7). `[verified]`
- **Verovio, peaks.js, lamejs sont LGPL** : acceptable en fichiers séparés, à condition de ne pas les minifier ensemble dans un bundle sans conserver la possibilité de remplacement, et de livrer les notices. `[inferred]`

### Ce qui est faux, survendu ou du travail perdu
- **« TOUT doit être vendable » (7 octobre) puis « au pire je virerai avec une MAJ » (8 octobre)** : une règle dure cassée le lendemain pour une décoration. Le modèle Supra2-IMG n'est de toute façon **pas** dans le build (ni `labo/`, ni PyTorch), donc « Léo a décidé de le livrer » ne décrit rien de réel aujourd'hui. `[verified]`
- **Le mot « AGPL » est traité comme un détail de packaging** alors que c'est la question qui décide si la lecture de partition peut exister dans un produit payant.

### Ce que je ferais à la place (réponses aux questions 5, 6, 10)
- **Q6, la ligne AGPL** `[inferred]` : lancer un programme AGPL installé **par l'utilisateur**, en sous-processus, par sa ligne de commande publique, en ne lisant que ses fichiers de sortie, est la « simple agrégation » que la FAQ GPL tolère. Trois choses franchissent la ligne : (a) l'embarquer dans le paquet ou le télécharger et l'installer **automatiquement** depuis l'app comme s'il en faisait partie ; (b) l'importer comme bibliothèque (c'est le cas de homr aujourd'hui) ; (c) dépendre de ses fichiers internes (`.omr` relus et « réparés » par `export_saved` / `mend_saved_book`, `HANDOFF.md:558` : c'est une dépendance au format interne, pas à la sortie publique). Ce que je ferais : un écran « Pupitre a besoin d'Audiveris, un logiciel libre séparé » avec un lien vers le site officiel, une détection après installation, aucun téléchargement par l'app ; homr sorti du build et traité pareil ; ne plus toucher au `.omr`. Un avocat une heure avant la vente, pas après.
- **Q5, Supra2-IMG** `[inferred]` : poids Apache-2.0 mais entraîné sur ~6 millions d'images FLUX dont les conditions ne sont pas lisibles. Le risque d'être attaqué pour quatre vignettes de 256 px est faible ; le coût de retirer est faible ; **la valeur est nulle pour le job du produit**. La bonne décision n'est pas « défendable ou pas », c'est « inutile » : garder le pinceau maison (notre code + CC0), jeter le modèle, et ne plus y revenir avant d'avoir des clients. Si un jour il est livré : marquage lisible par l'utilisateur (pas seulement des métadonnées PNG, qui ne survivent pas à un redimensionnement JPEG) et mention « image générée » dans l'interface, ce que l'AI Act (article 50) demande pour les contenus synthétiques.
- **Q10, les enregistrements et partitions du client** `[inferred]` : le risque n'est pas le client (copie privée en Suisse et dans l'UE), c'est **l'éditeur du logiciel** qui, dans son onboarding, **invite** à « ripper un CD, capturer YouTube » et livre des outils d'extraction (`mix_export.py`, `api_mp3.py`, `api_video.py`, stems Demucs stockés). Ce qui est vendu doit rester un lecteur de fichiers que l'utilisateur possède déjà : ne jamais écrire « capture YouTube » nulle part, ne pas exporter de MP3 dérivés d'un enregistrement acheté vers autre chose que le dossier local, ne pas livrer de pistes d'exercice dérivées d'enregistrements achetés dans un `.pupitre` partagé (la règle « le calage oui, l'audio non » existe déjà : la garder). Le `.pupitre` par audience (`api_library.py:929`) est le bon mécanisme. Ajouter dans l'app une phrase de responsabilité et un bouton de suppression complète.
- **Marque** : « Pupitre » n'a pas été recherché (`TODO-LEO.md`, item « après les concerts »). À faire avant tout site public, c'est gratuit.

### Dans quel ordre, et quoi arrêter
1. Sortir homr du build, écran d'installation séparée (une journée). 2. ffmpeg LGPL par système dans `bin/`. 3. Déplacer `madmom_beats.py`, `render_plugin.py`, `labo/` hors du paquet ; un test d'import sans ces paquets. 4. Une heure d'avocat. **Arrêter** : les exceptions à « tout vendable » ; les discussions sur Supra2-IMG.

---

## 9. Empaquetage pour la vente

*Lu par le relecteur principal (build_product.py en entier, Pupitre.bat, Pupitre.command, paths.py, inventaire). Le lecteur « packaging » a été coupé.*

### Ce qui est vraiment bon
- `build_product.py` est une **liste blanche** (pas une liste noire), audite le paquet contre les noms interdits et les achats, signe ad hoc, et mesure au lancement (`:21-24`). `[verified]`
- Le Python « python-build-standalone » est la bonne base pour un Mac. `[verified]`

### Ce qui est faible
- **Mac Apple Silicon seulement** (`:132` `os.uname`, `:271` `-target arm64-apple-macosx12.0`) ; pas de build Intel. `[verified]`
- **Aucune bibliothèque Python dans le paquet** (`'site-packages' not in p.parts`, `:199`) : le produit de 209 Mo n'a ni calage ni lecture ni rendu serveur. Le paquet « complet » (torch CPU, Demucs 324 Mo, beat_this 2 × 78 Mo, FCPE, CREPE 99 Mo, basic-pitch, Verovio, OpenCV) n'a jamais été construit une fois. `[verified]`
- **Windows** : `Pupitre.bat` suppose un Python et un venv présents ; aucune détection d'outils par système ; `omr.py:34-36` et 14 autres fichiers ont des chemins Mac ; `migrate_library.py:40` exige `jsc` (JavaScriptCore). **L'app n'a jamais été lancée sur Windows.** `[verified]`
- **Ni signature Developer ID, ni notarisation, ni mise à jour** : « right-click › Open » comme mode d'installation (`:21`, `LISEZ_MOI`). `[verified]`

### Ce qui est faux, survendu ou du travail perdu
- **« Portable : le dossier copié sur une clé USB doit marcher ailleurs »** (`CLAUDE.md`, règle de code 5) : faux sur Windows (jsc, chemins), faux sur un Mac vierge (venv, Audiveris, ffmpeg). `[verified]`
- **« 2 Go estimés »** : l'estimation n'a pas été vérifiée par une construction. `[inferred]`

### Ce que je ferais à la place (réponse à la question 9)
- **Bêta = Mac Apple Silicon seulement, sans calage automatique ni lecture** : c'est le seul paquet qui existe. L'écrire sur la boîte.
- **Taille** `[inferred]` : app + Verovio WASM + pianos compacts ≈ 150-210 Mo (le paquet d'aujourd'hui) ; calage automatique (torch CPU ≈ 200-300 Mo, Demucs 324, beat_this 156, CREPE 99, FCPE 42, numpy/scipy/librosa ≈ 150) ≈ 1,0-1,2 Go ; lecture (OpenCV, homr séparé) ≈ 300 Mo + Java d'Audiveris côté utilisateur. Donc **≈ 1,5 Go en tout**, à livrer comme **téléchargement optionnel au premier besoin** (« Installer le calage automatique, 1,1 Go »), pas dans l'installeur. Remplacer CREPE par FCPE seul (−99 Mo) ; mesurer si Demucs apporte quelque chose au calage (le rapport dit « à 1 point près égal » : `rapports/calage-verite-2026-10-07/RESULTATS.md`, test B) et, si non, ne l'installer que pour « Ce que j'entends ».
- **Windows après la bêta** : un runner CI Windows qui construit un venv avec les wheels CPU et lance `serve.py` + `GET /index.html` est le premier jalon ; tant qu'il n'est pas vert, ne pas écrire « Windows ».
- **Signature et notarisation** dès la bêta (un compte développeur, un après-midi), sinon chaque testeur appellera.

### Dans quel ordre, et quoi arrêter
1. Construire une fois le paquet complet et mesurer (une journée). 2. Notariser. 3. Téléchargement optionnel. **Arrêter** : promettre Windows ; la règle « clé USB ».

---

## 10. Performance sur un portable ordinaire

*Lu par le relecteur principal (pipeline.py, strips.py en partie, peintre-moteur.js, journal). Le lecteur « perf » a été coupé. Tout est `[inferred]` : rien n'a été mesuré ailleurs que sur le Mac de Léo.*

- **Rien n'a été mesuré sur une autre machine.** Les seuls temps du journal : pipeline 10-20 s par pièce avec canal fourni, 1-2 min avec Demucs, basic-pitch +30-70 s (`rapports/calage-verite-2026-10-07/RESULTATS.md`, section Temps) ; peinture 14,4 ms à 300 px ; petite IA 2 s pour 4 images ; oemer 50-130 min par page. Tout sur Apple Silicon. `[verified]`
- **Estimation pour un i5 sans GPU, 8 Go** `[inferred]` : Demucs htdemucs_ft sur 4 min d'audio ≈ 8-20 min CPU ; beat_this ≈ 1-2 min ; FCPE/CREPE ≈ 1-3 min ; align3 ≈ 1-2 min ; Audiveris 6 pages ≈ 3-6 min ; homr par bande ≈ 10-20 min. **« Ajouter une pièce » ≈ 30 à 50 minutes**, avec un pic mémoire de 3-5 Go côté Python et Chrome qui tient Verovio SVG d'une pièce de 300 mesures (plusieurs dizaines de Mo de DOM) et 86 Mo d'échantillons décodés. Sur 8 Go, ça swappe.
- **Le job queue tourne à `nice 15`, un à la fois** (`serve.py:252`) : sain, mais rien n'indique à l'utilisateur « 40 minutes ».
- **Ce que je ferais** : une seule mesure, un seul script (`tools/bench_machine.py` : Mozart Ave verum, 4 min, pipeline complet, temps par étape, RSS max), lancé sur un portable Windows à 500 CHF emprunté, avant d'écrire une ligne de plus sur le calage automatique. Puis : Demucs optionnel, FCPE seul, Verovio par page et non par pièce, pianos « compact » (17 Mo) par défaut.

---

## 11. La méthode elle-même

*Lu par le relecteur principal (HANDOFF en tranches, CLAUDE.md, CONTEXTE-COMPLET, REPRISE, agents, skill testeur). Le lecteur « méthode » a été coupé.*

### Ce qui est vraiment bon
- **Le journal existe, est daté, et nomme ce qui n'a pas été vu** (« mode nuit PAS vu », « pas écouté », « non relues ici »). Peu de projets ont ça. `[verified]`
- **Les rapports ont une section « ce qui reste faux »**, et les recettes sont gelées avant mesure. `[verified]`
- **Les mémoires apprises** (« ne jamais revendiquer un pourcentage d'une règle maison », « ne pas redesigner ce qu'il aime ») sont les bonnes leçons. `[verified]` `CONTEXTE-COMPLET §3`, `REPRISE`.

### Ce qui est faible
- **Le journal est la mémoire, et il fait 410 Ko en 567 lignes de 7 000 caractères** ; les agents en lisent « les 40 dernières lignes ». Les résumés en chat sont plus roses que les rapports (Léo l'a dit). `[verified]`
- **Les décisions s'empilent** : `REPRISE-2026-10-08.md` liste 6 décisions en attente ; 5 maquettes de plus le même jour ; 118 tours Loupe (PUP-R118) pour un nombre de décisions prises bien plus faible. `[verified]`
- **Des sessions parallèles éditent le même fichier** (« v721–723 sont d'autres sessions, entrées par git add -A, non relues ici ») ; `peintre-moteur.js` en trois états différents le même jour. `[verified]`
- **« Pour le budget »** : revues sautées, maquettes non regardées en mode nuit, seuils non écoutés. `[verified]`

### Ce qui est faux, survendu ou du travail perdu
- **La méthode optimise ce que Léo réagit en premier** : il a dit « très très très cool » aux pochettes et « ça sert à quoi ce que tu fais, si c'est juste pour générer des trucs faux ? » au reste ; les agents ont produit six entrées de pochettes. Personne ne refuse une demande. `[verified]` `HANDOFF.md:556-567`.
- **Sept agents « marketing »** (`code/claude/agents/`) ont produit une stratégie de marque, une tagline, un « big idea » (`STATUS.md`) avant qu'un seul client existe. Travail perdu. `[verified]`
- **760 versions en 15 jours** : la version n'est plus un jalon, c'est un réflexe ; aucune ne correspond à un état « testé par un humain ». `[inferred]`
- **Ce que la méthode rate systématiquement** : (1) elle confond « le code est d'accord avec lui-même » et « c'est vrai » ; (2) elle écrit la règle après avoir vu les cas ; (3) elle n'a pas de « non » : aucune entrée du journal ne dit « on ne fait pas ça » ; (4) elle produit du code à la vitesse de la conversation et des preuves à la vitesse d'un humain seul, qui n'a pas le temps.

### Ce que je ferais à la place (le processus pour 18 jours)
1. **Une session à la fois sur le produit.** Les autres sessions ne touchent qu'à des fichiers à elles (bancs, rapports). Un intégrateur (un agent, un rôle) relit chaque diff avant `versions.py save`.
2. **Une « définition de fini » en trois lignes dans `CLAUDE.md`** : un humain a vu l'écran (capture jointe) ; un humain a entendu le son quand il y a du son (fichier d'écoute daté) ; `run_all.py` est vert. Sans les trois, l'entrée du journal dit « non vérifié », pas « 0 erreur JS ».
3. **Gel fonctionnel** jusqu'au 26 : chaque demande de Léo qui n'est pas dans `BETA-26-OCT.md` reçoit la réponse « après la bêta », écrite dans le journal.
4. **Un build par jour**, installé sur un Mac vierge, ouvert par quelqu'un qui n'est pas Léo (un choriste, 15 minutes, chronométré).
5. **Le journal devient un changelog de ≤ 20 lignes par version** ; le reste va dans `rapports/`. `CONTEXTE-COMPLET.md` est remplacé par `ARCHITECTURE.md` (2 pages) généré depuis le code.
6. **Chaque chiffre a un fichier** : pas de pourcentage dans le journal sans le chemin du script qui l'a produit et de la sortie brute.

### Quoi arrêter
Les sessions parallèles sur `app.js` ; les maquettes (28 dossiers, dont `tenir-ma-voix` refusée, `chef` et `chef-v2` à 2 000 lignes jamais branchées) ; les agents marketing ; les versions sans jalon ; les résumés en chat plus doux que les rapports.

---

## 12. Nos affirmations, attaquées (section 5 du brief)

| Affirmation | Verdict | Expérience qui tranche, et sa taille minimale |
|---|---|---|
| « 29 mesures justes sur 29 » (Verdi) | **À supprimer.** Référence par l'agent, 15/29 vues pendant l'écriture des règles, triolet exclu par `holdout.py:43`, piano et paroles non évalués. `[verified]` | 20 mesures tirées par graine aléatoire sur des pages jamais ouvertes, transcrites par une personne (Léo, à la Loupe), notées sur hauteur + durée + syllabe. Une soirée. |
| « 6 justes sur 8 » (écoute, règle 2) | **Non mesurable.** Règle écrite après avoir vu les cas, notée sur ces cas (`ecoute/e3b_regle2.py:1`). `[verified]` | Geler la règle, la noter sur 20 autres notes chantées (pages 2, 4, 14 du zip). Deux heures. |
| « 7 justes sur 21 » (écoute, règle 1) | Honnête, et c'est un résultat **négatif** : l'oreille corrige les armures, pas les notes. | Aucune : conclure et arrêter. |
| « Vérifié en headless, 0 erreur JS » | **Ne prouve que l'absence de crash** avec écritures stubées et son interdit (`simplicity_report.py:85-90`). `[verified]` | Une capture par écran comparée à une référence (diff de pixels) ; une écoute humaine datée. |
| « 68,1 / 82,1 / 89,4 % → 70,8 / 84,7 / 90,9 % » (calage) | **Mélange vérité humaine et accord machine** (`bench_calage.py:113-135`) ; 11 pièces du même enregistrement, une voix, canal voix fourni. Les gains de +2 points sont dans le bruit. `[verified]` | Publier `n_fixed` par pièce et le chiffre sur ces notes seules ; ajouter 3 pièces d'un autre éditeur et 1 soliste. |
| « within50 : N % des attaques tombent juste » | **Auto-sélectif** (`pipeline.py:907`). Le projet le sait (`calage_truth.py:3-4`). `[verified]` | Rejouer sur le calage Verdi conservé : afficher `tot/total`. Dix minutes. |
| « Bien calé », « Calé, quelques endroits à revoir » | **Mots de l'utilisateur, pas de la machine** (`library.js:1532`). `[verified]` | Faire passer le Verdi par l'écran : les fenêtres tombent-elles sur les mesures 255-308 ? |
| « Lue à 81 % sûre » (maquette) | **Formule maison** sur des mesures qui ne tombent pas juste ; l'IA de la maquette est simulée. `[verified]` | Supprimer. Afficher les comptes bruts (« N mesures chantées ne tombent pas juste, paroles non vérifiées »). |
| « 548/548 », « pkg 1490 ok » | **Assertions × pièces de Léo, code contre lui-même** (`score_edits_test.mjs:16`). `[verified]` | Écrire « N assertions distinctes » ; un oracle écrit à la main sur 3 partitions publiques. |
| « Lecteur local 74-81 % des mesures justes » | Métrique sans paroles ni vérité humaine documentée. `[inferred]` | Même protocole que la ligne 1, sur 2 pages de chœur. |
| « Desdemona 195/195, 507 syllabes sur une ligne » | **Vrai et trompeur** : Emilia tombe à 13 syllabes, Voix 1 gagne 300 notes, une mesure apparaît. `[verified]` | Compter par mesure les syllabes d'Emilia sur les pages 2, 4, 14 et comparer à V2. Une heure. |
| « 0 fausse note générée », « 19/26 règles des grands accompagnateurs » (Pianiste) | **Non lu en détail** (lecteur coupé). Le vérificateur et les règles sont du même agent : c'est un contrôle de cohérence interne, pas une qualité musicale. `[guess]` | Écoute à l'aveugle par deux musiciens de 3 extraits : Partition / Pianiste / IA, notés 1-5. |
| « Rien n'est jamais effacé » | **Faux à trois endroits** (`serve.py:743`, `:658`, `versions.py:43`). `[verified]` | Aucune : corriger. |
| « Peinture 14,4 ms à 300 px » | Plausible, mesuré sur un seul Mac. `[inferred]` | Sans intérêt pour le produit. |

---

## 13. Réponses aux douze questions

Les questions 1, 2, 7 et 8 ont été traitées par un agent dédié (confiance haute) ; les autres par le relecteur principal seul.

**1. La lecture locale suffira-t-elle un jour ?** Non, ni le 26 octobre ni avec les moteurs qui existent : 66-81 % de mesures justes sur les pièces de réglage, 0-60 % sur les pièces jamais vues, paroles ni mesurées ni lisibles, les deux moteurs utilisables sont AGPL. Construire autour d'une partition déjà numérique (fichier du chef, CPDL, IMSLP, MuseScore.com) avec « Trouver cette partition » ; la lecture OMR devient « Brouillon (à vérifier) » et un outil interne de préparation ; relecture humaine (Léo ou un prestataire) pour ce qui est livré ; opéra hors périmètre. Sur la boîte : « Ta partition en MusicXML et ton enregistrement, calés. Un PDF ? Pupitre t'aide à trouver l'édition numérique ou te laisse travailler sur ta page. » Détail en section 3.

**2. Pupitre doit-il marcher sans partition lisible par la machine ?** Oui, et ce doit être le chemin par défaut de la bêta pour les PDF et photos : la page de l'utilisateur (redressée par `lecture-main/warp.js`, systèmes trouvés par `structure_image.py` / `strips.py` sans lecteur AGPL) est la partition à l'écran, les phrases qu'il pose sont le calage, la partition machine devient optionnelle et réduite à « ma ligne » (une portée pointée par l'utilisateur, ou entrée au clavier). Le seul chemin « sans partition » qui existe, « Partir de l'enregistrement », est la mauvaise réponse à cette bonne question (3,9 % de mots justes sur un vrai chœur selon l'agent ; et `draft_auto.py` importe encore un aligneur non vendable, `:128`, `:45`) : le cacher. Expérience à zéro code pour le jour 1 : les pages du Verdi dans « Images du morceau » tournées aux bons moments ; si Léo répète une semaine avec ça, l'écran « pages » vaut la bêta.

**3. Comment obtenir un calage fiable et un signal honnête ?** Mesurer la couverture (voix détectées vs notes chantées placées), le tempo par bloc de mesures, et la part des notes de ma voix entendues à l'endroit prévu sur **toutes** les notes ; afficher trois états ; refuser avant d'afficher dans les trois cas de la section 4 ; donner à align3 un état terminal ; remplacer les mots par des phrases ; séparer vérité humaine et accord machine dans le banc. Aujourd'hui aucun de ces cinq éléments n'existe.

**4. « Caler par phrases » est-il la bonne interaction ?** Oui : c'est la seule que Léo sait faire et la seule qui apporte une information de région (une phrase dit « d'ici à là », un mot ne dit rien sur la suite). Variante 1 « Au fil de l'écoute » comme capture (Espace = début, tenir = fin, « Oups » et « je ne l'entends pas » comme seuls autres gestes), variante 2 comme retouche sur le même écran, pas de variante 3. Modèle minimal d'une phrase : `{voice, qStart, qEnd, t0, t1, by, checked, absent, text}`. Les bords deviennent des ancres dures ; entre deux ancres, l'ancienne carte est remise à l'échelle ; au-delà de la dernière phrase, `null`. Classes d'échec couvertes : rentrées de chœur (34 %) et fins de pièce (14 %) par les débuts, points d'orgue (20 %) par les fins tenues, dérive (17 %) par la remise à l'échelle. Un débutant finit une pièce de 4 minutes (15-25 phrases) en 6-8 minutes avec la variante 1.

**5. Livrer Supra2-IMG ?** Non. Pas parce que c'est indéfendable (le risque est faible), mais parce que la fonction ne sert pas le job et casse la règle « tout vendable » posée la veille. Garder le pinceau maison. Si un jour il est livré : marquage visible dans l'interface, pas seulement dans les métadonnées PNG. Section 8.

**6. Lancer Audiveris et homr depuis une app payante ?** Sous-processus, installation par l'utilisateur depuis le site officiel, lecture de la sortie publique seulement : oui. Téléchargement et installation « en un clic » depuis l'app, import Python de homr, réparation du fichier interne `.omr` : non, c'est là que la ligne passe. Aujourd'hui homr et le `.omr` sont du mauvais côté. Une heure d'avocat avant la vente. Section 8.

**7. La base est-elle maintenable jusqu'à une vente ?** Pas en l'état, mais refactorisable sur place : `run_all.py`, lock des dépendances, CI macOS + Windows, `serve.py` derrière `main()`, suppression du code mort, API des modules figée, gel « `app.js` ne peut que rétrécir ». Ni réécriture (aucune spécification hors du code et de l'œil de Léo) ni « freeze and ship » (le paquet vendable n'a pas les fonctions). Section 5.

**8. La surface est-elle trop large ?** Oui, de loin. Bêta = « le Lecteur ». Sort du build : lecture de partition, Relire par une IA, Partir de l'enregistrement, Vidéo / Images, pochettes peintes et petite IA, Pianiste / IA, test de voix, Trouver ma note, Exporter pour le chœur, Raccourcir, Une portée par voix. Caché (mode Chanteur) : l'atelier de calage. Objectif chiffré avec `simplicity_report.py` : ≤ 12 commandes sur l'écran principal, ≤ 8 dans le panneau, ≤ 20 mots. Section 1.

**9. La taille est-elle acceptable ?** Le paquet de 209 Mo oui, mais il ne fait pas le produit. Le paquet complet ≈ 1,5 Go estimé (section 9) : acceptable seulement en téléchargement optionnel au premier besoin, jamais dans l'installeur. Dropper CREPE, Demucs optionnel, pianos compacts par défaut.

**10. Le risque commercial des enregistrements et partitions du client ?** Faible pour le client, réel pour l'éditeur si l'app **invite** à copier (« rip », « capture YouTube ») et **livre** des outils d'extraction. Rester un lecteur de fichiers possédés, pas un outil de copie ; `.pupitre` par audience sans audio acheté ; aucune phrase « YouTube » dans l'app ; une phrase de responsabilité ; marque à vérifier. Section 8.

**11. La plus petite version qu'un choriste paierait, et la distance ?** `[inferred]` Un choriste paie pour **sa partie, prête, sur son enregistrement de répétition**, avec boucle et ralenti : c'est ce que vendent les sites de pistes d'exercice, sans partition. Le MVP de Pupitre : ouvrir un `.pupitre` préparé, choisir sa voix, suivre, boucler, ralentir, entendre sa ligne, corriger un décalage grossier avec deux ou trois phrases. **Le code de cette version existe presque** (l'écran d'exercice, le `.pupitre`, le mode Lecteur `LEC` dans `app.js`). Ce qui manque : le paquet installable signé, le calage par phrases, l'absence de faux mots d'état, 8 pièces préparées, un test avec trois choristes. **Distance : 3 à 4 semaines d'un développeur compétent plus Léo**, pas 18 jours, et à condition que les pièces soient préparées par Léo. La version « le client apporte son PDF et son rip » est à plusieurs mois, si elle est atteignable.

**12. Ce qu'on ne voit pas du tout.** (1) **Qui prépare les pièces** : la question produit n° 1, jamais posée. (2) **Un deuxième utilisateur** : zéro à ce jour ; une heure avec un choriste vaut cent agents. (3) **Windows** : jamais lancé. (4) **Sauvegarde** : aucune hors du Mac. (5) **Support** : qui répond à 2 h du matin quand Demucs échoue chez un client ? Le modèle « calcul chez le client » impose un support que ni Léo ni des agents ne peuvent assurer ; c'est un argument pour « pièces préparées ». (6) **Mises à jour** : aucun mécanisme ; une app locale sans mise à jour accumule les bugs chez les clients. (7) **Accessibilité** : 150 boutons, des « ? », des gestes au clavier (⌘, ⇧) documentés dans l'aide ; rien n'a été testé avec un lecteur d'écran, et le mode nuit n'est « PAS vu » dans plusieurs entrées. (8) **Piraterie** : une app web locale est en clair ; la seule protection est la valeur des pièces préparées, pas le code. (9) **Prix** : jamais discuté ; les concurrents de pistes d'exercice vendent à la pièce ou par abonnement de chœur ; un logiciel seul sans contenu se vend mal à des amateurs. (10) **Concurrents** `[guess, non vérifié sur le web]` : Soundslice (synchronisation partition-audio, en ligne, payant), les éditeurs de pistes (Chord Perfect, ChoralTracks, Cyberbass), les lecteurs de partitions (forScore, Newzik), les apps OMR (PlayScore, ScanScore) ; personne ne promet « ton PDF + ton rip, alignés », et ce n'est probablement pas par manque d'idée. (11) **Sécurité** : `serve.py` est bon, mais un second serveur sur le même `Pupitre-data` casse les verrous. (12) **Migration entre versions** : `migrate_library.py` dépend de jsc et une `library.json` illisible n'a aucun chemin de réparation (`:40`, `api_library.py:3328`). (13) **Les MIDI du chœur** : « demander au chœur » est en attente depuis le 24 septembre. (14) **Le temps de Léo** : la ressource rare du projet est son oreille et son œil ; la méthode les dépense en tours Loupe sur des pochettes.

---

## 14. Top 15, du plus grave au moins grave

1. **L'app affiche « calé » sans fondement** (`library.js:1444`, `:1521`, `:1532` ; `pipeline.py:907` ; `pipeline.py:112`). **Action** : retirer ces mots aujourd'hui, un objet `quality`, trois états, trois refus.
2. **align3 ne sait pas s'arrêter avant la fin de la partition** (`align3.py:302`, `:370`) ; la seule comparaison de durées est morte (`api_library.py:549`). **Action** : contrôle de couverture + refus avant la bêta ; état terminal après.
3. **Le paquet vendable n'a ni calage ni lecture, et n'existe que pour Mac Apple Silicon** (`build_product.py:132`, `:199`, `:271`). **Action** : décider « bêta = Lecteur, Mac seulement » ; construire une fois le paquet complet et mesurer.
4. **La seule interaction que Léo sait faire (phrases) n'existe pas ; celle qui existe (mots) est une rustine** (`anchors.py:499`, `:599`, `:251` ; `caler-phrases.js:78`). **Action** : `phrase_map` + variante 1 dans l'app ; retirer « Caler avec des mots » de la surface client.
5. **La lecture de partition n'est pas un produit** (image 07 ; V2 13 syllabes ; `structure.py:518` ; moteurs AGPL). **Action** : chemin principal = fichier numérique ; page comme partition sinon ; OMR = outil interne.
6. **Les preuves sont circulaires** (`calage_refs/bach.json:58`, `ref.py:1`, `holdout.py:43`, `score_edits_test.mjs:16`, `simplicity_report.py:90`). **Action** : jeu d'or humain ; « vérifié » interdit sans artefact ; supprimer « 29/29 », « 548 », « 81 % sûre » des documents.
7. **Aucune sauvegarde hors du Mac, et « Sauvegarder » oublie les réglages** (`serve.py:101`, `api_library.py:755`). **Action** : « Sauvegarder tout » complet + miroir par défaut.
8. **« Rien n'est jamais effacé » est faux** (`serve.py:743`, `:658`, `versions.py:43`) et un test rouge sur les corrections est toléré depuis le 1er octobre (`SKILL.md:26`). **Action** : corriger les trois, décider le test.
9. **homr est une bibliothèque importée, le `.omr` d'Audiveris est réparé par l'app** : du mauvais côté de la ligne AGPL. **Action** : homr externe comme Audiveris ; ne plus toucher au `.omr` ; une heure d'avocat.
10. **La suite de tests ne tourne que sur le Mac de Léo et neuf « tests » n'ont aucune assertion** (`calage_truth.py:57`, `:423`). **Action** : `run_all.py`, planchers gelés, unit/bench séparés, CI.
11. **Cinq confiances incompatibles et un pipeline qui ne refuse jamais** (`pipeline.py:789`, `:489`). **Action** : un seul `quality`, supprimer `barConfidence`, `bar_cost`, `sync.confidence`.
12. **Le document que le client colle à son IA parle de Léo et de son serveur** (`POUR-UNE-IA.md:19-22`) et la fonction n'a jamais tourné pour de vrai. **Action** : cacher « Relire par une IA » ; réécrire en 600 mots ; tester une fois avec trois vraies IA.
13. **La surface : cinq produits, 150 boutons, 16 modules, 28 maquettes** ; `STATUS.md` et `TODO-LEO.md` du 24 septembre. **Action** : `BETA-26-OCT.md`, mode Chanteur, `APP_SKIP` étendu, maquettes hors de `app/`.
14. **Chemins Mac en dur dans 15 fichiers, `jsc` requis à la première ouverture** (`omr.py:34-36`, `migrate_library.py:40`). **Action** : `tools/tool.py`, test « pas de `/Users/` », Node ou bibliothèque vide à la place de jsc.
15. **La méthode : agents parallèles sans intégrateur, pochettes le jour où tout est faux, 760 versions, résumés plus doux que les rapports** (`HANDOFF.md:556-567`). **Action** : une session produit, définition de fini à trois lignes, un build par jour, gel.

---

## 15. Liste de ce qu'il faut arrêter

- Afficher « calé », « Bien calé », « N % des attaques » tant que `quality` n'existe pas.
- Toute nouvelle règle de lecture, tout nouveau moteur OMR, toute « nuit » de retuning.
- « Caler avec des mots », « Recaler ce passage », la variante 3 des phrases.
- Les pochettes peintes, la petite IA, les rosaces, les « détails qui se construisent ».
- Le Pianiste, Chanter / Entraînement, la vidéo, l'impression, l'export mix, le mode chef : hors de la bêta.
- Les maquettes dans `app/maquettes/` (28 dossiers) ; une seule par semaine, hors de l'arbre servi, supprimée après décision.
- Les sessions parallèles sur `app.js`, `index.html`, `serve.py`, `api_library.py`.
- Les commentaires datés dans le code ; les versions sans jalon ; les résumés en chat plus doux que les rapports.
- Les chiffres sans fichier source ; « 0 erreur JS » comme preuve ; les règles écrites après avoir vu les cas.
- Les agents marketing ; la stratégie de marque ; la tagline.
- Promettre Windows, « 100 % hors ligne » et « clé USB » avant qu'un runner Windows soit vert.
- Les exceptions à « tout vendable ».
- Écrire « jamais » dans un document tant qu'un `os.remove` existe.

---

## 16. « Si j'avais une semaine avant une bêta le 26 octobre »

Hypothèse : la bêta est « le Lecteur », Mac Apple Silicon, pièces préparées par Léo. Tout le reste est explicitement laissé cassé.

| Jour | Faire | Laisser cassé, et le dire |
|---|---|---|
| **J1** | Écrire `BETA-26-OCT.md` (≤ 12 commandes, ≤ 8 dans le panneau, gel). Retirer les mots « calé » et `within50` de `library.js`, `app.js`, `diagnose`. Mode Chanteur par défaut, forçage « atelier » supprimé. | Le calage automatique reste tel quel (pas de refus encore) : « Pupitre peut se tromper ; vérifie à l'oreille » écrit sur l'écran de contrôle. |
| **J2** | `APP_SKIP` étendu (lecture, relire, iareview, portees, raccourcir, sanspartition, peintre, video, train sauf « une prise »). `loadModule` filtré par `SHIPPED`. Boutons morts retirés d'`index.html`. `simplicity_report.py` avec objectif chiffré. | Lecture de partition, Relire par une IA : absents, pas « bientôt ». |
| **J3** | `run_all.py` portable (node + python, code de sortie), `requirements.txt` + lock, `py_compile` + `node --check`, planchers des bancs. Décider le test rouge d'`app_flow_post.js:56`. | CI Windows : non ; écrit dans le README. |
| **J4** | Contrôle de couverture dans `pipeline.check` (voix détectées vs notes placées, tempo par bloc) + trois refus en français ; fenêtres d'écoute choisies par `quality`. | État terminal d'align3 : non ; une partition plus longue que l'enregistrement est **refusée**, pas alignée. |
| **J5** | « Sauvegarder tout » complet (réglages inclus) + miroir par défaut ; suppression de l'effacement à 1 000 ; un seul écrivain pour `recordings.json`. Dossier d'une pièce réduit à 7 commandes (maquette « Affiche »). | Prises en IndexedDB : laissé, avec une phrase d'avertissement. |
| **J6** | Construire le paquet, signer, **notariser**, installer sur un Mac vierge, faire ouvrir par un choriste (15 min chronométrées, sans aide). Corriger uniquement ce qui l'a bloqué. | Intel, Windows : non. |
| **J7** | Préparer 5 à 8 pièces du répertoire d'Éolides en `.pupitre` (MusicXML propre + enregistrement de répétition + calage vérifié à l'oreille par Léo). Écrire la page « ce que Pupitre fait et ne fait pas ». | Le calage par phrases : maquette validée, pas livrée ; les testeurs corrigent dans l'atelier ou n'y touchent pas. |

Ce qui n'entre pas dans la semaine et qu'il faut dire aux testeurs : apporter son propre PDF, apporter son propre enregistrement sans l'aide de Léo, Windows, Intel, Chanter, Pianiste.

---

## 17. Pour Léo, sans jargon

Tu as construit, en deux semaines, un très bon outil **pour toi** : tes pièces, bien préparées à la main, s'ouvrent, se jouent, se bouclent, et ta ligne sonne au piano par-dessus l'enregistrement. Ça, c'est vrai, et c'est beau à l'écran. Ce que tu n'as pas, c'est le produit que tu racontes : une app où quelqu'un d'autre dépose son PDF et son enregistrement et obtient la même chose. Sur le seul morceau qui n'était pas à toi, la lecture a sorti des paroles illisibles, l'alignement a écrasé cinq minutes de musique dans une minute et demie, et l'app t'a dit « calé ». Elle te l'a dit parce que le mot « calé » ne vient d'aucune mesure : il s'affiche dès que le calcul se termine, et le pourcentage qui l'accompagne ne compte que les notes déjà bien placées. Les chiffres rassurants des rapports (« 29 sur 29 », « 548 tests », « 0 erreur ») ont été fabriqués et notés par les mêmes agents, sur tes propres pièces, sans qu'une oreille ni un œil humain ne tranche, et le paquet qu'on peut vendre aujourd'hui ne contient ni l'alignement ni la lecture, et ne marche que sur ton type de Mac. Les pochettes peintes sont jolies et ne servent à rien pour ça. Le 26 octobre, tu peux livrer une chose honnête : un lecteur de pièces que **toi** tu prépares pour ton chœur, sur Mac, sans promesse de lecture automatique ni de Windows. Si tu fais ça, tu as un vrai outil entre les mains des choristes en novembre, et tu apprends enfin quelque chose que ni toi ni les agents ne pouvez deviner : ce qu'un autre chanteur comprend, ou pas. Si tu continues à vouloir tout, tu auras le 26 octobre une démo qui ment poliment, comme aujourd'hui.
