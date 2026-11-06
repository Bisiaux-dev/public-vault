---
title: 03 - Bootstrap LibGDX
tags: [2d-game, bootstrap, libgdx]
equipe: ["[[Pierre]]", "[[Chris]]"]
---

**Bibliothèque retenue pour le projet** (à la place de JavaFX). Prise en main de LibGDX : fenêtre, écran, sprite, joueur, collisions, ennemi, score.
Code : `BOOTRAP_LibGDX/MyFirstLibGDXGame` (Maven, LibGDX 1.13.1, Java 17, package `com.mygame`). Lancement : `mvn compile exec:java`.

Retour : [[2D-Game/index|2D Game]] · Conception : [[2D-Game/ideas/pierre/Bullet hell roguelike - UML|UML du bullet hell]]

## Exercices

- [x] **Ex 1 : initialisation.** Projet Maven `MyFirstLibGDXGame` créé (desktop uniquement, backend LWJGL3). `DesktopLauncher` ouvre une fenêtre noire de 1280×720 à 60 FPS
- [x] **Ex 2 : structure.** `MyFirstLibGDXGame` hérite de `Game` et délègue l'affichage à `GameScreen` (qui hérite de `ScreenAdapter`) avec `setScreen()`
- [ ] **Ex 3 : sprite.** Ajouter un PNG dans `assets` et l'afficher au centre (bien distinguer `create`, `render` et `dispose`) *(en cours)*
- [ ] **Ex 4 : joueur.** Créer une classe `Player` qui se déplace avec les flèches, intégrée à `GameScreen`
- [ ] **Ex 5 : collisions.** Ajouter un obstacle fixe et une méthode de collision dans `Player` (position + taille), avec un message dans la console
- [ ] **Ex 6 : ennemi.** Créer une classe `Enemy` qui se déplace horizontalement et rebondit sur les bords (`update`)
- [ ] **Ex 7 : score.** Créer une classe `Score` qui augmente avec le temps, affichée en haut à gauche
- [ ] **Ex 8 (bonus) : architecture.** Ranger le code dans des dossiers `entities/` (Player, Enemy) et `managers/` (collisions, score)

## Ex 3 : ce qui reste

`assets/joueur.png` est en place, et `GameScreen.show()` crée le `SpriteBatch` et charge la `Texture`. Il manque :

- [ ] Ajouter `import com.badlogic.gdx.graphics.g2d.SpriteBatch;` dans `GameScreen` : sans lui, le projet ne compile plus (la dernière compilation date d'avant l'ajout du `SpriteBatch`)
- [ ] Dessiner le sprite au centre dans `render()` : `batch.begin()`, `batch.draw(hero, x, y)`, `batch.end()`, avec `x = (Gdx.graphics.getWidth() - hero.getWidth()) / 2` (pareil pour `y`)
- [ ] Redéfinir `dispose()` pour libérer `batch` et `hero`

## Lien avec l'UML

Le bootstrap prépare directement les classes du projet :

- `MyFirstLibGDXGame` deviendra `BulletHellGame`, et `GameScreen` deviendra `PlayingScreen`.
- `Player` (Ex 4) préfigure `PlayerShip` ; `Enemy` (Ex 6) garde son nom.
- La collision de l'Ex 5 prendra la forme de `CollisionSystem`, avec des `Rectangle` de LibGDX.
- Le rangement de l'Ex 8 correspond aux paquets `entity`, `ship`, `run`… de l'UML.
