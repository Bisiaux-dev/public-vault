---
title: 05 - Implémentation du jeu
tags: [2d-game, dev]
equipe: ["[[Pierre]]", "[[Chris]]"]
---

Coder un POC jouable qui suit le GDD et l'UML. Le code part du bootstrap LibGDX (`BOOTRAP_LibGDX/MyFirstLibGDXGame`, Maven, LibGDX 1.13.1, Java 17, lancement : `mvn compile exec:java`), et chaque tâche suit l'[[2D-Game/ideas/pierre/Bullet hell roguelike - UML|UML du bullet hell]] (les numéros de section y renvoient).

Retour : [[2D-Game/index|2D Game]]

## Répartition

| Qui        | Domaines                                                  | Paquets                                   | Tâches                                     |
| ---------- | --------------------------------------------------------- | ----------------------------------------- | ------------------------------------------ |
| À deux     | Socle (en premier), intégration (à la fin)                | tous                                      | [[2D-Game/tache-commun/index\|tache-commun]] |
| [[Pierre]] | Vaisseau modulaire, factories, niveaux, méta-progression  | `ship`, `run`, `meta`                     | [[2D-Game/tache-pierre/index\|tache-pierre]] |
| [[Chris]]  | Patterns, collisions, écrans                              | `entity` (patterns, boss), `core`, `view` | [[2D-Game/tache-chris/index\|tache-chris]]   |

Chaque tâche de code inclut ses tests JUnit (voir [[06 - Qualité (tests, CI, Javadoc)]]).

## Ordre

1. [[2D-Game/tache-commun/Socle|Socle commun]], à deux.
2. Chacun ses tâches, en parallèle.
3. [[2D-Game/tache-commun/Intégration|Intégration]], à deux.

## Dépendances entre Pierre et Chris

| Tâche qui attend                                              | Ce qu'elle attend                                                        |
| ------------------------------------------------------------- | ------------------------------------------------------------------------ |
| [[2D-Game/tache-pierre/Factories\|Factories]] (Pierre)        | les patterns ([[2D-Game/tache-chris/Patterns\|Patterns]], Chris)          |
| [[2D-Game/tache-chris/Collisions\|Collisions]] (Chris)        | `RunEventBus` ([[2D-Game/tache-pierre/Méta-progression\|Méta-progression]], Pierre) |
| [[2D-Game/tache-chris/Collisions\|Collisions]] (Chris)        | `ShipAssembly` ([[2D-Game/tache-pierre/Vaisseau modulaire\|Vaisseau modulaire]], Pierre) |

Les interfaces écrites pendant le socle permettent de commencer sans attendre : on code contre l'interface, l'implémentation arrive plus tard.
