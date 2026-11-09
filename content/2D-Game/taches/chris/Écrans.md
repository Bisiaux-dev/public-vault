---
title: Écrans
tags: [2d-game, dev, taches]
equipe: ["[[Chris]]"]
---

Le moteur LibGDX, les entrées, le rendu et les écrans du jeu (pattern State).

Retour : [[2D-Game/taches/chris/index|Tâches de Chris]] · [[2D-Game/ideas/pierre/Bullet hell roguelike - UML|UML]] §3.1, §5.1

- [ ] Renommer `MyFirstLibGDXGame` en `BulletHellGame`, et `GameScreen` en `PlayingScreen`
- [ ] Chaque écran étend `BaseScreen` (codée dans le [[2D-Game/taches/commun/Socle|socle]]) et redéfinit `update()` et `draw()`
- [ ] `InputHandler` (étend `InputAdapter`) et l'enum `Action`
- [ ] `Renderer` : `SpriteBatch`, atlas de textures, HUD (PV, niveau, dashs, score)
- [ ] Écrans : `MainMenuScreen`, `ShipSelectScreen`, `PlayingScreen`, `PauseScreen`, `RewardScreen`, `EditorScreen`, `GameOverScreen` et `ShopScreen`
- [ ] Transitions entre écrans comme dans le diagramme d'états (UML §5.1)
- [ ] Tests : `InputHandler` associe bien les touches aux actions ; `wasPressed()` n'est vrai qu'une frame
