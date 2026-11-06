---
title: 04 - Conception (GDD & UML)
tags: [2d-game, conception, uml]
equipe: ["[[Pierre]]", "[[Chris]]"]
---

On n'écrit pas de code avant d'avoir terminé le Game Design Document.

Retour : [[2D-Game/index|2D Game]]

## Choix du jeu

- [x] Choisir le genre : **bullet hell roguelike** ([[2D-Game/ideas/pierre/Bullet hell roguelike|idée retenue]])
- [x] Vérifier que le genre se prête à la POO : beaucoup d'entités avec des traits communs et des comportements différents
- [ ] **Faire valider le choix par le pédago**
- [x] Choisir la bibliothèque graphique : **LibGDX**

## Game Design Document

- [ ] Décrire la vue et la perspective (2D de côté, 2D de dessus…)
- [ ] Décrire les interactions avec l'environnement et les personnages
- [ ] Décrire les personnages : comportement, inventaire, progression
- [ ] Décrire la gestion des entrées, du temps (tour par tour ou temps réel) et des niveaux ou du monde
- [ ] Décrire l'IA et le pathfinding, s'il y en a
- [ ] Décrire les règles de combat et l'algorithme de résolution
- [ ] Définir les conditions de victoire et de défaite
- [ ] Décrire l'interface (affichage du jeu et des infos du personnage)
- [ ] Répondre aux questions propres au genre choisi (voir l'annexe « Inspirations » du sujet)

## UML

- [x] Diagramme de classes qui fait apparaître les design patterns ([[2D-Game/ideas/pierre/Bullet hell roguelike - UML|UML]] §3)
- [x] Diagramme de cas d'utilisation (§1)
- [x] Diagramme de séquence (boucle de jeu, combat…) (§4)
- [x] Diagramme d'états (états des entités ou du jeu) (§5)
- [ ] Diagramme d'activité, de composants ou de déploiement, si c'est utile
- [x] Lister et justifier les patterns choisis (Strategy, State, Factory, Observer, Command…) (§6)
