---
title: "Introduction à React"
date: 2022-04-11T12:11:05+02:00
draft: true
tags: ["react", "javascript", "framework"]
description: "Les concepts de base de la librairie javascript react"
---

## Définition

React est une **librairie javascript** qui permet de créer des **composants réutilisables**. Il a été créé par Facebook, et est actuellement (2022) le framework client le plus utilisé.

Se cours se focalisera sur les **"composants fonctions"** (et non pas les "composants classes") ainsi que sur l'utilisation des **hooks**.

## Création et utilisation d'un composant

Il faut essayer de découper l'interface en composants suffisament "petits" pour pouvoir être réutilisés dans le code.

Dans un fichier \*.jsx, il suffit de créer une fonction qui retourne du HTML. Un composant ne peut retourner qu'1 seule balise parente, mais on peut créer des balises "vides" : `<> et </>`

```jsx
function MyComponent() {
  return (
    <>
      <h1>Hey dude!</h1>
      <h2>Ca va ?</h2>
    </>
  );
}
export default MyComponent; //penser à exporter ce composant !
```

Puis, on peut utiliser ce composant créé dans un autre composant parent:

```jsx
function MyParentComponent() {
  <header>
    <MyComponent />
    <MyComponent2 />
    <h3>bien ou bien</h3>
  </header>;
}
```

Il est possible d'exécuter/afficher du javascript dans le jsx en l'entourant **d'accolades** :

```jsx
function Description() {
  const text = "Ici achetez toutes les plantes dont vous avez toujours rêvées";
  const emojis = "🤑🤑🤑";
  return <p>{text.slice(0, 11) + emojis}</p>;
}
```

## Create React App

Create-react-app est un outil qui permet de créer une structure de départ d'application react en une seule ligne de commande, et pré-configure des outils (Webpack, Babel, ESLint, Jest, etc.)

```bash
# appli JS:
npx create-react-app mon-appli

# appli TS:
npx create-react-app mon-appli --template typescript

# lancer le serveur
yarn start

# lancer un build
yarn build

# lancer les tests
yarn test
```

### Organisation des dossiers

- `public`: tous les éléments statiques (logo, favicon, manifest, par exemple)
- `src`: le code en lui même
  - `index.jsx`est le point d'entrée
  - `App.jsx` est le premier composant

L'organisation du reste du code est à la décision du développeur.

## Ajouter du style et des images

### Fichier CSS/SCSS

- installer sass : `npm install sass` ou `yarn add sass`
- créer le fichier de style : `MonComposant.module.scss` ("module" pour scoper le style au composant)
- y rajouter du style : `.ma-classe { color: red; }`
- créer le fichier composant: `MonComposant.jsx`
- créer un composant qui utilise cette classe avec `className` (et non pas "class", attention!), sans oublier d'importer le fichier scss

```jsx
import ".MonComposant.module.scss";

function MonComposant() {
  return <div className="ma-classe">coucou</div>;
}
```

### Attribut style

Il est possible d'utiliser l'attribut style, mais on lui passe un objet.

```jsx
function MonComposant() {
  return (
    <div style={
      "color": "red",
      "text-align": "center"
    }>
      coucou
    </div>
    );
}
```

### Images statiques

Pour ajouter une image à un composant, il suffit de l'importer, puis d'utiliser la variable d'import au niveau de l'attribut src de l'image

```jsx
import logo from "../assets/logo.png";

function MonComposant() {
  return (
    <header>
      <img src={logo} alt="mon super logo" />
    </header>
  );
}
```

## Listes et conditions

### Boucles

Les méthodes d'array sont très utilisées en React (map(), filter(), reduce(), forEach(), etc.), car leur retour est directement affiché en sortie du jsx.

> Attention: pour chaque item listé, il faut absolument penser à déterminer une **`key`** qui est un identifiant unique de l'objet (= souvent son id dans la base de donnée, ou autre chose unique si l'id n'est pas dispo)

```jsx
myStuffs = ["a", "b", "c", "d"];

function StuffList() {
  return (
    <ul>
      {myStuffs.map((stuff, index) => (
        <li key={`${stuff}-${index}`}>{stuff}</li>
      ))}
    </ul>
  );
}
export default StuffList;
```

### Conditions

On utilise principalement **l'oprateur ternaire** pour créer des conditions d'affichage dans le jsx:

```jsx
myStuffs = ["a", "b", "c", "d"];
function StuffList() {
  return (
    <ul>
      {myStuffs.map((stuff, index) => (
        <li key={`${stuff}-${index}`}>
          {stuff}
          {stuf === "b" ? <span>🔥</span> : <span>👎</span>}
        </li>
      ))}
    </ul>
  );
}
```

Il est aussi commun de voir l'opérateur && utilisé pour afficher conditionnellement qqch dans jsx:

```jsx
{
  stuff.isBestSale && <span>🔥</span>;
}
// si le booléen isBestSale est vrai, alors la flamme est affichée.
```

## Les props (propriétés)

```jsx
// dans le composant parent
<CareScale scaleValue={plant.light} />;

// dans le composant enfant
function CareScale(props) {
  const scaleValue = props.scaleValue; // OBLIGATOIREMENT passer par une constante. Pas possible de modifier directement les props
  return <div>{scaleValue}☀️</div>;
}
```

De manière générale, **la déstructuration** est très largement utilisé dans react:

```jsx
// dans le composant parent
<CareScale careType="water" scaleValue={plant.water} />;
// NB: guillemet pour les props de type string, accolades pour tout le reste

// dans le composant enfant
function CareScale({ scaleValue, careType }) {
  return (
    <div>
      {careType}: {scaleValue}☀️
    </div>
  );
}
```

### Cas particulier de la prop "children"

La prop "children" correspond au contenu entre les tags ouvrants et fermants du composant:

```jsx
// dans le composant parent:
<Banner>
  <img src={logo} alt="La maison jungle" />
  <h1 className="lmj-title">La maison jungle</h1>
</Banner>;

// dans le composant enfant:
function Banner({ children }) {
  return (
    <header>
      <nav>menu de navigation</nav>
      {children}
    </header>
  );
}
//=> dans ce cas, le img et le h1 seront placés après le menu de navigation
```

## Les évènements

### Evenements simples

- Dans le composant, déclarer l'évènement et mettre la fonction à appeler entre accolades:

```jsx
function MonComposant() {
  function handleClick() {
    console.log("click!");
  }

  return <button onClick={handleClick}>clique moi !</button>;
}

// par défaut react passe au handeler un objet "event" en paramètre (similaire aux event du DOM, mais mieux compatibles cross-browsers)

// Si on veut passer au handeler des propriétés custom, on fait:
function MonComposant() {
  function handleClick(monParam) {
    console.log("Le parametre passé est: " + monPAram);
  }

  return <button onClick={() => handleClick(monParam)}>clique moi !</button>;
}
```

### Formulaires contrôlés

On utilise le state pour faire un binding entre state et valeur affichée de l'input:

```jsx
import { useState } from "react";

function QuestionForm() {
  const [inputValue, setInputValue] = useState("Posez votre question ici");
  return (
    <div>
      <textarea
        value={inputValue}
        onChange={(e) => setInputValue(e.target.value)}
      />
    </div>
  );
}
export default QuestionForm;
```

## Le hook useState()

### Définition

Le "state" correspond en fait au jeu de données qui est utilisé par le composant et qui est conservé même si le composant est re-render.

Le state ne peur pas être modifié directement, le state peut être consdéré comme une constante dans la fonction, il faut passer par le **hook** `useState()`.

### Syntaxe

```jsx
const [truc, setTruc] = useState(valInitiale);
// truc : la variable du state
// setTruc : la focntion qui permet de changer le state : on lui passe en param la nouvelle valeur de la variable du state
// valInitiale : la valeur initiale de la variable du state
```

On utilise les crochets car on utilise la déstructuration (ou "décomposition" pour les array).. on aurait très bien pu faire:

```jsx
const trucArray = useState(valInitiale);
const truc = trucArray[0];
const setTruc = trucArray[1];
// => ça marche mais c'est moche !
```

On peut utiliser plusieurs `useState()` dans un composant, mais ils ne doivent pas être "nesté" dans des sous-fonctions du composant.

### Exemple

```jsx
function Cart() {
  const itemPrice = 8;
  const [cart, updateCart] = useState(0);
  const [isOpen, setIsOpen] = useState(false);

  return isOpen ? (
    <div className="lmj-cart">
      <button onClick={() => setIsOpen(false)}>Fermer</button>
      <h2>Panier</h2>
      <div>
        Monstera : {itemPrice}€
        <button onClick={() => updateCart(cart + 1)}>Ajouter</button>
      </div>
      <h3>Total : {itemPrice * cart}€</h3>
    </div>
  ) : (
    <button onClick={() => setIsOpen(true)}>Ouvrir le Panier</button>
  );
}
```

### "Faire remonter" le state

Dans ce cas, le `useState()` sera défini dans un composant parent, le state sera passé au composant enfant dans les props, ainsi que la fonction d'update.  
Cela permet de "partager" le state entre différents composants enfants.

```jsx
function ParentComponent() {
  const [truc, updateTruc] = useState("truc initial");

  return (
    <div>
      <ChildComponent1 truc={truc} updateTruc={updateTruc} />
      <ChildComponent2 truc={truc} updateTruc={updateTruc} />
    </div>
  );
}

function ChildComponent1({ truc, updateTruc }) {
  return (
    <div>
      <p>mon truc: {truc}</p>
      <button onClick={() => updateTruc("autre truc")}>change truc 1</button>
    </div>
  );
}

function ChildComponent2({ truc, updateTruc }) {
  return (
    <div>
      <p>mon truc: {truc}</p>
      <button onClick={() => updateTruc("encore un autre truc")}>
        change truc 2
      </button>
    </div>
  );
}
```

## Le Hook useEffect()

### Définition

Permet d'exécuter une fonction

- soit à chaque fois que le composant est re-render (= à chaque fois que le state change)
- soit lorsque une/des variable(s) spécifique(s) du state change
- soit une fois au premier render

### Syntaxe

```jsx
useEffect(callback, []);

// 1er param obligatoire: la fonction qui sera exécutée
// 2eme param optionnel: le tableau de dépendance
```

### Règles du tableau de dépendance

- `useEffect(callback)` : si aucun 2eme paramètre n'est passé, le callback est appelé à chaque fois que le composant est render
- `useEffect(callback, [toto, tutu])` : si un tableau de dépendance est passé en 2eme argument, le callback ne sera appelé que si les variables spécifiées (toto et tutu dans l'exemple..) sont modifiées avec useState()
- `useEffect(callback, [])` : si un tableau vide est passé, le callback sera exécuté seulement un premier render du composant.

### callback à la suppression du composant

Il est aussi possible d'exécuter une fonction au "démontage" du composant. Pour cela, le callback doit **return** une autre fonction qui sera exécutée juste avant la suppression du composant du DOM :

```jsx
useEffect(() => {
  console.log("affiché à chaque fois que le state change");

  return () => {
    console.log("affiché au démontage du composant");
  };
});
```
