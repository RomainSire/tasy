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

## Rendu côté client : Fonctionnement "normal" d'une appli React, sans Next.js

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
        <meta blabla />
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

## Rendu statique avec `getStaticProps()` et `getStaticPaths()`

Dans ce cas c'est un render STATIQUE => les pages sont générées au moment du build.
En fait, ça génère du html, et des fichier json qui contiennent les données. Puis les données seront "réconciliées".

```jsx
import Head from "next/head";
import Link from "next/Link";
import styles from "../styles.Home.module.css";

export default function Home({posts}) {
  return (
    <>
      <Head>
        <title>Mon super blog</title>
        <meta blabla />
      </Head>
      <ul>
        {posts.map(post => <li>
          <Link href={`/blog/${post.id}`}>
            <a>
              <h3>{{post.title}}</h3>
            </a>
          </Link>
        </li>)}
      </ul>
    </>
  )
}

export async function getStaticProps() {
  const posts = await fetch('http://ma/super/api/posts')
    .then(r => r.json())
  return {
    props: {
      posts
    }
  }
}
```

Puis on crée un nouveau fichier dans le dossier page, qui va correspondre à la page de détail d'un article. => pages/blog/[id].js (du coup, on pourra y accéder à l'url localhost:3000/blog/12 pour l'article avec l'id 12)

Mais là du coup, il faut indiquer à Next tous les id possibles pour qu'il puisse générer côté serveur toutes les pages possibles. Ca se fait avec la méthode **`getStaticPaths()`**

```jsx
import Link from "next/Link";

export default function Post({ post }) {
  return (
    <>
      <Link href="/">
        <a>Retour</a>
      </Link>
      <main>
        <h1>{post.title}</h1>
        <p>{post.body}</p>
      </main>
    </>
  );
}

// pour générer statiquement les pages
export async function getStaticProps({ params }) {
  const posts = await fetch(`http://ma/super/api/posts/${params.id}`).then(
    (r) => r.json()
  );
  return {
    props: {
      post,
    },
  };
}

// pour chercher tous les id possibles de l'API
export async function getStaticPaths() {
  const posts = await fetch("http://ma/super/api/posts").then((r) => r.json());
  return {
    paths: posts.map((post) => ({
      params: { id: post.id.toString() },
    })),
    fallback: false,
  };
}
```

## Rendu SSR (côté serveur) avec `getServerSideProps()`

Dans ce cas, la page n'est pas générée au build, elle est générée côté serveur, à chaque fois que l'utilisateur requete cette page.
C'est utile si le contenu change fréquemment MAIS rajoute du délai et du travail côté serveur.

C'est le même délire que ci-dessus, mais avec `getServerSideProps()` au lieu de `getStaticProps()`, et pas besoin de `getStaticPaths()`.

Exemple pour le fichier [id].js

```jsx
import Link from "next/Link";

export default function Post({ post }) {
  return (
    <>
      <Link href="/">
        <a>Retour</a>
      </Link>
      <main>
        <h1>{post.title}</h1>
        <p>{post.body}</p>
      </main>
    </>
  );
}

// pour générer statiquement les pages
export async function getServerSideProps({ params }) {
  const posts = await fetch(`http://ma/super/api/posts/${params.id}`).then(
    (r) => r.json()
  );
  return {
    props: {
      post,
    },
  };
}
```

## Rendu hybride (= génération statique incrémentale) avec `getStaticProps()` avec un "revalidate"

On mélange les 2 approches : on génère les pages en avance en statique, mais côté serveur pas au moment du build, mais pas à chaque fois qu'un utilisateur lance une requête, mais tous les "x temps" à définir.

Pour cela, on va utiliser la méthode `getStaticProps()` comme pour le full statique, mais cette fois, avec un **"revalidate"**, pour lui indiquer de re-générer la page statique toutes les x secondes.

Concrètement, en vrai le serveur ne régénère pas vraiment toutes les pages à chaque fois.
En fait à chaque fois qu'un utilisateur demande une page, le serveur lui envoie directement la page demandée (statique), mais en plus, en tache de fond il check si la page est vieille (générée il y a plus longtemps que le temps indiqué dans le "revalidate"). Si c'est le cas, il rebuild la page, et le prochain visiteur aura la page mise à jour.

Exemple:

```jsx
import Head from "next/head";
import Link from "next/Link";

export default function Home({posts}) {
  return (
    <>
      <Head>
        <title>Mon super blog</title>
        <meta blabla />
      </Head>
      <ul>
        {posts.map(post => <li>
          <Link href={`/blog/${post.id}`}>
            <a>
              <h3>{{post.title}}</h3>
            </a>
          </Link>
        </li>)}
      </ul>
    </>
  )
}

export async function getStaticProps() {
  const posts = await fetch('http://ma/super/api/posts')
    .then(r => r.json())
  return {
    props: {
      posts
    },
    revalidate: 5,  // <-- ici ! (délai de validité: 5 secondes)
  }
}
```

## Liens entre pages

Comme vu précédemment

- La route de la page dépend de l'enplacement du fichier dans le dossier **`pages`**
- Pour faire un lien d'une page à l'autre du site, il faut utiliser le composant **`<Link>`** qui va entourer le lien:

```jsx
<Link href="/mon/url">
  <a className="link">Mon autre page</a>
</Link>
```

- les classes css doivent être ajoutées à l'élément `<a>` et non pas au composant `<Link>`
- Pour un lien externe au site, utiliser seulement l'élément `<a>`

> lorsqu'un lien est visible sur la page, il va commencer à télécharger les données de cette page, comme ça, ça sera hyper rapide pour l'utilisateur s'il clique sur le lien (si la connexion est suffisament bonne)

## Images

Next a un composant qui permet de faire le café pour les images ! Il s'occupe de:

- responsive (différentes tailles)
- optimisation (different formats webP)
- lazy loading

Il s'agit du composant `<Image>`

> Nb: mettre les fichier statiques dans le dossier public
> exemple pour les images : `public/images/logo.jpg`

```jsx
import Image from "next/image";

const YourComponent = () => (
  <Image
    src="/images/profile.jpg" // Route of the image file
    height={144} // Desired size with correct aspect ratio
    width={144} // Desired size with correct aspect ratio
    alt="Your Name"
  />
);
```

## Head et metadata

Pour modifier le titre de la page, les metadata, la description, enfin tout ce qui peut se trouver dans le head d'une page, on utilise le composant Next : `<Head>`

```jsx
<Head>
  <title>Mon super titre pour ma page</title>
  <link rel="icon" href="/favicon.ico" />
</Head>
```

> NB: il est possible de modifier la balise head en elle même (pour rajouter l'attribut lang par exemple), pour cela il faut créer une page `pages/_document.js`

Il est aussi possible de charger des scripts js d'autres librairies, comme on le ferait dans le `<head>` d'une page. Dans ce cas, on ne va pas utiliser le composant `<Head>`, mais le composant `<Stript>` qui est prévu pour et qui ne va pas détériorer les performances.

```jsx
<Script
  src="https://connect.facebook.net/en_US/sdk.js"
  strategy="lazyOnload"
  onLoad={() =>
    console.log(`script loaded correctly, window.FB has been populated`)
  }
/>
```

## Style

Next prend en charge directement la librairie de CSS-IN-JS "styled-jsx"

Pour cela, simplement rajouter l'élément `<style jsx></style>` dans le 'return' du composant.

```jsx
<style jsx>{`
  .maClasse {
    padding: 0 0.5rem;
    display: flex;
    flex-direction: column;
  }
  main {
    padding: 5rem 0;
    display: flex;
    flex-direction: column;
  }
`}</style>
```

Mais Next suporte aussi le css normal, le scss, et les **modules** css ou scss (= dans ce cas le css est "scopé" au composant qui l'appelle)

Pour utiliser sass, simplement installer:

```bash
npm install -D sass
```

> NB: il est possible de créer un composant **`components/Layout.js`**, ce dernier vas "wrapper" toutes les pages et servir de layout global.

> NB: il est possible de créer un fichier CSS de classes utilitaires `styles/utils/module.css` pour les classes utilitaires qui seront utilisées à plusieurs endroits du code.

### CSS Global

Déjà il faut créer un fichier css à importer : **`styles/global.css`** par exemple.

Puis on peut redéfinir le composant "de base" qui va contenir tous les autres ocmposants: **App**. Pour cela on crée le fichier **`pages/_app.js`** (redémearrer le serveur après).

Il est alors possible d'importer du CSS global dans ce fichier (et dans ce fichier uniquement!)

```jsx
import "../styles/global.css";

export default function App({ Component, pageProps }) {
  return <Component {...pageProps} />;
}
```

## Page 404

Il est possible de coder une page 404 custom en créant le fichier `pages/404.js`

```jsx
// pages/404.js
export default function Custom404() {
  return <h1>404 - Page Not Found</h1>;
}
```
