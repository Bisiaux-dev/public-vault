---
title: Boss rush
tags: [2d-game, dev, taches]
equipe: ["[[Pierre]]"]
---

Une run est un enchaînement de combats contre le boss (1 seul boss pour l'instant), qui revient en boucle avec une difficulté qui monte à chaque fois. Remplace l'ancienne tâche « Niveaux » (niveaux normaux et mini-boss mis de côté le 2026-11-24).

Retour : [[2D-Game/taches/pierre/index|Tâches de Pierre]] · [[2D-Game/ideas/pierre/Bullet hell roguelike - UML|UML]] §3.5

- [ ] `DifficultyScaler` : `increase()`, `enemyShots()` (vitesse et dégâts des balles), `bossHpMultiplier()`
- [x] `RewardGenerator.offer(count)` : tire des pièces parmi toutes les pièces sauf le `COCKPIT`, avec le `Random` de la run
- [ ] `Level` : un boss, ses balles et le `CollisionSystem`, `isCleared()` quand le boss est mort *(attend `Boss` et `CollisionSystem` de Chris)*
- [ ] `Run` : graine, `nextLevel()`, `onLevelCleared()` (score + 1), `onPlayerDeath()`
- [ ] Tests : avec la même graine, on obtient les mêmes pièces ; le boss revient après sa défaite ; la difficulté augmente à chaque boss
