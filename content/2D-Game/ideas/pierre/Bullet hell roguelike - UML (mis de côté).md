---
title: Bullet hell roguelike — UML (mis de côté)
tags: [2d-game, uml, conception, shmup]
auteur: "[[Pierre]]"
statut: mis de côté
---

> [!warning] Mis de côté le 2026-11-24
> Pour finir dans les temps, le jeu est réduit à un **boss rush** (voir [[2D-Game/ideas/pierre/Bullet hell roguelike - UML|l'UML]]). Ces parties sont retirées de l'UML **temporairement**. Pour les remettre, recopier les sections ci-dessous à leur place et regénérer les images.

Ce qui a aussi été retiré ailleurs dans l'UML :

- §1 Cas d'utilisation : `UC7 Acheter une loot box` et `UC8 Voir ses déblocages` (reliés au joueur).
- §2 Paquets : le paquet `meta` (`PlayerProfile, Challenge, LootBox, RunEventBus`), avec `core --> meta`, `screen --> meta` et `meta --> ship`.
- §3.1 : `ShopScreen` (`BaseScreen <|-- ShopScreen`) et le champ `-PlayerProfile profile` de `BulletHellGame`.
- §3.4 : `DashBase` était débloquée par le défi des boosters de dash.
- §5.1 : l'état « Boutique » (`Menu --> Shop : Boutique`, `Shop --> Menu : Retour`), et `Over --> Menu : points et déblocages appliqués`.
- §6 : les patterns Observer (`RunEventBus` / `RunListener` / `Challenge`) et Repository (`ProfileRepository`).
- §7 : « À quoi servent les points ? » → seulement les loot box (armes) ; « Nombre de niveaux dans le pool » → au moins 20.
- Code : `run/RunEvent.java` et `run/RunListener.java`, écrits pendant le socle, ont été supprimés.

Images d'avant : [[2D-Game/ideas/pierre/assets/uml/07b-classes-niveaux-mis-de-cote.png|run et niveaux (PNG)]] · [[2D-Game/ideas/pierre/assets/uml/08-classes-meta.png|méta-progression (PNG)]] · [[2D-Game/ideas/pierre/assets/uml/11-sequence-mort.png|mort et déblocages (PNG)]]

## Ancienne §3.5 — niveaux normaux, mini-boss et boss


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

## Ancienne §3.6 — Méta-progression


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

## Ancienne §4.3 — Mort et méta-progression


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

## Ancien game design — niveaux

- Les niveaux sont **créés à la main** à l'avance (patterns d'ennemis et de balles fixes).
- **Seul leur ordre est aléatoire.** À chaque run, on en tire un au hasard dans le pool.
- La partie est **infinie**. On joue jusqu'à la mort.
- Un **mini-boss tous les 5 niveaux** et un **boss tous les 10 niveaux**.

```mermaid
flowchart LR
    N1[1-4 : niveaux aléatoires] --> MB5((5 : mini-boss))
    MB5 --> N6[6-9 : niveaux aléatoires]
    N6 --> B10((10 : boss))
    B10 --> N11[11-14]
    N11 --> MB15((15 : mini-boss))
    MB15 --> N16[16-19]
    N16 --> B20((20 : boss))
    B20 --> INF[... à l'infini]
```

## Ancien game design — méta-progression

Il y a deux manières de débloquer du contenu :

| Ce qu'on débloque               | Comment                                               | Exemple                                                                                               |
| ------------------------------- | ----------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| **Nouvelles bases de vaisseau** | En remplissant des **défis pendant une run**          | Équiper assez de boosters de dash en une partie débloque un vaisseau qui recharge ses dashs plus vite |
| **Nouvelles armes**             | Avec des **loot box** achetées avec les points gagnés (les points ne servent qu'à ça) | Une loot box donne une arme qui pourra ensuite apparaître dans les récompenses                        |
|                                 |                                                       |                                                                                                       |

```mermaid
flowchart LR
    R[Actions pendant la run] --> D{Défi rempli ?<br/>ex. N boosters de dash}
    D -- Oui --> V[Nouvelle base de vaisseau]
    M[Mort] --> P[Points]
    P --> L[Loot box]
    L --> W[Nouvelle arme débloquée]
    V --> S[Prochaine run]
    W --> S
```

## Anciennes réponses

- Les **points** obtenus à la mort servent **uniquement à acheter des loot box** (donc des armes).
- Il faut créer **au moins 20 niveaux** pour que l'ordre aléatoire reste varié.
- Pilier : « On progresse même en perdant. La mort débloque de nouveaux vaisseaux et de nouvelles armes. »
