---
title: 05 - Implémentation du jeu
tags: [2d-game, dev]
equipe: ["[[Pierre]]", "[[Chris]]"]
---

Coder un POC jouable qui suit le GDD et l'UML. Le code part de [[03 - Bootstrap LibGDX]], et chaque tâche suit l'[[2D-Game/ideas/pierre/Bullet hell roguelike - UML|UML du bullet hell]] (les numéros de section y renvoient).

Retour : [[2D-Game/index|2D Game]]

## Répartition

| Qui         | Domaines                                    | Paquets                               |
| ----------- | ------------------------------------------- | ------------------------------------- |
| [[Pierre]]  | Vaisseau modulaire, factories, niveaux, méta-progression | `ship`, `run`, `meta` |
| [[Chris]]   | Patterns, collisions, écrans                | `entity` (patterns, boss), `core`, `view` |

Chaque tâche de code inclut ses tests JUnit (voir [[06 - Qualité (tests, CI, Javadoc)]]).

## 0. Socle commun (à deux, en premier)

Tout le reste en dépend. À faire ensemble, en une séance, avant de se répartir le travail.

- [ ] Créer l'arborescence des paquets `core`, `view`, `run`, `meta`, `ship` et `entity` dans `com.mygame` (UML §2)
- [ ] Coder `Entity` (abstraite), `Damageable`, `Ship` (abstraite), `Bullet`, `Faction` et `BulletPool` (UML §3.2)
- [ ] Écrire les **interfaces** dont l'autre a besoin, sans les implémenter : `BulletPattern`, `MovementPattern`, `BossPhase`, `GameState`/`BaseScreen`, `RunListener`, `RunEvent`
- [ ] Ajouter JUnit 5 au `pom.xml`

## 1. Pierre

### Factories (UML §3.4, §3.5)

- [ ] `PartType`, `ShipBaseId` et le record `Stats` avec `plus()`
- [ ] `PartCatalog.create(PartType)` : crée la bonne pièce pour chaque type
- [ ] `ShipBase` (abstraite), `StandardBase` et `DashBase` avec `applyPassive()`
- [ ] `ShipFactory.create(ShipBaseId)` : renvoie un `PlayerShip` monté sur la bonne base
- [ ] `EnemyFactory.create(EnemyType, pos, DifficultyScaler)` : ennemi avec ses patterns et ses `ShotModifiers` *(attend les patterns de Chris)*
- [ ] Tests : chaque type crée la bonne classe, les stats de base sont correctes, la difficulté est bien appliquée

### Vaisseau modulaire (UML §3.4)

- [ ] `ShipComponent` (interface) et `ShipPart` (abstraite) avec `canMergeWith()` et `mergeWith()`
- [ ] `DashBooster` et `ShieldGenerator` (`absorb()`)
- [ ] `ShipAssembly` (Composite) : `canPlace()`, `place()`, `merge()`, `remove()`, stats = somme des enfants
- [ ] `PlayerShip` : déplacement, `dash()`, `takeDamage()` qui passe d'abord par `assembly.absorb()`
- [ ] Publier `PartEquipped` quand une pièce est placée ou fusionnée
- [ ] Tests : les stats s'additionnent ; deux pièces identiques fusionnent sur une seule case ; deux pièces différentes ne fusionnent pas ; on ne peut pas placer sur une case occupée ; un bouclier absorbe les dégâts avant les PV

### Niveaux (UML §3.5)

- [ ] Records `LevelDefinition`, `SpawnEvent`, et l'enum `LevelType`
- [ ] Format de fichier des niveaux (JSON) et `LevelLoader.loadAll(FileHandle)`
- [ ] `LevelPool.draw(n)` : boss tous les 10 niveaux, mini-boss tous les 5, sinon un niveau normal tiré sans remise, puis remélangé
- [ ] `Level` : fait apparaître les ennemis selon la timeline, `isCleared()`
- [ ] `DifficultyScaler` : vitesse et dégâts des balles, PV des boss selon le niveau
- [ ] `RewardGenerator.offer(count)` : tire des pièces parmi celles débloquées
- [ ] `Run` : graine, `nextLevel()`, `onLevelCleared()`, `onPlayerDeath()`
- [ ] Créer 3 à 5 niveaux à la main pour la démo
- [ ] Tests : avec la même graine, on obtient les mêmes niveaux ; les boss et mini-boss tombent aux bons numéros ; la difficulté augmente

### Méta-progression (UML §3.6)

- [ ] `RunEventBus` : `subscribe`, `unsubscribe`, `publish`
- [ ] Records `PartEquipped`, `EnemyKilled`, `LevelCleared` et `PlayerDied`
- [ ] `Challenge` (abstraite), `EquipPartsChallenge` et `ReachLevelChallenge`
- [ ] `PlayerProfile` : points, déblocages, `claimRewards()`
- [ ] `LootBox.open()` : dépense les points et débloque une arme encore verrouillée
- [ ] `ProfileRepository`, `JsonProfileRepository` (`Gdx.files.local()`) et `InMemoryProfileRepository`
- [ ] Tests : 5 boosters de dash équipés débloquent `DashBase` ; une loot box refuse l'achat sans assez de points ; le profil sauvegardé puis rechargé est identique

## 2. Chris

### Patterns (UML §3.3)

- [ ] `ShotModifiers` (record)
- [ ] `BulletPattern` : `RadialBurst`, `Spiral` et `AimedShot`
- [ ] `MovementPattern` : `LinearMove` et `SineMove`
- [ ] `Enemy` et `Boss` qui utilisent leurs patterns dans `update()`
- [ ] `BossPhase` : `PatternPhase`, `TransitionPhase` et `DyingPhase`, avec le passage d'une phase à l'autre selon les PV
- [ ] `Weapon` du joueur, qui réutilise un `BulletPattern`
- [ ] Tests : un pattern tire toujours les mêmes balles au même instant (déterministe) ; les `ShotModifiers` changent bien la vitesse et les dégâts ; le boss change de phase aux bons seuils de PV

### Collisions (UML §3.2)

- [ ] `Hitbox` avec des `Rectangle` LibGDX et `intersects()`
- [ ] `CollisionSystem.resolve()` : balles du joueur contre ennemis, balles ennemies contre joueur, contact joueur-ennemi
- [ ] Les balles qui touchent ou sortent de l'écran sont rendues au `BulletPool`
- [ ] Publier `EnemyKilled` sur le `RunEventBus` quand un ennemi meurt *(attend le bus de Pierre)*
- [ ] Hitbox du vaisseau = union des cases occupées par les pièces (`ShipAssembly.computeHitbox()`) *(attend le vaisseau modulaire de Pierre)*
- [ ] Tests : une balle sur la hitbox fait des dégâts, une balle à côté non ; une pièce ajoutée agrandit la hitbox ; une fusion ne l'agrandit pas

### Écrans (UML §3.1, §5.1)

- [ ] Renommer `MyFirstLibGDXGame` en `BulletHellGame`, et `GameScreen` en `PlayingScreen`
- [ ] `BaseScreen` : `render()` appelle `update()` puis `draw()`
- [ ] `InputHandler` (étend `InputAdapter`) et l'enum `Action`
- [ ] `Renderer` : `SpriteBatch`, atlas de textures, HUD (PV, niveau, dashs, score)
- [ ] Écrans : `MainMenuScreen`, `ShipSelectScreen`, `PlayingScreen`, `PauseScreen`, `RewardScreen`, `EditorScreen`, `GameOverScreen` et `ShopScreen`
- [ ] Transitions entre écrans comme dans le diagramme d'états (UML §5.1)
- [ ] Tests : `InputHandler` associe bien les touches aux actions ; `wasPressed()` n'est vrai qu'une frame

## 3. Fin (à deux)

- [ ] Brancher le tout dans `PlayingScreen` et jouer une run complète
- [ ] Vérifier que le code reste fidèle à l'UML (mettre les diagrammes à jour si besoin)
