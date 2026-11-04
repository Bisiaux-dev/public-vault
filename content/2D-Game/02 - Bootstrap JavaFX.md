---
title: 02 - Bootstrap JavaFX
tags: [2d-game, bootstrap, javafx]
---

Prise en main de JavaFX : afficher une fenêtre, un personnage, et le déplacer au clavier dans une boucle de jeu à 60 FPS.
Code : `bootrap_JavaFx/jeux_bootrap_java_fx` (Maven, JavaFX 21).

Retour : [[2D-Game/index|2D Game]]

## Mise en place

- [x] Installer l'Extension Pack for Java (VS Code) et créer le projet JavaFX
- [x] Vérifier que `JAVA_HOME` est défini
- [x] Ne pas utiliser les contrôleurs FXML : `App` contient un `Scene` privé et un `Pane`
- [x] Une fenêtre vide s'ouvre au lancement

## Entity et personnage

- [x] Créer la classe `Entity` avec une position, un `ImageView sprite` et un constructeur
- [ ] Ajouter l'attribut `Image spriteSheet`
- [ ] Ajouter les getters de position
- [ ] Rendre `Entity` **abstraite**, avec une méthode `update()` abstraite appelée à chaque frame
- [ ] Créer une classe de personnage (par ex. `Player`) qui hérite de `Entity`, avec un attribut `speed` et une redéfinition de `update()`
- [ ] Le constructeur appelle `super(...)` avec le chemin du fichier et la position (`setTranslateX` / `setTranslateY`)
- [x] Instancier le personnage dans `App` et l'ajouter à la scène

## Déplacement

- [ ] Créer une classe `EventHandler` instanciée dans `App`, qui reçoit la scène en paramètre
- [ ] Garder en mémoire les touches ZQSD enfoncées (appui et relâchement)
- [ ] Transmettre l'état des touches au personnage dans `update()`
- [ ] Créer la boucle de jeu à 60 mises à jour par seconde (`AnimationTimer` / `Timeline`)
- [ ] Faire bouger le personnage dans `update()` selon `speed`

> Pour l'instant, le déplacement est codé directement dans `App` avec `setOnKeyPressed` (1 px par appui). Il faut le déplacer dans `EventHandler` et `update()`.

## Nettoyage

- [ ] Supprimer les doublons `stage.setScene` / `stage.show` dans `App.start()`
- [ ] Corriger les commentaires « 10 pixels » (le pas réel est de 1 px)

## Pour aller plus loin

- [ ] Ajouter des ennemis
- [ ] Ajouter un système de combat
- [ ] Ajouter des objets à ramasser
- [ ] Ajouter un score
- [ ] Ajouter une interface propre avec un menu principal
