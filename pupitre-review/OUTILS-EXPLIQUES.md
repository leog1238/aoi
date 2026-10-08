# Les outils open source, expliqués simplement (pour Léo)

Pour chaque outil : ce que c'est, et à quoi il sert dans Pupitre. Les licences sont citées de mémoire : à revérifier avant de livrer.
Le tableau court est dans `OUTILS-OPEN-SOURCE.md`.

---

## 1. Ce qui débloque le plus

### Les jeux de données de chercheurs (Dagstuhl ChoirSet, Choral Singing Dataset, Schubert Winterreise Dataset)
Ce sont des enregistrements gratuits publiés par des universités : des chœurs et des chanteurs solistes, avec pour chaque note le moment exact où elle est chantée, relevé à la main par des musicologues. Aujourd'hui, Pupitre juge son calage avec des références écrites par les agents eux-mêmes, ou avec tes propres corrections sur une seule voix d'un seul enregistrement : c'est l'élève qui corrige sa propre copie. Avec ces données, on fait passer le calage de Pupitre sur des morceaux qu'il n'a jamais vus, et on compare à une vérité faite par d'autres. Si Pupitre tombe juste à 90 % là-dessus, c'est un vrai chiffre. S'il tombe à 50 %, on le sait avant les clients. On ne les met pas dans l'app : ils servent seulement à mesurer.

### mir_eval
C'est une petite boîte à outils de calcul utilisée par toute la recherche en musique pour dire « cet alignement est juste à tant de pour-cent, à 50 millisecondes près ». Aujourd'hui, chaque agent a inventé sa propre façon de compter, qui changeait d'un essai à l'autre : tu l'avais remarqué (« les chiffres changent à chaque fois »). Avec mir_eval, la règle de mesure est la même que celle des chercheurs et elle ne bouge plus. Les chiffres deviennent comparables d'une semaine à l'autre, et avec ceux publiés ailleurs.

### Silero VAD
« VAD » veut dire « détection d'activité vocale » : un tout petit programme (quelques Mo) qui écoute un enregistrement et dit « ici quelqu'un chante, ici personne ». C'est exactement ce qui a manqué sur le Verdi : l'enregistrement s'arrêtait à 12:09, mais la partition continuait avec l'Ave Maria, et Pupitre a écrasé cinq minutes de musique dans la dernière minute sans rien dire. Avec Silero VAD, Pupitre sait où la voix s'arrête vraiment, compare avec la fin de la partition, et peut dire honnêtement : « l'enregistrement s'arrête avant la fin de la partition, montre-moi où ». C'est la base du signal de confiance honnête.

### MuseScore 4 (comme programme séparé)
MuseScore est le logiciel gratuit d'écriture de partitions le plus utilisé au monde : on voit la partition, on clique sur une note fausse, on la corrige, on enregistre. Pupitre a commencé à construire son propre éditeur de notes, ce qui est un énorme travail et ne sera jamais aussi bon. À la place : un bouton « Corriger dans MuseScore » ouvre la partition dans MuseScore, la personne corrige, enregistre, et Pupitre relit le fichier. MuseScore est sous licence GPL, mais comme c'est l'utilisateur qui l'installe lui-même et que Pupitre ne fait que lui passer un fichier, il n'y a pas de problème pour vendre Pupitre (comme pour Audiveris).

### OpenScore, le corpus de music21 et CPDL
Ce sont des bibliothèques de partitions déjà numériques et propres, tapées note par note par des bénévoles : OpenScore (des milliers de mélodies et de lieder, libres de tout droit), music21 (une collection de chorals et de classiques), CPDL (la grande bibliothèque gratuite de musique chorale). La leçon du Verdi est que lire une partition à partir d'une photo ou d'un PDF donne des résultats faux, surtout les paroles. Le chemin principal de Pupitre devrait donc être : « trouve la version numérique de ta pièce ». Un bouton « Trouver cette partition » ouvre ces sites avec le titre déjà rempli. Pour beaucoup de pièces de chœur classiques, la version propre existe déjà.

### wavesurfer.js (avec ses « régions »)
C'est une bibliothèque qui dessine la forme d'onde d'un enregistrement dans une page web (la bande grise qui monte et descend avec le son), et qui permet d'y poser des zones colorées qu'on tire avec la souris. C'est exactement ton geste : « d'ici jusque-là, c'est cette phrase ». On pose une zone par phrase, on tire ses bords, on réécoute. C'est la base toute faite de « Caler par phrases », au lieu de la reconstruire à la main. En plus, elle peut remplacer peaks.js, que Pupitre utilise aujourd'hui pour la même chose mais avec une licence plus contraignante.

---

## 2. À garder (déjà de bons choix)

### synctoolbox
C'est le moteur qui compare la partition et l'enregistrement pour trouver où ils correspondent. Il est déjà au cœur du calage automatique de Pupitre, il est bon et sa licence est libre. On le garde ; le problème n'est pas lui, c'est qu'on ne lui permet pas de dire « la partition continue après la fin de l'enregistrement ».

### Verovio
C'est ce qui dessine la partition à l'écran. Il sait aussi donner, pour chaque note, son moment dans la musique (la « timemap ») : Pupitre recalcule ça de son côté, ce qui fait du code en double. On garde Verovio et on utilise ce qu'il sait déjà faire.

### Signalsmith Stretch
C'est ce qui ralentit l'enregistrement sans changer la hauteur des notes, et qui transpose. Tu l'avais choisi à l'oreille en écoute à l'aveugle. Libre, léger, bon : on garde.

### beat_this, FCPE, basic-pitch
Trois petits modèles d'analyse du son : beat_this trouve les temps (la pulsation), FCPE trouve la hauteur de la voix chantée, basic-pitch reconnaît les notes jouées. Ils sont libres et utiles au calage automatique. On garde.

### Demucs
C'est le modèle qui sépare les voix de l'accompagnement dans un enregistrement. Utile pour le mixeur « Ce que j'entends », mais vos propres mesures montrent qu'il n'améliore presque pas le calage, et il est lourd (324 Mo) et lent sur un ordinateur ordinaire. On le garde, mais en option, à télécharger seulement si la personne le veut.

### smplr et les pianos libres
smplr est ce qui joue « ma ligne » au piano dans le navigateur ; les sons de piano utilisés sont libres de droits. On garde.

---

## 3. Réduire la taille et le support

### onnxruntime
Les petits modèles d'analyse du son ont besoin d'un « moteur » pour tourner. Aujourd'hui c'est PyTorch, un géant de plusieurs centaines de Mo, qui rend l'app énorme et difficile à installer sur Windows. onnxruntime est un moteur beaucoup plus léger, qui fait tourner les mêmes modèles une fois convertis. basic-pitch tourne déjà comme ça. Si on convertit aussi beat_this et FCPE, on peut se passer de PyTorch dans l'app de base, et l'installation passe de plus d'un gigaoctet à quelques centaines de Mo.

### Le décodage audio intégré au navigateur
Chrome, Safari et Edge savent déjà lire les fichiers mp3 et m4a tout seuls. Aujourd'hui, Pupitre passe souvent par ffmpeg, un programme externe qu'il faut fournir pour chaque système, avec des questions de licence (la version sur ton Mac est GPL, donc pas vendable). Pour simplement écouter, le navigateur suffit. On ne garde ffmpeg que pour l'analyse, dans une version LGPL fournie pour Mac et pour Windows.

### pYIN (dans librosa)
C'est une méthode de calcul classique pour trouver la hauteur d'une voix qui chante, sans intelligence artificielle ni modèle à télécharger. Pupitre utilise aujourd'hui CREPE pour ça, qui pèse 99 Mo. Pour le contrôle « est-ce que j'entends ta voix là où la partition la met ? », pYIN suffit largement et ne pèse rien de plus, car librosa est déjà installé.

### uv
C'est un outil qui installe des programmes Python très vite et de façon fiable. Il permettrait d'installer le « calage automatique » (le gros morceau, environ 1 Go) seulement quand la personne en a besoin, avec un message clair : « Installer le calage automatique (1 Go) ». L'app de base reste petite et s'installe en une minute.

### pywebview ou Tauri
Ce sont des « coques » qui transforment une page web en vraie application avec sa fenêtre et son icône, sur Mac et sur Windows. Aujourd'hui, la coque de Pupitre est écrite en Swift, qui ne marche que sur Mac. pywebview est le choix simple si on garde Python derrière ; Tauri est plus moderne et plus léger. Sans l'un des deux, la promesse « Mac et Windows » ne peut pas être tenue.

### Sparkle (Mac) et WinSparkle (Windows)
C'est le petit système qui affiche « Une nouvelle version de Pupitre est disponible, installer ? » et qui la télécharge de façon sûre. Pupitre n'en a pas : un client qui a un bug le garde pour toujours, et toi tu dois envoyer des fichiers à la main à chacun. Avant de vendre quoi que ce soit, il en faut un.

---

## 4. Rendre les preuves honnêtes

### Playwright, avec la comparaison de captures
Playwright pilote un navigateur tout seul : il ouvre Pupitre, clique, et peut prendre une photo de l'écran. La fonction intéressante : il compare cette photo à une photo de référence que tu as validée une fois, et signale dès qu'un pixel a bougé. Aujourd'hui, les agents écrivent « vérifié, 0 erreur JS », ce qui veut seulement dire « la page n'a pas planté ». Avec la comparaison de captures, un bouton qui disparaît ou un texte qui déborde est vu automatiquement, sans que tu aies à tout ouvrir.

### pytest et ruff
pytest lance tous les tests Python en une seule commande et dit clairement « vert » ou « rouge ». ruff relit le code Python et signale les erreurs bêtes (variable mal écrite, import oublié) avant même de lancer quoi que ce soit. Aujourd'hui, les tests de Pupitre se lancent un par un, certains n'ont aucune vérification dedans, et la plupart ne marchent que sur ton Mac. Avec ces deux outils, n'importe quel agent, ou un futur développeur, sait en dix secondes si quelque chose est cassé.

### La vérification de types de TypeScript, et ESLint
Le code de l'app est en JavaScript, un langage qui laisse passer beaucoup d'erreurs jusqu'au moment où l'utilisateur clique. TypeScript peut vérifier le JavaScript existant sans le réécrire, simplement en ajoutant une ligne en haut des fichiers : il signale par exemple qu'un module appelle une fonction qui n'existe plus. ESLint fait la même chose pour les fautes de style et les pièges connus. C'est une ceinture de sécurité pour un fichier de 8 500 lignes que plusieurs agents modifient en même temps.

### GitHub Actions (sur Mac et sur Windows)
C'est un service gratuit de GitHub qui, à chaque modification envoyée, lance automatiquement les tests sur un vrai Mac et un vrai PC Windows dans le cloud. Aujourd'hui, Pupitre n'a jamais tourné sur Windows. Avec ça, on le sait chaque jour : si l'app ne démarre pas sur Windows, la case devient rouge et l'agent doit réparer avant de continuer.

### pre-commit
C'est un garde-fou qui s'exécute juste avant chaque enregistrement de code (chaque « commit »). On lui donne des règles simples : refuser un chemin propre à ton Mac (`/Users/leomarthaler/…`), refuser un import d'outil interdit (madmom, homr), refuser un fichier de maquette dans l'app. Les règles de `CLAUDE.md` que les agents oublient deviennent impossibles à contourner.

---

## 5. À ne pas utiliser, et pourquoi

### Whisper et WhisperX pour les paroles chantées
Ce sont d'excellents outils pour transcrire la parole, mais ils sont mauvais sur le chant (voyelles tenues, vibrato, langues mélangées, orchestre derrière). Ils donneraient des paroles fausses avec un air sûr d'eux, exactement le problème qu'on veut éviter.

### MMS, madmom, ROSVOT
Ces modèles fonctionnent, mais leur licence interdit l'usage commercial. Les mettre dans un produit vendu, c'est s'exposer à devoir tout retirer. L'audit du 7 octobre les a déjà écartés : il ne faut pas les faire revenir.

### oemer, SMT
Ce sont d'autres lecteurs de partitions testés le 8 octobre : ils plantent ou lisent 0 % de la page. Inutile d'y passer du temps.

### homr importé directement dans Python
homr lit bien les photos de partitions, mais il est sous licence AGPL, la plus contraignante. L'utiliser comme une pièce de Pupitre obligerait à publier le code de Pupitre. S'il reste, il doit être un programme séparé que l'utilisateur installe lui-même, comme Audiveris.

### Spleeter
Il sépare les voix de l'accompagnement, comme Demucs, mais moins bien. Demucs est déjà là : pas besoin d'un deuxième.

### Les modèles qui génèrent des images
Ils ne servent pas le métier de Pupitre (apprendre sa voix), posent des questions de droits sur les images d'entraînement, et alourdissent l'app. Le pinceau maison suffit pour les pochettes.
