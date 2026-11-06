---
title: Factories
tags: [2d-game, dev, taches]
equipe: ["[[Pierre]]"]
---

Un seul endroit crée les bases, les pièces et les ennemis (pattern Factory).

Retour : [[2D-Game/tache-pierre/index|Tâches de Pierre]] · [[2D-Game/ideas/pierre/Bullet hell roguelike - UML|UML]] §3.4, §3.5

- [ ] `PartType`, `ShipBaseId` et le record `Stats` avec `plus()`
- [ ] `PartCatalog.create(PartType)` : crée la bonne pièce pour chaque type
- [ ] `ShipBase` (abstraite), `StandardBase` et `DashBase` avec `applyPassive()`
- [ ] `ShipFactory.create(ShipBaseId)` : renvoie un `PlayerShip` monté sur la bonne base
- [ ] `EnemyFactory.create(EnemyType, pos, DifficultyScaler)` : ennemi avec ses patterns et ses `ShotModifiers` *(attend [[2D-Game/tache-chris/Patterns|les patterns de Chris]])*
- [ ] Tests : chaque type crée la bonne classe, les stats de base sont correctes, la difficulté est bien appliquée
