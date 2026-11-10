---
title: Vaisseau modulaire
tags: [2d-game, dev, taches]
equipe: ["[[Pierre]]"]
---

Le cœur du jeu (pattern Composite) : le vaisseau est un assemblage de pièces.

Retour : [[2D-Game/taches/pierre/index|Tâches de Pierre]] · [[2D-Game/ideas/pierre/Bullet hell roguelike - UML|UML]] §3.4

- [ ] `GridPos` (record `col`, `row`) : une case de la grille. `Stats` et `PartType` viennent de [[2D-Game/taches/pierre/Factories|Factories]], à coder avant
- [ ] `ShipComponent` (interface) et `ShipPart` (abstraite) avec `canMergeWith()` et `mergeWith()`
- [ ] `DashBooster` et `ShieldGenerator` (`absorb()`)
- [ ] `ShipAssembly` (Composite) : `canPlace()`, `place()`, `merge()`, `remove()`, stats = somme des enfants
- [ ] `PlayerShip` : déplacement, `dash()`, `takeDamage()` qui passe d'abord par `assembly.absorb()`
- [ ] Publier `PartEquipped` quand une pièce est placée ou fusionnée
- [ ] Tests : les stats s'additionnent ; deux pièces identiques fusionnent sur une seule case ; deux pièces différentes ne fusionnent pas ; on ne peut pas placer sur une case occupée ; un bouclier absorbe les dégâts avant les PV

> [!info] Chris attend cette tâche
> Sa tâche [[2D-Game/taches/chris/Collisions|Collisions]] a besoin de `ShipAssembly.computeHitbox()`.
