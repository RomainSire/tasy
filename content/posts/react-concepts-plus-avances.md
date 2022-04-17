---
title: "Une app complète avec React"
date: 2022-04-17T16:58:04+02:00
tags: ["react", "javascript", "framework", "bonnes pratiques"]
description: "Explication de quelques concepts plus avancés de React dans le but de créer une app complète"
---

## Organisation du code

React et create-react-app n'imposent pas de structure, mais il vaut mieux choisir une bonne structure et s'y tenir.

Deux bons exemples de structures sont:

- celle imposée par **Next.js** : un dossier pour les pages, un autre pour le style, un autre pour les petits composants.
- celle **d'Angular** : chaque composant a son propre dossier, dans lequel sont mis TOUT ce qui a trait au composant : le JS, le style (scopé), les tests, le storybook, etc.

> NB: Toujours dans l'objectif d'harmoniser son code, c'est une bonne idée d'utiliser ESLint et Prettier pour produire du code uniforme entre les différents fichiers.
> Créer les fichiers `.eslintrc` et `.prettierrc` pour customiser les règles à appliquer pour le projet. Installer éganelment les 2 extension VSCode, et le "format on save".

## Le router

Une application nécessite un bon router. Pour les app "nues" (= créées avec react, ou create-react-app) la librairie `React Router` est une bonne option.

Un router est directement inclus avec **Next.js** : la structure des routes correspond à la structure des fichiers dans le dossier `pages` (= similaire à SvelteKit)

> NB: Ne pas oubliler de créer une page 404 custom! ;)

## Le typage

Il existe 2 options.

- Typescript : voir la doc de **create-react-app** ou **Next.js** pour voir comment setup un projet avec Typescript. TS a pour but de détecter les erreurs de typages AVANT l'exécution, directement dans l'éditeur.

- Proptype : ça a un peu le même but, mais l'erreur apparaitra à l'exécution, et permet seulement de typer les props. C'est en quelque sorte un TS light et moins performant ! C'est bien de savoir que ça existe, mais c'est mieux d'utiliser TS !

## Le style: plein d'options !

### 1. CSS global

Mettre ses fichiers css globalement, pas scopé => ok pour petits projets seulement !

### 2. CSS modules = scoper au composant

Option par défaut avec React: nommer les fichier css en `blablabla.module.css`, ce fichier est autimatiquement scopé uniquement au composant qui l'appelle.

```jsx
import styles from "../styles/MonComposant.module.css";

export default function MonComposant() {
  return <div className={styles.maclasse}>coucou</div>;
}
```

NB: il est possible de "partager" du code CSS entre plusieurs modules en utilisant **"composes"** dans le css, ce qui permet de "charger" et "écraser" du css d'un autre fichier:

```css
.page {
  color: purple;
  composes: className from "./shared.css";
}
```

### 3. SASS !

Easy peasy, installer: **`npm install sass`**, et voila! renommer les fichiers css par `MonCoposant.module.scss` et ça marche tout seul ! :)

### 4. CSS-in-JS

Dans le JSX, le HTML est déjà complètement géré par le JS, donc autant faire gérer le CSS également par le JS !  
Perso, je préfère le css traditionnel, mais ça peut avoir des avantages.

Dans ce cas, la libraisie "styled components" est une bonne option.

> Spécifiquement pour Next.js, il y a la libraisie **"styled jsx"** qui peut mériter le détour : il suffit d'uvrir une balise **style** dans le composant à laquelle on ajoute l'attribut **jsx**. Puis on écrit le style comme un string avec des backticks. Ce qui permet d'utiliser des variable (du state notamment) dans le style ave cla notation **`color: ${color}`**.

Ces styles sont également scopés.

### 5. librairies de classes utilitaires

En gros Tailwind css. Là encore, c'est pas trop mon délire.

### 6. librairies css

Là c'est Bootstrap : même combat, je ne suis pas fan !

### 7.librairie de composants

Exemple: React bootstrap, ou Mantine. Ce sont des composants déjà codés qu'on peut utiliser.

Ça peut être utile quand on n'a pas d'inspi !
