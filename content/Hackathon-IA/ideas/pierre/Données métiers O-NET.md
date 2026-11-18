---
title: Données métiers O*NET — intérêts et niveau de préparation
tags: [hackathon-ia, donnees, riasec, onet, metiers]
auteur: "[[Pierre]]"
statut: intégré
---

Utilisation de la table `table-metiers-onet-fr-jobzones.xlsx` et de la base O\*NET dans le [[Hackathon-IA/ideas/pierre/Jeu de rôle des compétences|jeu de rôle des compétences]] et la [[Hackathon-IA/ideas/pierre/Fiche de préparation psy|fiche pour le psy]].

## Sources

| Source | Contenu | Licence |
|---|---|---|
| `table-metiers-onet-fr-jobzones.xlsx` | 721 métiers O\*NET **traduits en français** : titre, description simple d'environ 100 mots, niveau de préparation (Job Zone 1 à 5). Colonne « Zone réelle » corrigée à la main d'après l'ONISEP sur 93 lignes. | Origine à confirmer : la table semble utilisée pour les Rapports des Possibles _(hypothèse)_ |
| [O\*NET 31.0](https://www.onetcenter.org/database.html) · Career Interest Types | **Profil RIASEC de chaque métier** (échelle 1 à 7) + les 3 types dominants | CC BY 4.0 (USDOL/ETA) |
| O\*NET 31.0 · Specific Interest Areas | **41 domaines d'intérêt précis** par métier (Mécanique, Animaux, Cuisine, Action sociale…) | CC BY 4.0 |
| O\*NET 31.0 · Interests Illustrative Activities | 4 activités d'exemple par type RIASEC et par domaine (« Réparer des appareils ménagers »…) | CC BY 4.0 |

## Ce qui est intégré (dépôt `2026-equipe-12`)

- `scripts/build-metiers.py` → `data/metiers.json` : **675 métiers**, chacun avec son titre et sa description en français, son niveau (dont **84 corrigés d'après l'ONISEP**), son profil RIASEC et ses 3 domaines d'intérêt principaux.
- 46 métiers sont écartés : les catégories « All Other » d'O\*NET, qui n'ont pas de profil RIASEC, et 4 doublons.
- `metiers-proches.js` : un **rapprochement sans IA**.
  1. Les compétences gardées par le joueur donnent un profil RIASEC : chaque type principal compte 2 points, chaque type secondaire 1 point.
  2. Ce profil est comparé à celui des 675 métiers (similarité cosinus sur les profils centrés).
  3. On garde **5 métiers accessibles rapidement (niveaux 1 à 3)** et **3 avec des études plus longues (niveaux 4 et 5)**.
- La fiche affiche une section **« Métiers à découvrir »** : titre, les 2 types RIASEC du métier, le niveau de préparation en clair, puis la description et les domaines quand on clique. La mention de source CC BY 4.0 est en bas.

## Exemples (profils de test inventés)

| Profil | Métiers accessibles proposés |
|---|---|
| Inès : Social, Conventionnel, Entreprenant | Auxiliaire de vie · Garde d'enfants à domicile · AESH · Auxiliaire de puériculture · Accompagnateur de voyages |
| Yanis : Réaliste, Entreprenant | Ouvrier agricole · Conducteur de drague · Ouvrier d'élevage · Sapeur-pompier · Plongeur professionnel |
| Lou : Social, Entreprenant, Artistique | Guide touristique · Animateur radio · Animateur socioculturel · Acteur · Concierge |

## Idées pour la suite du jeu

- **Fin de partie** : la même section « Métiers à découvrir », calculée à partir des compétences gagnées pendant la partie.
- **Inspiration pour les problèmes** : partir des activités d'exemple O\*NET par domaine (réparer, conduire, soigner un animal, cuisiner, vendre…) pour écrire des situations qui couvrent les 6 types RIASEC.
- **Domaines d'intérêt** : on pourrait les afficher sur la fiche personnage (« Tu as montré de l'intérêt pour : Mécanique, Animaux »). C'est plus concret que les lettres RIASEC.

## Points d'attention

- [ ] **Origine de la table** : si elle vient du Centre des Possibles, il faut leur accord avant de la publier dans le dépôt public (les données O\*NET seules sont sous CC BY 4.0).
- [ ] **O\*NET ↔ ROME** : il n'existe pas de table de correspondance directe. Il y a bien une correspondance officielle [ESCO ↔ O\*NET](https://esco.ec.europa.eu/en/use-esco/other-crosswalks), mais rien trouvé entre ROME et ESCO dans l'open data de France Travail. Le lien passe donc par le **RIASEC**.
- [ ] Le classement O\*NET est américain : même corrigé, le niveau reste indicatif.
- [ ] La section n'a pas encore été vérifiée dans le navigateur, faute de clé API pour générer une analyse. Le calcul a été testé en ligne de commande sur les 3 profils ci-dessus.
