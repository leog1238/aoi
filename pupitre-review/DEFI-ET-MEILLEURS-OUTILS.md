# Tout remettre en cause : ce que je t'ai dit de faux, et le meilleur de ce qui existe (8 oct 2026)

Cette fois, les affirmations ont été vérifiées par recherche web (sources en bas). Ce qui n'a pas pu être confirmé est dit.

---

## 1. Ce que je t'ai dit de faux ou trop vite

| Ce que j'ai dit | Ce qui est vrai | Gravité |
|---|---|---|
| **Demucs : MIT, feu vert** | Le **code** est MIT, mais les **poids htdemucs** sont contestés : l'auteur a écrit qu'ils sont « fournis pour la recherche », une conversion ONNX les dit CC BY-NC 4.0, d'autres pages disent MIT. Rien n'est tranché, et le dépôt n'est plus maintenu. **Pupitre utilise htdemucs_ft par défaut.** | Élevée |
| **Silero VAD : MIT** | La licence MIT est confirmée par plusieurs pages, mais le README GitHub affiche un badge « CC BY-NC 4.0 ». À trancher en lisant le fichier LICENSE. | Moyenne |
| **synctoolbox : MIT** (le cœur d'align3) | Une base de données le liste en licence « other » (non standard). Je n'ai pas pu lire le texte. À vérifier dans le dépôt groupmm/synctoolbox. | Moyenne |
| **CPDL : copie libre mais pas la revente** | Faux dans l'autre sens : la licence CPDL par défaut autorise la distribution, même payante. Mais chaque partition a sa propre licence. Il faut regarder pièce par pièce. | Faible |
| **Il n'existe pas de corpus choral OpenScore** | Exact, mais je ne l'avais pas dit : OpenScore Lieder (≈1 356 mélodies, MusicXML, CC0) est parfait pour les **solistes**, pas pour les chœurs. | Info |
| **« Personne ne promet ton PDF + ton enregistrement, alignés »** | **Faux.** Soundslice lit les PDF et les photos (OCR musical), importe le MusicXML, synchronise ta partition avec ton MP3 ou une vidéo YouTube (tu tapes les temps forts au clavier), garde la page scannée synchronisée, et a une page « chœur » (solo / muet par voix). | **Très élevée** |
| **Les IA généralistes lisent mal les partitions** | Confirmé par la recherche, mais sur des modèles plus anciens : Gemini 2.5 Pro, meilleur modèle testé, 55 % de bonnes réponses quand la partition est une image contre 96 % quand elle est donnée en texte. Aucune étude publique ne mesure Claude, GPT-6 ou un modèle actuel sur photo → MusicXML. | Le test d'une soirée reste nécessaire |
| wavesurfer.js : BSD-3 | Probablement BSD-3, mais la page « about » du projet parle de CC BY 3.0. Lire le fichier LICENSE du dépôt. | Faible |
| Choral Singing Dataset, Dagstuhl ChoirSet | Licences non confirmées (Dagstuhl : probablement CC BY 4.0 pour l'article, pas vérifié pour les fichiers). **Schubert Winterreise Dataset : CC BY 3.0 confirmé**, avec une restriction sur une interprétation (SC06, ne pas redistribuer modifiée). | Moyenne |

---

## 2. La vraie remise en cause : Soundslice existe

C'est la découverte la plus importante de cette recherche, et elle touche le projet entier, pas un outil.

Soundslice fait déjà, en ligne et en abonnement :
- la lecture de PDF et de photos (encore en bêta chez eux, mais jugée par des utilisateurs comme la meilleure du marché, et qui **demande** quand elle doute au lieu d'inventer) ;
- l'import MusicXML ;
- la synchronisation avec **ton** enregistrement (MP3, YouTube), par taps sur les temps ;
- l'entraînement sur la page scannée elle-même, synchronisée (exactement la question 2 du brief) ;
- solo / muet par voix pour les chœurs ;
- une offre de licence pour l'intégrer ailleurs (≈ 100 $ pour les 200 premiers utilisateurs uniques par mois, puis 0,50 $ par utilisateur).

Et à côté : **carus music** (éditions Carus synchronisées avec de vrais enregistrements, « coach » qui joue ta voix au piano, ralenti), **Cantamus** (MusicXML → pistes chantées, la partition suit), **Singerhood**, **Choir Player**, **Chorilo**, **cori**.

**Donc la question n'est plus « peut-on faire Pupitre ? », c'est « pourquoi un choriste prendrait Pupitre plutôt que Soundslice ou Carus ? »**
Les seules réponses défendables que je vois :
1. **Hors ligne et local** : les enregistrements achetés ne quittent jamais l'ordinateur. Soundslice est dans le cloud.
2. **Pensé pour UN chanteur de chœur** : ma ligne au piano par-dessus le vrai enregistrement, mix des voix, transposition, ralenti qui garde la hauteur, en français, sans jargon.
3. **Un achat, pas un abonnement.**
4. **Ton chœur** : tu prépares les pièces pour Éolides, comme Carus le fait pour son catalogue.

Si aucune de ces quatre ne compte pour les choristes, Pupitre n'a pas de raison d'exister comme produit. Il faut le savoir **avant** d'écrire une ligne de plus.

**Expérience d'un soir, à faire en premier :** prendre l'essai gratuit de Soundslice, y mettre ton Cantique (PDF du chœur) et ton enregistrement, et ton Verdi. Noter : temps pour arriver à une pièce utilisable, qualité de la lecture, qualité de la synchro, ce qui manque par rapport à Pupitre. Si Soundslice fait 80 % de Pupitre sur ton Verdi, ta stratégie change (s'appuyer dessus, ou viser uniquement les 4 différences ci-dessus).

---

## 3. Le meilleur de ce qui existe, problème par problème

### Lire une partition (photo / PDF → MusicXML)
| Option | Type | Pour Pupitre |
|---|---|---|
| **ReadScoreLib** (le moteur de PlayScore 2) | Bibliothèque commerciale, sous licence pour développeurs, multiplateforme, sort du MusicXML | **La seule lecture qu'on peut embarquer légalement dans une app vendue, hors ligne.** Demander le prix et les conditions ; attention : l'app PlayScore interdit l'usage commercial de ses fichiers de sortie sans licence, il faut un contrat SDK. Tester sur le Verdi 3 pages et le Cantique. |
| **Soundslice (scanner)** | Service en ligne | Probablement le meilleur en usage réel, mais pas d'API de lecture : on ne peut pas l'appeler depuis Pupitre. |
| **Audiveris** | Libre, AGPL | Reste l'option gratuite, en programme séparé installé par l'utilisateur. |
| Recherche : **Transcoda, LEGATO 2, Zeus, SMT** | Modèles de recherche | Pas prêts pour un produit. Transcoda : 18 % d'erreur (OMR-NED) sur données synthétiques, 64 % sur de vieilles gravures. Code de Transcoda AGPL. À suivre, pas à brancher. |
| IA généraliste (GPT, Claude, Gemini) | En ligne | Aucune mesure publique sur photo → MusicXML. À tester toi-même (une soirée), ne pas supposer. |
| **Sheet Music Benchmark** (685 pages, mesure OMR-NED) | Banc de test standard (ISMIR 2026) | Remplace les règles de mesure maison : comparer n'importe quel lecteur sur la même règle que les chercheurs. |

### Caler la partition sur l'enregistrement
| Option | Type | Pour Pupitre |
|---|---|---|
| **synctoolbox** | Bibliothèque libre (licence à vérifier) | Déjà le cœur d'align3, de la même équipe qui fait la recherche du domaine (Erlangen). Garder, licence à confirmer. |
| **partitura** | Apache-2.0 | Manipulation propre de partitions et d'alignements en Python ; peut remplacer du code maison. |
| **Matchmaker** (ISMIR 2025) | Libre, licence non trouvée | Suivi de partition en temps réel, avec un banc d'évaluation, mais testé seulement sur piano. Intéressant pour « Chanter ». |
| Alignement chant ↔ partition avec **mesure de fiabilité** (SMC 2025) | Article, code annoncé mais non publié | Exactement le signal de confiance qui manque. Écrire aux auteurs. |
| **Rien de spécifique au chœur** n'existe en libre | — | C'est la confirmation que le « calage par phrases » fait main est la bonne voie, et un vrai différenciateur. |
| **Klangio API** | Commercial (REST) | Audio → partition (MusicXML). Utile seulement pour « je n'ai pas de partition » ; tarifs sur demande. |

### Séparer les voix de l'accompagnement
- **Demucs** : licence des poids contestée (voir section 1). Vos propres mesures disent qu'il n'aide presque pas le calage. **Le retirer du chemin par défaut**, le garder seulement pour le mixeur, et seulement après réponse écrite de Meta ou d'un avocat.
- **BS-RoFormer / Mel-RoFormer** : CC BY-NC-SA, interdit. **Spleeter, UVR** : code MIT, licence des poids non trouvée.
- Alternative sans risque : ne pas séparer, utiliser le canal voix quand il est fourni (cas des pistes d'exercice), sinon une détection d'activité vocale.

### Vérité pour mesurer honnêtement
- **Schubert Winterreise Dataset** (CC BY 3.0, confirmé) : voix soliste + piano, avec alignements. Idéal pour le cas « soliste / opéra » qui a cassé le Verdi.
- **Dagstuhl ChoirSet** (probablement CC BY 4.0) : chœur multipiste. À confirmer.
- **Choral Singing Dataset** : licence à lire sur Zenodo.

### Partitions déjà numériques
- **OpenScore Lieder** (CC0, ≈1 356 mélodies) pour les solistes.
- **CPDL** : licence par pièce, la licence par défaut autorise la distribution même payante, avec l'attribution conservée.
- **IMSLP** : surtout des PDF ; pas de conditions d'API officielles trouvées.

---

## 4. Ce que je ferais, dans l'ordre (mis à jour)

1. **Un soir : tester Soundslice** sur ton Cantique, ton Loch et ton Verdi. Décider ensuite si Pupitre est un produit ou ton outil.
2. **Une semaine : écrire** à Organum (ReadScoreLib, prix et test), à Meta / Demucs (poids), lire les LICENSE de synctoolbox, Silero et wavesurfer.
3. **Une soirée : le test « IA généraliste »** sur 30 mesures du Verdi vérifiées par toi.
4. Mesurer le calage sur Schubert Winterreise (soliste) et Dagstuhl (chœur) au lieu de tes seules pièces.
5. Seulement ensuite : construire, en t'appuyant sur ce qui gagne.

---

## Sources
- Sheet Music Benchmark : https://arxiv.org/abs/2506.10488
- Transcoda : https://arxiv.org/abs/2605.10835
- OMR sur manuscrits réels (Zeus, PaliGemma) : https://arxiv.org/html/2606.09479v1
- LEGATO 2 : https://arxiv.org/pdf/2607.05769
- MuSViT : https://arxiv.org/html/2606.31811v1
- Lecture de partition par IA généraliste (Gemini 2.5 Pro 55 % image / 96 % texte) : https://arxiv.org/pdf/2509.04059
- NOTA : https://arxiv.org/html/2502.14893v1
- Alignement chant ↔ partition avec fiabilité (SMC 2025) : https://zenodo.org/records/15838731
- Matchmaker : https://arxiv.org/abs/2510.10087
- pyAMPACT : https://arxiv.org/pdf/2412.05436
- Demucs, discussion sur la licence des poids : https://huggingface.co/adefossez/HTDemucs/discussions/1
- Demucs ONNX (CC BY-NC) : https://huggingface.co/HRSadeghi/demucs-onnx
- Silero VAD (MIT) : https://www.fon.hum.uva.nl/praat/manual/Silero_VAD_MIT_License.html ; https://pypi.org/project/silero-vad
- beat_this (MIT, code et poids) : https://huggingface.co/cstr/beat-this-GGUF
- wavesurfer.js : https://wavesurfer.xyz/about ; https://sourceforge.net/mirror/wavesurfer-js/
- synctoolbox : https://science.ecosyste.ms/projects/1787 ; https://pypi.org/project/synctoolbox
- partitura (Apache-2.0) : https://pypistats.org/packages/partitura
- Schubert Winterreise Dataset (CC BY 3.0) : https://zenodo.org/record/3968389
- Dagstuhl ChoirSet : https://audiolabs-erlangen.com/resources/MIR/2020-DagstuhlChoirSet
- Choral Singing Dataset : https://zenodo.org/record/2649950
- CPDL : https://en.wikipedia.org/wiki/Choral_Public_Domain_Library
- IMSLP licence : https://imslp.org/wiki/CC
- OpenScore (CC0) : https://creativecommons.org/2017/06/30/openscores-plans-liberate-sheet-music/
- Soundslice, chœur : https://www.soundslice.com/practice-choir/
- Soundslice, PDF et photos : https://www.soundslice.com/help/en/creating/pdf-import/293/overview/ ; https://www.soundslice.com/help/en/player/advanced/332/practicing-with-pdfs-photos/
- Soundslice, licence : https://www.soundslice.com/licensing/
- Avis utilisateurs sur Soundslice : https://vi-control.net/community/threads/a-tool-to-scan-printed-music-that-actuay-works-at-last.158673/
- PlayScore 2 (avis Sound On Sound) : https://www.soundonsound.com/reviews/playscore-2
- ReadScoreLib / SeeScore : https://www.seescore.co.uk/?p=6
- Klangio : https://klang.io/products/
- carus music : https://www.carus-verlag.com/en/attributes/carus-music-the-choir-app/
- Comparatif d'apps chorales 2026 : https://cori.music/en/blog/choir-app-2026
- Cantamus : https://cantamus.app/
- Singerhood : https://singerhood.com/
- Choir Player : https://www.choirplayer.com/
- Chorilo : https://www.chorilo.com/sheet-music-management
