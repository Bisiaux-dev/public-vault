---
title: Factories
tags: [2d-game, dev, taches]
equipe: ["[[Pierre]]"]
---

Un seul endroit crée les bases, les pièces et les boss (pattern Factory).

Retour : [[2D-Game/taches/pierre/index|Tâches de Pierre]] · [[2D-Game/ideas/pierre/Bullet hell roguelike - UML|UML]] §3.4, §3.5

- [ ] `PartType`, `ShipBaseId` et le record `Stats` avec `plus()`
- [ ] `PartCatalog.create(PartType)` : crée la bonne pièce pour chaque type (`COCKPIT` = un `Weapon` avec les stats du `CANNON`)
- [ ] `ShipBase` (abstraite), `StandardBase` et `DashBase` avec `applyPassive()` (les deux bases sont disponibles dès le début)
- [ ] `ShipFactory.create(ShipBaseId)` : renvoie un `PlayerShip` monté sur la bonne base, avec le `COCKPIT` posé sur la case centrale
- [ ] `BossFactory.create(bossNumber, DifficultyScaler)` : le boss suivant de la liste, en boucle, avec ses `ShotModifiers` et ses PV ajustés *(attend [[2D-Game/taches/chris/Patterns|les patterns et `Boss` de Chris]])*
- [ ] Tests : chaque type crée la bonne classe, les stats de base sont correctes, la difficulté est bien appliquée
