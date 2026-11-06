---
title: Niveaux
tags: [2d-game, dev, taches]
equipe: ["[[Pierre]]"]
---

Niveaux faits main, tirés dans un ordre aléatoire, avec une difficulté qui monte.

Retour : [[2D-Game/taches/pierre/index|Tâches de Pierre]] · [[2D-Game/ideas/pierre/Bullet hell roguelike - UML|UML]] §3.5

- [ ] Records `LevelDefinition`, `SpawnEvent`, et l'enum `LevelType`
- [ ] Format de fichier des niveaux (JSON) et `LevelLoader.loadAll(FileHandle)`
- [ ] `LevelPool.draw(n)` : boss tous les 10 niveaux, mini-boss tous les 5, sinon un niveau normal tiré sans remise, puis remélangé
- [ ] `Level` : fait apparaître les ennemis selon la timeline, `isCleared()`
- [ ] `DifficultyScaler` : vitesse et dégâts des balles, PV des boss selon le niveau
- [ ] `RewardGenerator.offer(count)` : tire des pièces parmi celles débloquées
- [ ] `Run` : graine, `nextLevel()`, `onLevelCleared()`, `onPlayerDeath()`
- [ ] Créer au moins 20 niveaux à la main (pour que l'ordre aléatoire reste varié)
- [ ] Tests : avec la même graine, on obtient les mêmes niveaux ; les boss et mini-boss tombent aux bons numéros ; la difficulté augmente
