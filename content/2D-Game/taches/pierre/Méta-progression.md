---
title: Méta-progression
tags: [2d-game, dev, taches]
equipe: ["[[Pierre]]"]
---

> [!warning] Mise de côté le 2026-11-24
> Le jeu est réduit à un boss rush pour finir dans les temps. Cette tâche ne se fait pas pour l'instant. Son UML est dans [[2D-Game/ideas/pierre/Bullet hell roguelike - UML (mis de côté)|UML (mis de côté)]].

Ce qu'on garde après la mort : points, défis, loot box (pattern Observer).

Retour : [[2D-Game/taches/pierre/index|Tâches de Pierre]] · [[2D-Game/ideas/pierre/Bullet hell roguelike - UML (mis de côté)|UML (mis de côté)]]

- [ ] `RunEventBus` : `subscribe`, `unsubscribe`, `publish`
- [ ] Records `PartEquipped`, `EnemyKilled`, `LevelCleared` et `PlayerDied`
- [ ] `Challenge` (abstraite), `EquipPartsChallenge` et `ReachLevelChallenge`
- [ ] `PlayerProfile` : points, déblocages, `claimRewards()`
- [ ] `LootBox.open()` : dépense les points et débloque une arme encore verrouillée
- [ ] `ProfileRepository`, `JsonProfileRepository` (`Gdx.files.local()`) et `InMemoryProfileRepository`
- [ ] Tests : 5 boosters de dash équipés débloquent `DashBase` ; une loot box refuse l'achat sans assez de points ; le profil sauvegardé puis rechargé est identique


