---
title: Socle commun
tags: [2d-game, dev, taches]
equipe: ["[[Pierre]]", "[[Chris]]"]
---

Tout le reste en dépend. À faire ensemble, en une séance, avant de se répartir le travail.

Retour : [[2D-Game/taches/commun/index|Tâches communes]] · [[2D-Game/ideas/pierre/Bullet hell roguelike - UML|UML]] §2, §3.2

- [x] Créer les paquets `core`, `view`, `run`, `meta`, `ship` et `entity` dans `com.mygame`
- [ ] Coder `Entity` (abstraite), `Damageable`, `Ship` (abstraite), `Bullet`, `Faction` et `BulletPool`
- [ ] Écrire les **interfaces** dont l'autre a besoin, sans les implémenter : `BulletPattern`, `MovementPattern`, `BossPhase`, `RunListener`, `RunEvent` (sealed)
- [ ] Coder `BaseScreen` (**classe abstraite**, étend `ScreenAdapter`) : `render()` appelle `update()` puis `draw()`, qui restent abstraites. `Screen` est l'interface fournie par LibGDX, on ne l'écrit pas
- [ ] Ajouter JUnit 5 au `pom.xml`
