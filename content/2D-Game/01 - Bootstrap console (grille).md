---
title: 01 - Bootstrap console (grille)
tags: [2d-game, bootstrap]
---

Faire un jeu simple sur une grille 2D fixe. Le joueur se déplace d'une case par tour (haut, bas, gauche, droite). Dans une case, il combat les ennemis un par un. Quand il n'y a plus d'ennemi, il ramasse les objets. Ce bootstrap peut servir de première brique pour le projet.

Retour : [[2D-Game/index|2D Game]]

## Tâches

- [ ] Partir du template de la classe `Game`
- [ ] Coder la grille et les cases (une case contient des ennemis et des objets)
- [ ] Gérer le déplacement du joueur au clavier, un tour à la fois
- [ ] Terminer la boucle de jeu
- [ ] Gérer le combat contre les ennemis de la case, l'un après l'autre
- [ ] Ramasser les objets de la case quand il ne reste plus d'ennemi
- [ ] Créer au moins **3 types d'ennemis**, chacun avec sa stratégie d'attaque (dégâts, vol d'un objet du sac…)
- [ ] Créer au moins **3 types d'objets**, chacun avec son effet
- [ ] Définir la condition de victoire (tuer tous les ennemis, trouver un objet précis, atteindre une case…)
- [ ] Définir les conditions de défaite (plus de vie, piège, temps écoulé…)
- [ ] Afficher l'état du jeu dans la console à chaque tour
- [ ] Écrire un `main()` qui crée et configure une partie
- [ ] Utiliser le polymorphisme et éviter la duplication (classes abstraites, interfaces, pattern Strategy pour les attaques)
- [ ] Documenter chaque classe et les méthodes importantes (Javadoc)
- [ ] Écrire un README qui explique le jeu

## Pour aller plus loin (facultatif)

- [ ] Ajouter des obstacles (murs, portes, échelles) et des niveaux générés de façon procédurale
- [ ] Ajouter des interactions (dialogues, inventaire)
- [ ] Faire bouger les monstres, avec du pathfinding
- [ ] Rendre le système de combat plus riche
