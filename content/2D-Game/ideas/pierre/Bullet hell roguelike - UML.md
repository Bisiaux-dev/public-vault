---
title: Bullet hell roguelike — UML
tags: [2d-game, uml, conception, shmup]
auteur: "[[Pierre]]"
statut: brouillon
---

UML de l'idée [[2D-Game/ideas/pierre/Bullet hell roguelike|Bullet hell roguelike — vaisseau modulaire]]. Ça couvre la partie UML de [[04 - Conception (GDD & UML)]].

Cible : Java 17 + LibGDX 1.13 (même base que `BOOTRAP_LibGDX`, package `com.mygame`). `MyFirstLibGDXGame` devient `BulletHellGame` et `GameScreen` devient `PlayingScreen`.

**Choix de départ : le modèle n'utilise que les classes de LibGDX qui marchent sans fenêtre** (`Vector2`, `Rectangle`, `Pool`). Les entités ne portent pas de `Texture`, seulement un `spriteId`. Les `Texture` et le `SpriteBatch` demandent un contexte OpenGL, donc ils restent dans `Renderer` et les écrans. On peut ainsi tester avec JUnit les collisions, la fusion, la difficulté et le tirage des niveaux sans ouvrir de fenêtre.

## 1. Cas d'utilisation

```mermaid
flowchart LR
    J([Joueur])
    subgraph Jeu
        UC1([Choisir un vaisseau de base])
        UC2([Jouer un niveau])
        UC3([Bouger, tirer, dasher])
        UC4([Accepter ou refuser une pièce])
        UC5([Placer une pièce dans l'éditeur])
        UC6([Fusionner deux pièces identiques])
        UC7([Acheter une loot box])
        UC8([Voir ses déblocages])
    end
    J --- UC1 & UC2 & UC4 & UC5 & UC7 & UC8
    UC2 -. «include» .-> UC3
    UC6 -. «extend» .-> UC5
```

## 2. Paquets

Les flèches indiquent qui dépend de qui. Il n'y a pas de cycle : `entity` ne connaît personne, et `PlayerShip` est rangé dans `ship` pour que `entity` ne dépende pas du vaisseau modulaire.

```mermaid
flowchart LR
    core["core<br/>DesktopLauncher, BulletHellGame, écrans, InputHandler"]
    view["view<br/>Renderer"]
    run["run<br/>Run, Level, LevelPool, RewardGenerator, DifficultyScaler"]
    meta["meta<br/>PlayerProfile, Challenge, LootBox, RunEventBus"]
    ship["ship<br/>PlayerShip, ShipAssembly, ShipPart, ShipBase"]
    entity["entity<br/>Entity, Ship, Enemy, Boss, Bullet, patterns"]
    core --> run & meta & view & ship
    view --> entity & run
    run --> meta & ship & entity
    meta --> ship
    ship --> entity
```

## 3. Diagrammes de classes

### 3.1 Moteur et écrans — State, Template Method

LibGDX fournit déjà le pattern State : `Game` garde un `Screen` courant et `setScreen()` en change. Chaque écran du jeu est un état.

```mermaid
classDiagram
    direction TB
    class DesktopLauncher {
        +main(String[] args)$ void
    }
    class Game {
        <<abstract, LibGDX>>
        +setScreen(Screen s) void
        +render() void
    }
    class Screen {
        <<interface, LibGDX>>
        +show() void
        +render(float delta) void
        +hide() void
        +dispose() void
    }
    class ScreenAdapter {
        <<LibGDX>>
    }
    class BulletHellGame {
        -SpriteBatch batch
        -AssetManager assets
        -InputHandler input
        -PlayerProfile profile
        -Run run
        +create() void
        +dispose() void
    }
    class BaseScreen {
        <<abstract>>
        #BulletHellGame game
        +render(float delta) void
        #update(float delta)* void
        #draw(Renderer r)* void
    }
    class MainMenuScreen
    class ShipSelectScreen
    class PlayingScreen {
        -Level level
    }
    class PauseScreen
    class RewardScreen {
        -List~ShipPart~ offer
    }
    class EditorScreen {
        -ShipPart pending
    }
    class GameOverScreen
    class ShopScreen
    class InputHandler {
        -IntSet down
        -IntSet justPressed
        +keyDown(int keycode) boolean
        +keyUp(int keycode) boolean
        +isDown(Action a) boolean
        +wasPressed(Action a) boolean
        +endFrame() void
    }
    class InputAdapter {
        <<LibGDX>>
    }
    class Action {
        <<enumeration>>
        UP
        DOWN
        LEFT
        RIGHT
        SHOOT
        DASH
        CONFIRM
        PAUSE
    }
    class Renderer {
        -SpriteBatch batch
        -TextureAtlas atlas
        -BitmapFont font
        +draw(Entity e) void
        +drawHud(Run run) void
    }

    DesktopLauncher ..> BulletHellGame : lance
    Game <|-- BulletHellGame
    Game o-- Screen : écran courant
    Screen <|.. ScreenAdapter
    ScreenAdapter <|-- BaseScreen
    BulletHellGame *-- InputHandler
    BulletHellGame *-- Renderer
    InputAdapter <|-- InputHandler
    InputHandler ..> Action
    BaseScreen <|-- MainMenuScreen
    BaseScreen <|-- ShipSelectScreen
    BaseScreen <|-- PlayingScreen
    BaseScreen <|-- PauseScreen
    BaseScreen <|-- RewardScreen
    BaseScreen <|-- EditorScreen
    BaseScreen <|-- GameOverScreen
    BaseScreen <|-- ShopScreen
    note for BaseScreen "Template Method : render() appelle update() puis draw()"
```

- `BaseScreen.render(delta)` est le même pour tous les écrans : il appelle `update(delta)`, efface l'écran, puis `draw(renderer)`. Chaque écran ne redéfinit que ces deux méthodes. On sépare ainsi la logique (testable) du dessin.
- `InputHandler` étend `InputAdapter` et est branché avec `Gdx.input.setInputProcessor()`. Il retient les touches enfoncées pour que `PlayerShip` les lise dans `update()`. Les touches sont associées à une `Action`, donc on peut changer les contrôles sans toucher au vaisseau.

### 3.2 Entités et collisions

```mermaid
classDiagram
    direction TB
    class Entity {
        <<abstract>>
        #Vector2 position
        #Vector2 velocity
        #String spriteId
        #boolean alive
        +update(float delta)* void
        +getHitbox()* Hitbox
        +isAlive() boolean
        +destroy() void
    }
    class Damageable {
        <<interface>>
        +takeDamage(int amount) void
        +getHp() int
    }
    class Ship {
        <<abstract>>
        #int hp
        #int maxHp
        #float speed
        +takeDamage(int amount) void
        +getHp() int
    }
    class PlayerShip {
        voir 3.4
    }
    class Enemy {
        -EnemyType type
        -BulletPattern pattern
        -MovementPattern movement
        -ShotModifiers shotMods
        -int scoreValue
        +update(float delta) void
        +getHitbox() Hitbox
    }
    class Boss {
        -BossPhase phase
        +setPhase(BossPhase next) void
        +update(float delta) void
    }
    class Bullet {
        -int damage
        -Faction faction
        +init(Vector2 pos, Vector2 vel, int damage, Faction f) void
        +reset() void
        +update(float delta) void
        +getHitbox() Hitbox
    }
    class Faction {
        <<enumeration>>
        PLAYER
        ENEMY
    }
    class Pool~T~ {
        <<abstract, LibGDX>>
        +obtain() T
        +free(T object) void
        #newObject()* T
    }
    class Poolable {
        <<interface, LibGDX>>
        +reset() void
    }
    class BulletPool {
        -Array~Bullet~ active
        #newObject() Bullet
        +spawn(Vector2 pos, Vector2 vel, int damage, Faction f) Bullet
        +update(float delta) void
    }
    class Hitbox {
        -Array~Rectangle~ shapes
        +intersects(Hitbox other) boolean
    }

    Entity <|-- Ship
    Entity <|-- Bullet
    Damageable <|.. Ship
    Ship <|-- PlayerShip
    Ship <|-- Enemy
    Enemy <|-- Boss
    Entity ..> Hitbox
    Bullet --> Faction
    Poolable <|.. Bullet
    Pool <|-- BulletPool
    BulletPool o-- "*" Bullet
    note for BulletPool "Object Pool : on recycle les balles au lieu d'en créer des centaines par seconde"
```

`BulletPool` s'appuie sur `Pool` de LibGDX : `spawn()` fait `obtain()` puis `init()`, et une balle sortie de l'écran ou qui a touché est rendue avec `free()`, qui appelle `reset()`. `Vector2`, `Rectangle` et `Array` sont aussi ceux de LibGDX. Ils marchent sans fenêtre, donc dans les tests.

### 3.3 Comportements des ennemis — Strategy, State

Les patterns de tir sont fixes et déterministes (même entrée, mêmes balles), donc apprenables. La difficulté passe par `ShotModifiers` (vitesse et dégâts), pas par de nouvelles attaques.

```mermaid
classDiagram
    direction TB
    class Enemy
    class Boss
    class BulletPattern {
        <<interface>>
        +fire(Vector2 origin, float t, BulletPool pool, ShotModifiers mods) void
    }
    class RadialBurst {
        -int bulletCount
    }
    class Spiral {
        -float angularSpeed
    }
    class AimedShot {
        -Supplier~Vector2~ target
    }
    class MovementPattern {
        <<interface>>
        +positionAt(Vector2 start, float t) Vector2
    }
    class LinearMove
    class SineMove
    class ShotModifiers {
        <<record>>
        float speedMultiplier
        float damageMultiplier
    }
    class BossPhase {
        <<interface>>
        +enter(Boss boss) void
        +update(Boss boss, float delta) void
        +next(Boss boss) BossPhase
    }
    class PatternPhase {
        -List~BulletPattern~ patterns
        -float hpThreshold
    }
    class TransitionPhase {
        -float duration
    }
    class DyingPhase

    Enemy <|-- Boss
    Enemy o-- BulletPattern
    Enemy o-- MovementPattern
    Enemy --> ShotModifiers
    BulletPattern <|.. RadialBurst
    BulletPattern <|.. Spiral
    BulletPattern <|.. AimedShot
    MovementPattern <|.. LinearMove
    MovementPattern <|.. SineMove
    Boss o-- BossPhase : phase courante
    BossPhase <|.. PatternPhase
    BossPhase <|.. TransitionPhase
    BossPhase <|.. DyingPhase
    PatternPhase o-- "1..*" BulletPattern
    note for BulletPattern "Strategy : réutilisé par les armes du joueur (3.4)"
    note for BossPhase "State : la phase change selon les PV du boss"
```

### 3.4 Vaisseau modulaire — Composite, Factory

Chaque pièce occupe une case de la grille. La hitbox du vaisseau est l'union des cases occupées. Fusionner deux pièces identiques additionne leurs stats et libère une case.

```mermaid
classDiagram
    direction TB
    class Ship {
        <<abstract>>
    }
    class PlayerShip {
        -ShipAssembly assembly
        -int dashCharges
        -float dashTimer
        +update(float delta) void
        +dash() void
        +takeDamage(int amount) void
        +getHitbox() Hitbox
    }
    class ShipComponent {
        <<interface>>
        +getStats() Stats
        +getCells() List~GridPos~
        +update(PlayerShip owner, float delta) void
        +absorb(int damage) int
    }
    class ShipAssembly {
        -ShipBase base
        -List~ShipComponent~ children
        -int maxCells
        +canPlace(ShipPart p, GridPos pos) boolean
        +place(ShipPart p, GridPos pos) void
        +merge(ShipPart p, GridPos pos) void
        +remove(GridPos pos) ShipPart
        +computeHitbox(Vector2 origin) Hitbox
    }
    class ShipPart {
        <<abstract>>
        #PartType type
        #Stats stats
        #GridPos cell
        #int fusionLevel
        +canMergeWith(ShipPart other) boolean
        +mergeWith(ShipPart other) ShipPart
    }
    class Weapon {
        -BulletPattern pattern
        -float cooldown
        +update(PlayerShip owner, float delta) void
    }
    class DashBooster
    class ShieldGenerator {
        -int charge
        +absorb(int damage) int
    }
    class PartType {
        <<enumeration>>
        CANNON
        SPREAD_GUN
        DASH_BOOSTER
        SHIELD
    }
    class Stats {
        <<record>>
        int damage
        float fireRate
        float speed
        int maxHp
        int dashCharges
        float dashCooldown
        +plus(Stats other) Stats
    }
    class ShipBase {
        <<abstract>>
        #ShipBaseId id
        #Stats baseStats
        #List~GridPos~ shape
        +applyPassive(PlayerShip ship)* void
    }
    class StandardBase
    class DashBase
    class ShipBaseId {
        <<enumeration>>
        STANDARD
        DASH
    }
    class ShipFactory {
        +create(ShipBaseId id) PlayerShip
    }
    class PartCatalog {
        +create(PartType type) ShipPart
    }
    class BulletPattern {
        <<interface>>
    }

    Ship <|-- PlayerShip
    PlayerShip *-- "1" ShipAssembly
    ShipComponent <|.. ShipAssembly
    ShipComponent <|.. ShipPart
    ShipAssembly o-- "0..*" ShipComponent : enfants
    ShipAssembly *-- "1" ShipBase
    ShipPart <|-- Weapon
    ShipPart <|-- DashBooster
    ShipPart <|-- ShieldGenerator
    ShipPart --> PartType
    ShipPart --> Stats
    Weapon o-- BulletPattern
    ShipBase <|-- StandardBase
    ShipBase <|-- DashBase
    ShipBase --> ShipBaseId
    ShipFactory ..> PlayerShip : crée
    ShipFactory ..> ShipBase : choisit
    PartCatalog ..> ShipPart : crée
    note for ShipAssembly "Composite : stats, hitbox et absorption = somme des enfants"
```

- `CANNON` et `SPREAD_GUN` sont tous deux des `Weapon` : seul leur `BulletPattern` change. Ajouter une arme ne demande pas de nouvelle classe.
- `PlayerShip.takeDamage()` passe d'abord les dégâts à `assembly.absorb()`. Les boucliers en retirent une partie, le reste touche les PV.
- `DashBase` est la base débloquée par le défi des boosters de dash : son `applyPassive()` réduit le temps de recharge du dash.

### 3.5 Run et niveaux

```mermaid
classDiagram
    direction TB
    class Run {
        -long seed
        -int levelNumber
        -int score
        -PlayerShip ship
        +start() void
        +nextLevel() Level
        +onLevelCleared() void
        +onPlayerDeath() int
    }
    class LevelPool {
        -List~LevelDefinition~ normals
        -List~LevelDefinition~ miniBosses
        -List~LevelDefinition~ bosses
        -Random rng
        +draw(int levelNumber) LevelDefinition
    }
    class LevelDefinition {
        <<record>>
        String id
        LevelType type
        List~SpawnEvent~ timeline
    }
    class LevelType {
        <<enumeration>>
        NORMAL
        MINI_BOSS
        BOSS
    }
    class SpawnEvent {
        <<record>>
        float time
        EnemyType enemy
        Vector2 position
    }
    class LevelLoader {
        +loadAll(FileHandle dir) List~LevelDefinition~
    }
    class Level {
        -LevelDefinition definition
        -List~Enemy~ enemies
        -BulletPool bullets
        -float elapsed
        +update(float delta) void
        +isCleared() boolean
    }
    class EnemyFactory {
        +create(EnemyType type, Vector2 pos, DifficultyScaler d) Enemy
    }
    class DifficultyScaler {
        -int tier
        +increase() void
        +enemyShots() ShotModifiers
        +bossHpMultiplier() float
    }
    class RewardGenerator {
        -Random rng
        -PartCatalog catalog
        -Set~PartType~ unlocked
        +offer(int count) List~ShipPart~
    }
    class CollisionSystem {
        +resolve(PlayerShip p, List~Enemy~ enemies, BulletPool pool) void
    }
    class RunEventBus

    Run *-- LevelPool
    Run *-- RewardGenerator
    Run *-- DifficultyScaler
    Run --> "0..1" Level : niveau courant
    Run --> RunEventBus : publie
    LevelPool o-- "*" LevelDefinition
    LevelLoader ..> LevelDefinition : lit les fichiers
    LevelDefinition --> LevelType
    LevelDefinition *-- "*" SpawnEvent
    Level --> LevelDefinition
    Level ..> EnemyFactory : fait apparaître
    Level *-- CollisionSystem
    EnemyFactory ..> DifficultyScaler
    note for LevelPool "Niveaux faits main, seul l'ordre est tiré.<br>Multiple de 10 : boss. Multiple de 5 : mini-boss."
    note for Run "Une seule graine pour toute la run : rejouable et testable"
```

- `Run.nextLevel()` fait `levelNumber++`, `difficulty.increase()`, puis `pool.draw(levelNumber)`. C'est le « Difficulté +1 → niveau tiré au hasard » de la boucle de run.
- `LevelPool.draw()` tire sans remise. Quand le pool de niveaux normaux est vide, il le remélange. La partie peut donc durer à l'infini.
- `LevelPool` et `RewardGenerator` reçoivent un `Random` créé à partir de la graine de la run. Avec la même graine, les tests obtiennent les mêmes niveaux et les mêmes pièces.

### 3.6 Méta-progression — Observer

```mermaid
classDiagram
    direction TB
    class RunEventBus {
        -List~RunListener~ listeners
        +subscribe(RunListener l) void
        +unsubscribe(RunListener l) void
        +publish(RunEvent e) void
    }
    class RunListener {
        <<interface>>
        +onEvent(RunEvent e) void
    }
    class RunEvent {
        <<sealed interface>>
    }
    class PartEquipped {
        <<record>>
        PartType type
    }
    class EnemyKilled {
        <<record>>
        EnemyType type
    }
    class LevelCleared {
        <<record>>
        int number
    }
    class PlayerDied {
        <<record>>
        int levelReached
        int score
    }
    class Challenge {
        <<abstract>>
        #String description
        #ShipBaseId reward
        #boolean completed
        +onEvent(RunEvent e) void
        +reset() void
        #isMet()* boolean
    }
    class EquipPartsChallenge {
        -PartType type
        -int target
        -int count
    }
    class ReachLevelChallenge {
        -int target
    }
    class PlayerProfile {
        -int points
        -Set~ShipBaseId~ unlockedBases
        -Set~PartType~ unlockedWeapons
        -List~Challenge~ challenges
        +addPoints(int p) void
        +spend(int p) boolean
        +claimRewards() List~ShipBaseId~
        +unlockWeapon(PartType t) void
    }
    class LootBox {
        -int price
        +open(PlayerProfile p, Random rng) Optional~PartType~
    }
    class ProfileRepository {
        <<interface>>
        +load() PlayerProfile
        +save(PlayerProfile p) void
    }
    class JsonProfileRepository
    class InMemoryProfileRepository

    RunEventBus o-- "*" RunListener
    RunEventBus ..> RunEvent
    RunEvent <|.. PartEquipped
    RunEvent <|.. EnemyKilled
    RunEvent <|.. LevelCleared
    RunEvent <|.. PlayerDied
    RunListener <|.. Challenge
    Challenge <|-- EquipPartsChallenge
    Challenge <|-- ReachLevelChallenge
    PlayerProfile *-- "*" Challenge
    LootBox ..> PlayerProfile : dépense des points, débloque une arme
    ProfileRepository <|.. JsonProfileRepository
    ProfileRepository <|.. InMemoryProfileRepository
    ProfileRepository ..> PlayerProfile
    note for RunEventBus "Observer : les défis suivent la run sans que Run les connaisse"
```

- Au début d'une run, `Run` remet à zéro les défis non remplis du profil et les abonne au bus.
- Exemple : `EquipPartsChallenge(DASH_BOOSTER, 5)` compte les `PartEquipped(DASH_BOOSTER)`. À 5, il passe à `completed` et débloquera `DashBase`.
- `JsonProfileRepository` écrit le profil avec `Json` de LibGDX dans `Gdx.files.local()`. `InMemoryProfileRepository` sert aux tests : pas d'écriture sur le disque.

## 4. Diagrammes de séquence

### 4.1 Une frame de jeu

```mermaid
sequenceDiagram
    participant GDX as Boucle LibGDX
    participant G as BulletHellGame
    participant PS as PlayingScreen
    participant L as Level
    participant P as PlayerShip
    participant E as Enemy
    participant Pool as BulletPool
    participant C as CollisionSystem
    participant R as Renderer

    GDX->>G: render()
    G->>PS: render(delta)
    PS->>PS: update(delta)
    PS->>L: update(delta)
    L->>L: fait apparaître les SpawnEvent dont le temps est passé
    L->>P: update(delta)
    P->>P: lit InputHandler, bouge, dash
    P->>Pool: les Weapon tirent via leur BulletPattern
    loop chaque ennemi
        L->>E: update(delta)
        E->>E: movement.positionAt(start, t)
        E->>Pool: pattern.fire(origin, t, pool, shotMods)
    end
    L->>Pool: update(delta)
    L->>C: resolve(player, enemies, pool)
    C-->>P: takeDamage() si touché
    C-->>E: takeDamage() si touché
    alt PV du joueur à 0
        PS->>G: setScreen(GameOverScreen)
    else niveau terminé
        PS->>G: setScreen(RewardScreen)
    end
    PS->>PS: draw(renderer)
    PS->>R: draw(entité) pour chaque entité, puis drawHud(run)
```

### 4.2 Fin de niveau : récompense, éditeur, fusion

```mermaid
sequenceDiagram
    actor J as Joueur
    participant Run
    participant Bus as RunEventBus
    participant RS as RewardScreen
    participant RG as RewardGenerator
    participant ES as EditorScreen
    participant A as ShipAssembly

    Run->>Bus: publish(LevelCleared(n))
    RS->>RG: offer(1)
    RG-->>RS: [pièce tirée]
    RS->>J: affiche la pièce et son effet sur la hitbox
    alt le joueur accepte
        J->>RS: accepter
        RS->>ES: setScreen(EditorScreen)
        J->>ES: dépose la pièce sur une case
        alt case libre
            ES->>A: canPlace(part, pos)
            A-->>ES: true
            ES->>A: place(part, pos)
        else case occupée par une pièce identique
            ES->>A: merge(part, pos)
            A->>A: stats additionnées, une seule case occupée
        end
        ES->>Bus: publish(PartEquipped(type))
    else le joueur refuse
        J->>RS: refuser
    end
    RS->>Run: nextLevel()
    Run->>Run: levelNumber++, difficulty.increase(), pool.draw(n)
```

### 4.3 Mort et méta-progression

```mermaid
sequenceDiagram
    actor J as Joueur
    participant PS as PlayingScreen
    participant Run
    participant Bus as RunEventBus
    participant Ch as Challenge
    participant Prof as PlayerProfile
    participant Repo as ProfileRepository
    participant Shop as ShopScreen
    participant LB as LootBox

    Note over Run,Ch: au début de la run, les défis du profil sont abonnés au bus
    PS->>Run: onPlayerDeath()
    Run->>Bus: publish(PlayerDied(n, score))
    Bus->>Ch: onEvent(PlayerDied)
    Run-->>PS: points gagnés
    PS->>Prof: addPoints(points)
    PS->>Prof: claimRewards()
    Prof-->>PS: bases débloquées par les défis remplis
    PS->>Repo: save(profile)
    opt plus tard, dans la boutique
        J->>Shop: acheter une loot box
        Shop->>LB: open(profile, rng)
        LB->>Prof: spend(price)
        LB->>Prof: unlockWeapon(type)
        LB-->>Shop: arme débloquée
        Shop->>Repo: save(profile)
    end
```

## 5. Diagrammes d'états

### 5.1 Écrans du jeu (`Screen`)

```mermaid
stateDiagram-v2
    state "Menu principal" as Menu
    state "Choix du vaisseau" as Select
    state "Niveau en cours" as Playing
    state "Pause" as Pause
    state "Récompense" as Reward
    state "Éditeur" as Editor
    state "Game over" as Over
    state "Boutique" as Shop

    [*] --> Menu
    Menu --> Select : Jouer
    Menu --> Shop : Boutique
    Shop --> Menu : Retour
    Select --> Playing : base choisie, Run créée
    Playing --> Pause : Échap
    Pause --> Playing : Échap
    Playing --> Reward : niveau terminé
    Playing --> Over : PV à 0
    Reward --> Editor : pièce acceptée
    Reward --> Playing : pièce refusée
    Editor --> Playing : valider
    Over --> Menu : points et déblocages appliqués
```

### 5.2 Phases d'un boss (`BossPhase`)

Exemple pour un boss à 3 phases. Un mini-boss en a moins. Les seuils de PV sont des exemples.

```mermaid
stateDiagram-v2
    state "Phase 1 (PatternPhase)" as P1
    state "Transition" as T1
    state "Phase 2 (PatternPhase)" as P2
    state "Transition" as T2
    state "Phase 3 (PatternPhase)" as P3
    state "Destruction (DyingPhase)" as D

    [*] --> P1
    P1 --> T1 : PV sous 60 %
    T1 --> P2 : fin du délai
    P2 --> T2 : PV sous 30 %
    T2 --> P3 : fin du délai
    P3 --> D : PV à 0
    D --> [*] : niveau terminé
    note right of T1
        TransitionPhase : invulnérable, ne tire pas.
        Laisse le temps de lire le changement de phase.
    end note
```

## 6. Patterns retenus

| Pattern     | Où                                             | Pourquoi                                                                                           |
| ----------- | ---------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| State       | `BulletHellGame` / `Screen`                   | Fourni par LibGDX (`setScreen()`). Chaque écran a ses entrées, sa logique et son rendu, sans grand `switch`. |
| Template Method | `BaseScreen`                               | `render()` est commun : il appelle `update()` puis `draw()`, que chaque écran redéfinit.             |
| State       | `Boss` / `BossPhase`                           | Les phases changent selon les PV. Chaque phase est une classe qu'on peut tester seule.              |
| Strategy    | `BulletPattern`, `MovementPattern`             | Les ennemis et les armes du joueur partagent les mêmes patterns. Une nouvelle arme = un nouveau pattern. |
| Composite   | `ShipComponent` / `ShipAssembly` / `ShipPart`  | On traite le vaisseau entier comme une pièce : stats, hitbox et absorption sont la somme des enfants. |
| Factory     | `ShipFactory`, `PartCatalog`, `EnemyFactory`   | Un seul endroit crée les bases, les pièces gagnées et les ennemis ajustés à la difficulté.          |
| Observer    | `RunEventBus` / `RunListener` / `Challenge`    | Les défis suivent la run sans que `Run` les connaisse. On ajoute un défi sans toucher à `Run`.     |
| Object Pool | `BulletPool` (étend `Pool` de LibGDX)          | Des centaines de balles à l'écran : on les recycle au lieu d'en créer à chaque tir.                 |
| Repository  | `ProfileRepository`                            | La sauvegarde du profil se remplace par une version en mémoire dans les tests.                     |

## 7. Ce qui dépend des questions ouvertes

Voir les questions ouvertes de [[2D-Game/ideas/pierre/Bullet hell roguelike|l'idée]]. L'UML les laisse ouvertes, avec un choix par défaut.

| Question                                   | Où ça se joue                                  | Choix par défaut                     |
| ------------------------------------------ | ---------------------------------------------- | ------------------------------------ |
| Nombre de pièces proposées                 | `RewardGenerator.offer(int count)`             | 1                                    |
| Limite de fusion                           | `ShipPart.canMergeWith()` vérifie `fusionLevel` | une constante `MAX_FUSION`           |
| Les pièces ont-elles des PV ?              | `ShipPart` implémenterait `Damageable`         | non                                  |
| Taille maximale du vaisseau                | `ShipAssembly.maxCells` et `canPlace()`        | champ prévu, valeur à fixer          |
| Éditeur à chaque niveau ou après un boss ? | transition `Reward → Editor`                   | après chaque pièce acceptée          |
| À quoi servent les points ?                | `PlayerProfile.spend()` est générique          | seulement les loot box               |

## 8. Exports (images)

Les 13 diagrammes sont exportés en SVG et en PNG (fond blanc) dans `assets/uml/`, pour le GDD et la soutenance. Ils ne se mettent pas à jour tout seuls : il faut les regénérer si on modifie un diagramme ci-dessus.

| #   | Diagramme                      | Fichiers                                                                                                   |
| --- | ------------------------------ | ---------------------------------------------------------------------------------------------------------- |
| 1   | Cas d'utilisation              | [[2D-Game/ideas/pierre/assets/uml/01-cas-utilisation.png\|PNG]] · [[2D-Game/ideas/pierre/assets/uml/01-cas-utilisation.svg\|SVG]] |
| 2   | Paquets                        | [[2D-Game/ideas/pierre/assets/uml/02-paquets.png\|PNG]] · [[2D-Game/ideas/pierre/assets/uml/02-paquets.svg\|SVG]] |
| 3   | Classes : moteur et écrans     | [[2D-Game/ideas/pierre/assets/uml/03-classes-moteur.png\|PNG]] · [[2D-Game/ideas/pierre/assets/uml/03-classes-moteur.svg\|SVG]] |
| 4   | Classes : entités              | [[2D-Game/ideas/pierre/assets/uml/04-classes-entites.png\|PNG]] · [[2D-Game/ideas/pierre/assets/uml/04-classes-entites.svg\|SVG]] |
| 5   | Classes : comportements        | [[2D-Game/ideas/pierre/assets/uml/05-classes-comportements.png\|PNG]] · [[2D-Game/ideas/pierre/assets/uml/05-classes-comportements.svg\|SVG]] |
| 6   | Classes : vaisseau modulaire   | [[2D-Game/ideas/pierre/assets/uml/06-classes-vaisseau.png\|PNG]] · [[2D-Game/ideas/pierre/assets/uml/06-classes-vaisseau.svg\|SVG]] |
| 7   | Classes : run et niveaux       | [[2D-Game/ideas/pierre/assets/uml/07-classes-run.png\|PNG]] · [[2D-Game/ideas/pierre/assets/uml/07-classes-run.svg\|SVG]] |
| 8   | Classes : méta-progression     | [[2D-Game/ideas/pierre/assets/uml/08-classes-meta.png\|PNG]] · [[2D-Game/ideas/pierre/assets/uml/08-classes-meta.svg\|SVG]] |
| 9   | Séquence : une frame           | [[2D-Game/ideas/pierre/assets/uml/09-sequence-frame.png\|PNG]] · [[2D-Game/ideas/pierre/assets/uml/09-sequence-frame.svg\|SVG]] |
| 10  | Séquence : fin de niveau       | [[2D-Game/ideas/pierre/assets/uml/10-sequence-fin-niveau.png\|PNG]] · [[2D-Game/ideas/pierre/assets/uml/10-sequence-fin-niveau.svg\|SVG]] |
| 11  | Séquence : mort et déblocages  | [[2D-Game/ideas/pierre/assets/uml/11-sequence-mort.png\|PNG]] · [[2D-Game/ideas/pierre/assets/uml/11-sequence-mort.svg\|SVG]] |
| 12  | États : écrans                 | [[2D-Game/ideas/pierre/assets/uml/12-etats-ecrans.png\|PNG]] · [[2D-Game/ideas/pierre/assets/uml/12-etats-ecrans.svg\|SVG]] |
| 13  | États : phases d'un boss       | [[2D-Game/ideas/pierre/assets/uml/13-etats-boss.png\|PNG]] · [[2D-Game/ideas/pierre/assets/uml/13-etats-boss.svg\|SVG]] |
