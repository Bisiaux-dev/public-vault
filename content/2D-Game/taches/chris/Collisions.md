---
title: Collisions
tags: [2d-game, dev, taches]
equipe: ["[[Chris]]"]
---

Qui touche qui, et ce qui se passe quand ça touche.

Retour : [[2D-Game/taches/chris/index|Tâches de Chris]] · [[2D-Game/ideas/pierre/Bullet hell roguelike - UML|UML]] §3.2

- [ ] `Hitbox` avec des `Rectangle` LibGDX et `intersects()`
- [ ] `CollisionSystem.resolve()` : balles du joueur contre ennemis, balles ennemies contre joueur, contact joueur-ennemi
- [ ] Les balles qui touchent ou sortent de l'écran sont rendues au `BulletPool`
- [ ] Publier `EnemyKilled` sur le `RunEventBus` quand un ennemi meurt *(attend [[2D-Game/taches/pierre/Méta-progression|le bus de Pierre]])*
- [ ] Hitbox du vaisseau = union des cases occupées par les pièces (`ShipAssembly.computeHitbox()`) *(attend [[2D-Game/taches/pierre/Vaisseau modulaire|le vaisseau modulaire de Pierre]])*
- [ ] Tests : une balle sur la hitbox fait des dégâts, une balle à côté non ; une pièce ajoutée agrandit la hitbox ; une fusion ne l'agrandit pas
