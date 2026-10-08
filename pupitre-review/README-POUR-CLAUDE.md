# Revue hostile de Pupitre (8 octobre 2026) — mode d'emploi pour l'agent qui lit ce dossier

Ce dossier est le résultat d'une revue externe du zip `Pupitre-pour-une-IA.zip`, faite par un modèle qui n'a pas construit l'app.

- `REVUE-PUPITRE-2026-10-08.md` : le rapport complet en français, structuré selon `00-LIS-MOI-D-ABORD.md`
  (11 domaines, affirmations attaquées, 12 questions, top 15, liste d'arrêt, plan d'une semaine, paragraphe pour Léo).
  Chaque constat porte un chemin et un numéro de ligne, et une étiquette [verified] / [inferred] / [guess].
- `constats.json` : les 82 constats bruts (id, titre, sévérité, catégorie, preuves file:line, détail, ce qu'il faudrait faire à la place,
  expérience qui tranche). `status: confirmed` = recontrôlé par le relecteur principal ; `status: unverified` = constat d'un lecteur
  spécialisé, non réfuté par un second agent (la vérification contradictoire a été coupée pour le budget).
- `constats-compact.json` : la même liste en court (pour trier, classer, planifier).
- `reponses-questions-1-2-7-8.json` : réponses détaillées des agents dédiés aux questions 1, 2, 7 et 8 du brief (raisonnement,
  preuves, actions ordonnées). Les questions 3-6 et 9-12 sont répondues dans le rapport par le relecteur principal.
- `sorties-brutes-agents/` : les sorties JSON telles que récoltées (lecteurs « calage », « phrases », « tests », « données »,
  les 4 questions, et `0-lead.json` = constats du relecteur principal).

Comment s'en servir : lire d'abord « Le verdict en une page » et le « Top 15 » du rapport ; puis, pour chaque action, ouvrir
le constat correspondant dans `constats.json` (champs `evidence` et `what_instead`) avant de toucher au code.
Ne pas « corriger » un constat sans rouvrir la ligne citée : certains numéros de ligne peuvent être décalés de quelques lignes.
