---
title: Socle commun
tags: [2d-game, dev, taches]
equipe: ["[[Pierre]]", "[[Chris]]"]
---

Tout le reste en dépend. À faire ensemble, en une séance, avant de se répartir le travail.

Retour : [[2D-Game/taches/commun/index|Tâches communes]] · [[2D-Game/ideas/pierre/Bullet hell roguelike - UML|UML]] §2, §3.2

- [ ] Créer les paquets `core`, `screen`, `view`, `run`, `meta`, `ship` et `entity` dans `com.mygame`
- [x] Coder `Entity` (abstraite), `Damageable`, `Ship` (abstraite), `Bullet`, `Faction` et `BulletPool`
- [x] Écrire les **interfaces** dont l'autre a besoin, sans les implémenter : `BulletPattern`, `MovementPattern`, `BossPhase`, `RunListener`, `RunEvent` (sealed)
- [x] Coder `BaseScreen` dans `screen` (**classe abstraite**, étend `ScreenAdapter`) : `render()` appelle `update()` puis `draw()`, qui restent abstraites. `Screen` est l'interface fournie par LibGDX, on ne l'écrit pas
- [x] Ajouter JUnit 5 au module `core` (`core/build.gradle`)
