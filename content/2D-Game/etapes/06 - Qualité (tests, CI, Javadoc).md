---
title: 06 - Qualité (tests, CI, Javadoc)
tags: [2d-game, tests, ci]
equipe: ["[[Pierre]]", "[[Chris]]"]
---

Retour : [[2D-Game/index|2D Game]]

## Tests

- [ ] Ajouter JUnit 5 au module `core` (`core/build.gradle`)
- [ ] Tests unitaires du combat
- [ ] Tests unitaires des effets des objets
- [ ] Tests unitaires des conditions de victoire et de défaite
- [ ] Tests unitaires des comportements des ennemis et des déplacements

## Automatisation

- [ ] Lancer les tests automatiquement (script ou pipeline GitHub Actions avec `./gradlew test` à chaque push)
- [ ] Mesurer la couverture (plugin Gradle `jacoco`) et l'afficher dans la CI
- [ ] Fixer un seuil minimal de couverture

## Documentation

- [ ] Écrire la Javadoc de toutes les classes et méthodes importantes
- [ ] Générer la Javadoc (`./gradlew javadoc`)
- [ ] Écrire un README complet : présentation, installation, lancement, contrôles, architecture
