---
title: Patterns
tags: [2d-game, dev, taches]
equipe: ["[[Chris]]"]
---

Les comportements des ennemis et des boss (patterns Strategy et State).

Retour : [[2D-Game/taches/chris/index|Tâches de Chris]] · [[2D-Game/ideas/pierre/Bullet hell roguelike - UML|UML]] §3.3

- [ ] `ShotModifiers` (record)
- [ ] `BulletPattern` : `RadialBurst`, `Spiral` et `AimedShot`
- [ ] `MovementPattern` : `LinearMove` et `SineMove`
- [ ] `Enemy` et `Boss` qui utilisent leurs patterns dans `update()`
- [ ] `BossPhase` : `PatternPhase`, `TransitionPhase` et `DyingPhase`, avec le passage d'une phase à l'autre selon les PV
- [ ] `Weapon` du joueur, qui réutilise un `BulletPattern`
- [ ] Tests : un pattern tire toujours les mêmes balles au même instant (déterministe) ; les `ShotModifiers` changent bien la vitesse et les dégâts ; le boss change de phase aux bons seuils de PV

> [!info] Pierre attend cette tâche
> Sa tâche [[2D-Game/taches/pierre/Factories|Factories]] (`EnemyFactory`) a besoin des patterns.
