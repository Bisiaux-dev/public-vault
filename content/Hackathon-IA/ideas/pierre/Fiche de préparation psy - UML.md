---
title: Fiche de préparation psy — UML
tags: [hackathon-ia, uml, conception]
auteur: "[[Pierre]]"
statut: brouillon
---

UML de l'idée [[Hackathon-IA/ideas/pierre/Fiche de préparation psy|Potentiel caché — fiche de préparation pour le psy]]. Ça couvre la partie UML de [[02 - Conception (parcours & UML)]].

Cible : Node.js ≥ 20, sans framework, ES modules. Front en HTML/CSS/JS vanilla. Modèle `claude-opus-5-5` via `@anthropic-ai/sdk`.

**Choix de départ : l'IA ne fait qu'une seule chose, proposer des compétences prouvées.** Tout le reste est du code déterministe sur les données du ROME : la vérification des preuves, le RIASEC et les familles de métiers. C'est ce qui rend le résultat explicable au jury.

## 1. Cas d'utilisation

```mermaid
flowchart LR
    J([Jeune])
    P([Psychologue du travail])
    subgraph Outil
        UC1([Raconter ses expériences])
        UC2([Dicter à la voix])
        UC3([Voir les compétences proposées])
        UC4([Garder ou retirer une compétence])
        UC5([Reformuler avec ses mots])
        UC6([Générer la fiche])
        UC7([Lire la fiche avant l'entretien])
        UC8([Imprimer la fiche])
    end
    J --- UC1 & UC3 & UC4 & UC6
    P --- UC7 & UC8
    UC2 -. «extend» .-> UC1
    UC5 -. «extend» .-> UC4
```

## 2. Composants

```mermaid
flowchart LR
    subgraph Navigateur
        UI["index.html + app.js<br/>3 écrans, dictée, impression"]
        CALC["Calculs déterministes<br/>RIASEC, familles de métiers"]
    end
    subgraph Serveur Node
        SRV["server.js<br/>statique + POST /api/analyse"]
        ANA["lib/analyse.js<br/>prompt, schéma JSON, garde-fous"]
    end
    ROME[("data/rome.json<br/>ROME 4.0 v26M10")]
    API["API Anthropic<br/>claude-opus-5-5"]
    UI -- "récit (JSON)" --> SRV
    SRV --> ANA
    ANA --> API
    ANA --> ROME
    CALC --> ROME
    UI --> CALC
```

En démo statique (GitHub Pages, sans serveur), `app.js` charge les résultats pré-calculés `demo/<exemple>.json`, produits par `npm run demo`.

## 3. Séquence — analyse d'un récit

```mermaid
sequenceDiagram
    actor J as Jeune
    participant UI as app.js
    participant S as server.js
    participant A as analyse.js
    participant C as Claude
    J->>UI: écrit ou dicte son récit
    UI->>S: POST /api/analyse {recit}
    S->>S: valide (30 à 10 000 caractères)
    S->>A: analyser(recit)
    A->>C: system (règles + 506 macro-compétences, en cache) + récit
    C-->>A: JSON {competences[], pistes_entretien[], a_approfondir[]}
    A->>A: garde-fous (§4)
    A-->>S: compétences retenues + écartées
    S-->>UI: 200 JSON
    UI-->>J: cartes à valider
```

## 4. Activité — garde-fous sur chaque proposition

```mermaid
flowchart TD
    P[Proposition de l'IA] --> Q{Code dans le ROME ?}
    Q -- Non --> X1[Écartée : hors référentiel]
    Q -- Oui --> R{Citation retrouvée dans le récit ?<br/>casse, apostrophes et espaces normalisés}
    R -- Non --> X2[Écartée : pas de preuve]
    R -- Oui --> D{Déjà proposée ?<br/>même code + même citation}
    D -- Oui --> X3[Ignorée]
    D -- Non --> OK[Retenue + libellé, catégorie et RIASEC du ROME]
    X1 & X2 --> CPT[Comptées dans le pied de la fiche]
```

Les règles côté prompt (aucune déduction sensible, tutoiement, pas de score…) sont dans `SYSTEM`, dans `lib/analyse.js`.

## 5. États — une carte de compétence

```mermaid
stateDiagram-v2
    [*] --> Proposee
    Proposee --> Gardee: C'est moi
    Proposee --> Retiree: Pas moi
    Gardee --> Proposee: recliquer (annuler)
    Retiree --> Proposee: recliquer (annuler)
    Gardee --> Gardee: reformuler ses mots
    Gardee --> SurLaFiche: Préparer la fiche
    SurLaFiche --> [*]
```

Le bouton « Préparer la fiche » est désactivé tant qu'aucune carte n'est gardée.

## 6. Modèle de données

```mermaid
classDiagram
    class Rome {
        source: string
        macros: MacroCompetence[]
        fiches: dictionnaire code vers FicheMetier
        domainesPro: dictionnaire code vers libellé
    }
    class MacroCompetence {
        code: string, ex. 1A102
        libelle: string
        categorie: Savoir-faire ou Savoir-être
        enjeu: string
        objectif: string
        riasec: string[], majeur puis mineur
        fiches: string[], codes ROME
    }
    class FicheMetier {
        titre: string
        domaine: string, ex. K12
        appellations: string[]
    }
    class CompetenceProposee {
        code_rome: string
        situation: string
        citation: string
        mots_du_jeune: string
        question_psy: string
        libelle_rome: string
        riasec: string[]
    }
    class Analyse {
        competences: CompetenceProposee[]
        ecartees: object[]
        pistes_entretien: string[]
        a_approfondir: string[]
    }
    Rome "1" *-- "506" MacroCompetence
    Rome "1" *-- "1923" FicheMetier
    MacroCompetence "*" --> "*" FicheMetier : mobilisée dans
    Analyse "1" *-- "*" CompetenceProposee
    CompetenceProposee ..> MacroCompetence : code_rome
```

## 7. Familles de métiers — algorithme

1. Pour chaque compétence gardée, on prend les fiches ROME qui la mobilisent.
2. On compte, pour chaque fiche, combien de compétences gardées y mènent.
3. On regroupe par domaine professionnel (ex. `K12`) et on additionne.
4. On garde les **4 domaines** qui reviennent le plus, et dans chacun les **3 fiches** les plus partagées, avec un lien Métierscope.

C'est présenté comme « à explorer ensemble, pas une recommandation ».
