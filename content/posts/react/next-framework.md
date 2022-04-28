---
title: "Next.js"
date: 2022-04-25T22:47:27+02:00
tags: ["react", "next.js", "javascript", "framework", "SSR", "site statique"]
description: "Fonctionnement du framework Next.js"
---

## La base

### Définition

React est "seulement" une librairie qui permet de créer des composants, de les affichers sur une page, et de gérer leur state.
Next.js par contre est un "vrai" framework avec, entre autre, une solution de routing, gestion des assets, et surtout plusieurs solution pour construire le site:

- **rendu côté client** : les pages sont générées côté client (fonctionnement des single page application)
- **rendu côté serveur** : les pages sont générées au moment de la requête, mais côté serveur
- **rendu hybride** : pages statiques générées périodiquement, en tache de fond
- **rendu statique** : les pages sont générées au moment du build

### CLI

```bash
# pour installer
npx create-next-app@latest
# ou
yarn create next-app
# pour du typescript, rajouter le flag "--typescript"

# pour lancer le serveur de dev
yarn dev

# pour builder le projet
yarn run build # des infos apparaissent pour nous informer de quel type de render in s'agit (statique, SSR, CSR, hybride)
# output généré dans le dossier "next"

# si rendu complètement statique, on peut exporter le site pour l'héberger sur un serveur sans node
npx next export
# output généra dans le dossier "out"
```

### Structure des dossiers

- **public** : pour les assets statiques directement accessibles
- **pages** : Pour générer les pages du site. Les URL vont dépendre de la structure des dossiers
  - pour passer des **paramètres d'url** (id ou slug par ex), on va mettre le nom du fichier entre crochets
- **pages/api** : regroupe nos services d'API
- **style** : pour le style (système de modules et scss géré si sass installé)

## Fonctionnement "normal" d'une appli React, sans Next.js

La page est une coquille vide, il faut attendre le chargement du bundle JS pour ensuite aller chercher les données brutes côté client.

```jsx
import Head from "next/head";
import {useState, useEffect} from "react";
import styles from "../styles.Home.module.css";

export default function Home() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    fetch('http://ma/super/api)
    .then(r => r.json())
    .then(setPosts)
  }, [])

  return (
    <>
      <Head>
        <title>Mon super blog</title>
      </Head>
      <ul>
        {posts.map(post => <li>
          <h3>{{post.title}}</h3>
        </li>)}
      </ul>
    </>
  )
}
```

## Génération statique avec `getStaticProps()`

Dans ce cas c'est un render STATIQUE => les pages sont générées au moment du build.
En fait, ça génère du html, et des fichier json qui contiennent les données.

```jsx
import Head from "next/head";
import {useState, useEffect} from "react";
import styles from "../styles.Home.module.css";

export default function Home({posts}) {
  return (
    <>
      <Head>
        <title>Mon super blog</title>
      </Head>
      <ul>
        {posts.map(post => <li>
          <h3>{{post.title}}</h3>
        </li>)}
      </ul>
    </>
  )
}

export async function getStaticProps() {
  const posts = await fetch('http://ma/super/api)
    .then(r => r.json())
  return {
    props: {
      posts
    }
  }
}
```
