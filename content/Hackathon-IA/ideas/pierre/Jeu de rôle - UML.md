---
title: Jeu de rôle des compétences — UML
tags: [hackathon-ia, uml, conception, jeu, jdr]
auteur: "[[Pierre]]"
statut: brouillon
---

UML du [[Hackathon-IA/ideas/pierre/Jeu de rôle des compétences|jeu de rôle des compétences]], tel qu'il est codé dans le dépôt `2026-equipe-12` (commit `b5f316c`, 19/11/2026). Le choix du format et du matériel est dans [[Hackathon-IA/ideas/pierre/Jeu de rôle - mise en place|la mise en place]]. Le contrat technique détaillé est `SPEC.md`.

Cible : **Cloudflare Workers + Durable Objects** (JavaScript, ES modules), front en HTML/CSS/JS vanilla, MJ IA `claude-opus-5-5` via `@anthropic-ai/sdk`, avec un **MJ de secours** déterministe (mock) quand l'IA ne répond pas. En prod sur https://potentiel-cache.planbhunalp.workers.dev.

**Choix de départ : la logique du jeu est une machine à états PURE** (`src/jeu.js`, sans réseau, sans horloge, sans aléatoire). Le Durable Object ne fait qu'orchestrer : il applique un événement, persiste l'état, exécute les effets (minuteur, appels IA) et envoie à chacun **sa** vue filtrée. Ça rend tout le jeu testable sans serveur, et la confidentialité vérifiable : une réponse n'existe que dans la vue de son auteur.

## 1. Cas d'utilisation

```mermaid
flowchart LR
    J([Joueur, sur son téléphone])
    A([Animateur, écran commun])
    P([Psy ou conseiller])
    MJ([MJ IA])
    subgraph Jeu
        UC1([Rejoindre avec le QR code])
        UC2([Créer son personnage en racontant son vécu])
        UC3([Valider « J'ai lu »])
        UC4([Discuter à voix haute])
        UC5([Répondre en privé])
        UC6([Choisir parmi 4 propositions])
        UC7([Garder ou refuser sa compétence])
        UC8([Coller la gommette sur sa fiche papier])
        UC9([Lancer la partie, régler les minuteurs])
        UC10([Voir qui a fini, passer à la suite])
        UC11([Évaluer les réponses])
        UC12([Donner un indice])
        UC13([Attribuer une compétence à chacun])
        UC14([Lire et imprimer la fiche])
    end
    J --- UC1 & UC2 & UC3 & UC5 & UC6 & UC7 & UC8
    J --- UC4
    A --- UC9 & UC10 & UC4
    MJ --- UC11 & UC12 & UC13
    P --- UC14
    UC12 -.->|"«extend» si personne n'a trouvé"| UC11
    UC6 -.->|"«extend» après l'indice"| UC12
```

## 2. Déploiement et composants

```mermaid
flowchart LR
    subgraph Salle
        E["Écran commun (PC ou vidéoprojecteur)<br/>ecran.html + ecran.js"]
        T1["Téléphone joueur<br/>joueur.html + joueur.js"]
        T2["Téléphone joueur"]
        IMP["Imprimante<br/>fiche.html, fiche-papier.html"]
    end
    subgraph Cloudflare
        W["Worker potentiel-cache<br/>src/worker.js : routes HTTP"]
        AS[("Static Assets<br/>public/ : pages, JS, CSS,<br/>illustrations, data/*.json")]
        DO["Durable Object Partie<br/>une instance par code de partie<br/>src/partie.js"]
        JEU["src/jeu.js<br/>machine à états pure"]
        MJ["src/mj.js<br/>évaluer, attribuer"]
        AN["src/analyse.js<br/>récit → compétences de départ"]
        DATA[("src/donnees.js<br/>rome.json, problemes.json")]
        SQL[("Stockage SQLite du DO<br/>état, gardes IA, alarme")]
    end
    API["API Anthropic<br/>claude-opus-5-5"]
    MOCK["MJ de secours (mock)<br/>mots-clés, règles"]

    E -- "HTTPS : POST /api/parties" --> W
    E & T1 & T2 -- "WebSocket /api/parties/CODE/ws" --> W
    T1 -- "GET /p/CODE" --> W
    W --> AS
    W -- "getByName(code)" --> DO
    DO --> JEU
    DO --> MJ & AN
    MJ & AN --> API
    MJ & AN -.->|"échec ou pas de crédit"| MOCK
    MJ & AN --> DATA
    DO --> SQL
    IMP -- "GET /api/parties/CODE/fiche/ID" --> W
```

| Route | Rôle |
|---|---|
| `POST /api/parties` | crée une partie, renvoie `{ code, cleEcran }` (la clé animateur ne sort jamais ailleurs) |
| `GET /api/parties/CODE/ws?role=ecran&cle=…` | WebSocket de l'écran animateur (refus 4003 sans la bonne clé) |
| `GET /api/parties/CODE/ws?role=joueur&joueur=ID` | WebSocket d'un joueur (reconnexion avec le même ID) |
| `GET /api/parties/CODE/fiche/ID` | fiche JSON d'un joueur (compétences gardées, RIASEC) |
| `GET /p/CODE` | lien du QR code → page joueur |
| `POST /api/analyse` | outil solo (fiche sans jeu) |

## 3. Classes — état d'une partie

```mermaid
classDiagram
    direction LR
    class Partie {
        <<Durable Object>>
        +init(code, nbProblemes, durees)
        +fetch(request) WebSocket
        +webSocketMessage(ws, message)
        +alarm()
        +fiche(joueurId)
        -traiter(evenement)
        -executer(effets)
        -diffuser()
        -calculerIA(effet)
    }
    class Etat {
        +string code
        +string cleEcran
        +Phase phase
        +Reglages reglages
        +string[] problemes
        +int problemeIndex
        +int tour
        +string tourId
        +number minuteurDebut
        +number minuteurFin
        +bool tempsEcoule
        +Evaluation evaluation
        +Map attribution
    }
    class Reglages {
        +int nbProblemes
        +int dureeEchange
        +int dureeReponse
    }
    class Joueur {
        +string id
        +string pseudo
        +string avatar
        +bool connecte
        +string recit
        +Depart depart
    }
    class Depart {
        +string etat
        +Competence[] propositions
        +Competence[] gardees
    }
    class Reponse {
        +bool lu
        +bool luIndice
        +string tour1
        +int choix
    }
    class Competence {
        +string code
        +string libelle
        +string mots
        +string preuve
        +string typePreuve
        +string[] riasec
        +int problemeIndex
        +int departIndex
        +bool gardee
    }
    class Evaluation {
        +bool resolu
        +string synthese
        +int indiceIndex
    }
    class Probleme {
        +string id
        +string titre
        +string situation
        +string question
        +string[] riasec
        +Solution solution
        +string[] indices
        +Choix[] choix
        +string[] competences
    }
    class Choix {
        +string texte
        +bool correct
        +string competence
    }
    class MJ {
        <<src/mj.js>>
        +evaluer(probleme, reponses)
        +attribuer(probleme, historique)
    }
    Partie "1" *-- "1" Etat
    Etat *-- Reglages
    Etat "1" *-- "1..6" Joueur
    Etat "1" o-- "1..6" Probleme : catalogue tiré
    Joueur *-- Depart
    Joueur "1" *-- "*" Reponse : une par problème
    Joueur "1" *-- "*" Competence : départ et une par problème
    Etat *-- "0..1" Evaluation
    Probleme *-- "4" Choix
    Partie ..> MJ : effets evaluer, attribuer
```

`typePreuve` vaut `vecu` (compétence de départ, tirée du récit), `jeu` (montrée pendant le jeu) ou `participation` (filet de sécurité : personne ne repart sans compétence). `gardee` vaut `null` tant que le joueur n'a pas répondu, ce qui permet de lui reproposer la carte plus tard.

## 4. États — les phases d'une partie

```mermaid
stateDiagram-v2
    [*] --> accueil
    accueil --> creation : animateur Commencer
    creation --> presentation : tous ont validé leur personnage, ou animateur

    presentation --> echange : tous ont lu, ou animateur
    echange --> reponse : animateur Passer aux réponses
    reponse --> evaluation : tous ont répondu, ou animateur
    evaluation --> solution : résolu
    evaluation --> indice : personne n'a trouvé
    indice --> echange2 : tous ont lu l'indice, ou animateur
    echange2 --> choix : animateur Passer aux propositions
    choix --> solution : tous ont choisi, ou animateur
    solution --> competences : animateur Suivant

    competences --> presentation : tous ont gardé ou refusé, ou animateur, problème suivant
    competences --> fin : dernier problème
    fin --> [*] : nettoyage 1 h après
```

Règles portées par ces transitions :

- **À la fin d'un minuteur, rien n'avance tout seul.** L'alarme met seulement `tempsEcoule` à vrai. L'écran affiche « Temps écoulé » et les joueurs en retard peuvent encore répondre.
- **On s'arrête dès qu'une personne a bien répondu** : l'évaluation porte sur toutes les réponses du tour, et un seul « résolu » suffit.
- **Personne n'apprend qu'il s'est trompé** : aucune vue ne dit qui a trouvé ni quelle proposition était la bonne.
- **Une compétence par joueur et par problème**, donnée en phase `competences`, y compris à ceux qui n'ont pas trouvé.
- Si le MJ IA ne répond pas (`evaluation`, `competences`), le bouton « Suivant » de l'animateur relance l'étape avec le MJ de secours.

## 5. Séquence — un tour de jeu

```mermaid
sequenceDiagram
    autonumber
    actor Ana as Animateur (écran)
    actor J as Joueurs (téléphones)
    participant DO as Partie (Durable Object)
    participant Jeu as jeu.js
    participant MJ as MJ IA (ou secours)

    DO-->>Ana: vue écran (situation, illustration, statut par joueur)
    DO-->>J: vue joueur (bouton J'ai lu)
    J->>DO: lu (chaque joueur)
    DO->>Jeu: appliquer(etat, lu)
    Jeu-->>DO: tous ont lu, phase echange, effet minuteur
    DO-->>Ana: minuteur + statuts
    Note over DO: alarme à 0, tempsEcoule = vrai, on attend
    Ana->>DO: fin_minuteur (Passer aux réponses)
    J->>DO: reponse (texte privé)
    DO-->>Ana: Lina ✓ Sam … (jamais le texte)
    J->>DO: reponse (dernier joueur)
    Jeu-->>DO: phase evaluation, effet evaluer
    DO->>MJ: evaluer(problème, réponses)
    MJ-->>DO: resolu oui ou non (+ synthèse)
    alt résolu
        DO-->>Ana: Problème résolu ! + synthèse
    else personne n'a trouvé
        DO-->>Ana: indice
        J->>DO: lu (indice)
        Note over Ana,J: nouvel échange puis 4 choix privés
        J->>DO: choix(index)
        DO-->>Ana: solution du problème
    end
    Ana->>DO: suivant
    DO->>MJ: attribuer(problème, historique de chaque joueur)
    MJ-->>DO: une compétence par joueur, avec sa preuve
    DO-->>J: ta compétence, la preuve, sa couleur RIASEC
    J->>DO: garder_competence(oui ou non)
    Note over J: le joueur colle la gommette sur sa fiche papier
    Jeu-->>DO: tous ont répondu, problème suivant ou fin
```

## 6. Activité — attribuer une compétence à un joueur

```mermaid
flowchart TD
    A([Historique du joueur :<br/>réponse du tour 1, choix éventuel]) --> R{Réponse de remplissage ?<br/>« je sais pas », « bof »}
    R -- oui --> R2[Le texte est ignoré :<br/>il ne prouve rien]
    R -- non --> B
    R2 --> B{Reste-t-il un texte<br/>ou un choix ?}
    B -- non --> E[Compétence de participation<br/>« A participé à la discussion »]
    B -- oui --> F[MJ IA : choisit dans la liste FERMÉE<br/>des compétences du problème]
    F --> G{Code dans la liste ?<br/>Citation copiée mot pour mot<br/>dans sa réponse ?}
    G -- oui --> H[Compétence de jeu<br/>preuve = sa phrase, ou le texte de son choix]
    G -- "non, ou IA indisponible" --> I{MJ de secours :<br/>mots-clés d'une compétence<br/>dans la réponse ?}
    I -- oui --> H
    I -- non --> C{A fait un choix<br/>parmi les 4 ?}
    C -- oui --> D[Compétence écrite pour ce choix<br/>preuve = texte du choix]
    C -- non --> S{Vraie phrase<br/>sur le sujet ?}
    S -- oui --> G2[Compétence la plus générique<br/>du problème]
    S -- non --> E
    D & E & H & G2 --> K([Carte proposée au joueur :<br/>« Je la garde » ou « Pas moi »])
```

Les garde-fous viennent de la [[Hackathon-IA/ideas/pierre/Fiche de préparation psy - UML|fiche de préparation psy]] : liste ROME fermée, citation vérifiée, et l'humain qui décide en dernier.

## 7. Qui voit quoi

| Donnée | Auteur | Autres joueurs | Écran commun | Fiche |
|---|---|---|---|---|
| Récit de départ | ✅ | ❌ | ❌ | preuve « vécu » si gardée |
| Texte d'une réponse | ✅ | ❌ | ❌ | preuve « montrée en jeu » si gardée |
| Choix parmi les 4 | ✅ | ❌ | ❌ | ❌ |
| A lu, a répondu, a choisi | ✅ | ❌ | ✅ (✓ ou …) | ❌ |
| Qui a trouvé la solution | ❌ | ❌ | ❌ | ❌ |
| Compétence reçue | ✅ | ❌ | ❌ | ✅ si gardée |

Le serveur construit une vue par rôle (`vue(etat, role, joueurId)`). Les tests et la simulation vérifient qu'aucun texte de réponse ni de récit n'arrive jamais à l'écran ou chez un autre joueur.

## 8. Données

```mermaid
flowchart LR
    ROME[("ROME 4.0 v26M10<br/>506 macro-compétences<br/>RIASEC sur 357")] --> R["public/data/rome.json"]
    ONET[("O*NET 31.0<br/>profils RIASEC")] --> M["public/data/metiers.json<br/>675 métiers"]
    FR[("Table O*NET FR<br/>titres, Job Zones")] --> M
    ESCO[("ESCO v1.2<br/>+ correspondance ESCO / O*NET")] --> M
    PB["data/problemes.json<br/>6 problèmes illustrés"] --> JEU["Jeu"]
    R --> JEU
    R & M --> FICHE["Fiche : compétences,<br/>profil RIASEC, métiers proches"]
```

Les métiers proches se calculent par **cosinus** entre le profil RIASEC des compétences gardées et celui de chaque métier, sur des vecteurs centrés (lettre majeure = 2, mineure = 1).
