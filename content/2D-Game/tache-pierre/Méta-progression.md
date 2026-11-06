---
title: Méta-progression
tags: [2d-game, dev, taches]
equipe: ["[[Pierre]]"]
---

Ce qu'on garde après la mort : points, défis, loot box (pattern Observer).

Retour : [[2D-Game/tache-pierre/index|Tâches de Pierre]] · [[2D-Game/ideas/pierre/Bullet hell roguelike - UML|UML]] §3.6

- [ ] `RunEventBus` : `subscribe`, `unsubscribe`, `publish`
- [ ] Records `PartEquipped`, `EnemyKilled`, `LevelCleared` et `PlayerDied`
- [ ] `Challenge` (abstraite), `EquipPartsChallenge` et `ReachLevelChallenge`
- [ ] `PlayerProfile` : points, déblocages, `claimRewards()`
- [ ] `LootBox.open()` : dépense les points et débloque une arme encore verrouillée
- [ ] `ProfileRepository`, `JsonProfileRepository` (`Gdx.files.local()`) et `InMemoryProfileRepository`
- [ ] Tests : 5 boosters de dash équipés débloquent `DashBase` ; une loot box refuse l'achat sans assez de points ; le profil sauvegardé puis rechargé est identique

> [!info] Chris attend cette tâche
> Sa tâche [[2D-Game/tache-chris/Collisions|Collisions]] publie `EnemyKilled` sur le `RunEventBus`.
