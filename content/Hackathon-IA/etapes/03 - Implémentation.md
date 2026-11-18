---
title: 03 - Implémentation
tags: [hackathon-ia, dev]
equipe: ["[[Pierre]]"]
---

Retour : [[Hackathon-IA/index|Hackathon IA]] · Conception : [[Hackathon-IA/ideas/pierre/Fiche de préparation psy - UML|UML]]

Dépôt : [Code4sud/2026-equipe-12](https://github.com/Code4sud/2026-equipe-12) (cloné dans `Desktop/2026-equipe-12`).

## Fait

- [x] `data/rome.json` : 506 macro-compétences (dont 357 avec un code RIASEC) et 1 923 fiches métiers, générés par `scripts/build-rome.py`
- [x] `lib/analyse.js` : appel à Claude (Opus 5.5) avec une sortie JSON, puis les garde-fous côté serveur
- [x] `server.js` : serveur Node sans framework. Il sert les fichiers statiques et `POST /api/analyse`, la clé API reste côté serveur et aucun récit n'est enregistré
- [x] `index.html` / `app.js` / `style.css` : les 3 écrans, la dictée vocale (Chrome), les relances, 3 récits d'exemple inventés et l'impression en PDF
- [x] Section RIASEC sur la fiche et puces RIASEC sur les cartes
- [x] README et `projet.json` remplis

## Reste à faire

- [ ] Tester l'analyse IA de bout en bout (il faut une clé API Anthropic dans `ANTHROPIC_API_KEY`, puis `npm start`)
- [ ] `npm run demo` : pré-calculer les 3 exemples pour la démo statique GitHub Pages
- [ ] Vérifier le rendu sur mobile et l'impression
- [ ] Captures d'écran dans `captures/` et listées dans `projet.json`
- [ ] Remplir les écoles dans `projet.json`
- [ ] Commit puis push sur `main` (la fiche du site du hackathon se met à jour en moins d'une minute)
