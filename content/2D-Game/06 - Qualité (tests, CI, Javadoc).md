---
title: 06 - Qualité (tests, CI, Javadoc)
tags: [2d-game, tests, ci]
---

Retour : [[2D-Game/index|2D Game]]

## Tests

- [ ] Passer de JUnit 4.11 à JUnit 5 dans le `pom.xml`
- [ ] Tests unitaires du combat
- [ ] Tests unitaires des effets des objets
- [ ] Tests unitaires des conditions de victoire et de défaite
- [ ] Tests unitaires des comportements des ennemis et des déplacements
- [ ] Supprimer le test d'exemple `AppTest`

## Automatisation

- [ ] Lancer les tests automatiquement (script ou pipeline GitHub Actions avec `mvn test` à chaque push)
- [ ] Mesurer la couverture (JaCoCo) et l'afficher dans la CI
- [ ] Fixer un seuil minimal de couverture

## Documentation

- [ ] Écrire la Javadoc de toutes les classes et méthodes importantes
- [ ] Générer la Javadoc (`mvn javadoc:javadoc`)
- [ ] Écrire un README complet : présentation, installation, lancement, contrôles, architecture
