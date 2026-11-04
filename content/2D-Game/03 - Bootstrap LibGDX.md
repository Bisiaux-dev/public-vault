---
title: 03 - Bootstrap LibGDX
tags: [2d-game, bootstrap, libgdx]
---

Bootstrap alternatif, à faire seulement si tu choisis LibGDX plutôt que JavaFX. Le dossier `BOOTRAP_LibGDX` est vide pour l'instant.

Retour : [[2D-Game/index|2D Game]]

- [ ] **Ex 1 : initialisation.** Avec le setup tool, créer `MyFirstLibGDXGame` (package `com.mygame`, desktop uniquement), l'importer dans l'IDE et vérifier qu'une fenêtre noire s'affiche
- [ ] **Ex 2 : structure.** Créer une classe `GameScreen` qui gère la logique, la classe principale lui délègue l'affichage (héritage ?)
- [ ] **Ex 3 : sprite.** Ajouter un PNG dans `assets` et l'afficher au centre (bien distinguer `create`, `render` et `dispose`)
- [ ] **Ex 4 : joueur.** Créer une classe `Player` qui se déplace avec les flèches, intégrée à `GameScreen`
- [ ] **Ex 5 : collisions.** Ajouter un obstacle fixe et une méthode de collision dans `Player` (position + taille), avec un message dans la console
- [ ] **Ex 6 : ennemi.** Créer une classe `Enemy` qui se déplace horizontalement et rebondit sur les bords (`update`)
- [ ] **Ex 7 : score.** Créer une classe `Score` qui augmente avec le temps, affichée en haut à gauche
- [ ] **Ex 8 (bonus) : architecture.** Ranger le code dans des dossiers `entities/` (Player, Enemy) et `managers/` (collisions, score)
