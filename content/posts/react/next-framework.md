---
title: "Next.js"
date: 2022-04-25T22:47:27+02:00
tags: ["react", "next.js", "javascript", "framework", "SSR", "site statique"]
description: "Fonctionnement du framework Next.js"
---

## Définition

React est "seulement" une librairie qui permet de créer des composants, de les affichers sur une page, et de gérer leur state.
Next.js par contre est un "vrai" framework avec, entre autre, une solution de routing, gestion des assets, et surtout plusieurs solution pour construire le site:

- **rendu côté client** : les pages sont générées côté client (fonctionnement des single page application)
- **rendu côté serveur** : les pages sont générées au moment de la requête, mais côté serveur
- **rendu hybride** : pages statiques générées périodiquement, en tache de fond
- **rendu statique** : les pages sont générées au moment du build

## Les bases

### CLI

```bash
# pour installer
npx create-next-app@latest
# ou
yarn create next-app
# pour du typescript, rajouter le flag "--typescript"

# pour lancer le serveur
yarn dev
```

### Structure des dossiers
