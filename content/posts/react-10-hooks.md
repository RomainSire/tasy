---
title: "10 hooks de React expliqués"
date: 2022-04-15T12:47:26+02:00
tags: ["react", "javascript", "framework", "hooks"]
description: "Comment fonctionnent les 10 hooks les plus communs de React"
---

## Introduction

Le "state" correspond en fait au jeu de données qui est utilisé par le composant et qui est conservé même si le composant est re-render.

Initialement les composants React étaient des classes. Cette approche est toujours possible, mais maintenant il est conseillé d'utiliser des composant sous forme de Fonction.

Le state était étroitement lié à la classe du composant et de nombreuses features étaient liées au type "classe" des composants. Les hooks permettent d'accéder à ces features sur des composants de type fonction, et souvent plus simplement qu'avant.

Un hook n'est rien de plus qu'une **fonction** dont le nom commence par **use**: `useSuperPower()` et permet de faire un action spécifique.

10 hooks sont fournis par défaut par React, et il est possible d'en créer des customs. PAr conventions, on nommera ces hooks customs avec un nom commençant par **use**.

> Il y a un règle importante à garder en tête lorsqu'on utilise des hooks : toujours les appeler au plus haut niveau d'une fonction composant, jamais "nesté" dans une sous-fonction ou un boucle ou autre.  
> Exception: les hooks custom, qui dépendent forcment de la manière dont on a codé le hook!

## 1. useState()

### Définition

Le state ne peut pas être modifié directement, le state peut être consdéré comme une constante dans la fonction, il faut passer par le **hook** `useState()`.

useState() permet de changer le state.

> NB: lorsque le state change, le composant est re-render

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

## 2.useEffect()

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

## 3. useContext()

### Définition

Permet d'utiliser **l'API contexte** de React, qui permet de partager des props, sans devoir les passer d'un composant parent, à son enfant, à son petit-enfant, etc.

### Exemple

```jsx
const moods = {
  happy: ":)",
  sad: ":(",
};

const MoodContext = createContext(moods);

function App() {
  return (
    <MoodContext.Provider value={moods.happy}>
      <MoodEmoji />
    </MoodContext.Provider>
  );
}

function MoodEmoji() {
  const mood = useContext(MoodContext); // utilise la valeur donnée par le provider parent le plus proche

  return <p>{mood}</p>;
}
```

Si la valeur change dans le provider parent, alors le composant enfant utilisant ce context sera mis à jour automatiquement.

### Exemple plus réaliste: dark mode qui ne change que le Main du site

```jsx
// fichier ThemeProvider.jsx
export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState("light");
  const toggleTheme = () => {
    setTheme(theme === "light" ? "dark" : "light");
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

// fichier App.jsx : on englobe tous les composant du site dans ce provider
function App() {
  return (
    <ThemeProvider>
      <Header />
      <Main>
        <Composant1 />
        <Composant2 />
        <Composant3 />
        <LightSwitch />
      </Main>
    </ThemeProvider>
  );
}

// Dans le composant Main on va récupérer le theme et afficher la bonne classe selon qu'il est "light" ou "dark"
import { ThemeContext } from "../context/ThemeProvider";
function Main() {
  const { theme } = useContext(ThemeContext);

  return (
    <main style={theme === "dark" ? "background-color: black;" : ""}>
      {children}
    </main>
  );
}

// Puis dans le composant LightSwitch
function LightSwitch() {
  const { toggleTheme, theme } = useContext(ThemeContext);
  return (
    <button onClick={() => toggleTheme()}>
      Changer de mode : {theme === "light" ? "☀️" : "🌙"}
    </button>
  );
}
```

## 4. useRef()

Permet de créer un objet mutable qui va garder la même référence entre les renders, mais qui ne provoque pas un re-render de la page lorsque sa valeur change.

La valeur actuelle est accessible avec `maVariable.current`

```jsx
function App() {
  // Dans ce cas, la valeur est bien mise à jour à chaque click sur le bouton
  // Mais elle n'est jamais affichée (il y a toujours "0" dans le bouton) car le changement de valeur n'implique pas un re-render du composant
  const count = useRef(0);

  return (
    <button onClick={() => count.current++}>
      Ma valeur:
      {count.current}
    </button>
  );
}
```

En pratique, ce hook est souvent utilisé pour récupérer un élement html du DOM grace à l'attribut `ref` dans le html:

```jsx
function App() {
  const myBtn = useRef(null);
  const clickIt = () => myBtn.current.click();
  return <button ref={myBtn}>mon bouton</button>;
}

// dans ce cas, quand la fonction clickIt() est appelé (par une autre méthode), alors le bouton est cliqué.
```

## 5. useReducer()

C'est une autre manière d'utiliser le state : plutôt que de mettre à jour le state directement, on envoie une action qui arrive à une fonction "reducer" qui détermine comment modifier le state.

```jsx
function reducer(state, action) { // prend le state et l'action (= ce qu'il y a dans le dispach de l'element html)
  switch (action.type) {
    case 'increment':
      return state + 1;
    case 'decrement':
      return state - 1;
    default
      throw new Error();
  }
}

fucntion App() {
  const [state, dispach] = useReducer(reducer, 0); // prend le state, et la valeur initiale en param
  return (
    <>
      Count: {state}
      <button onClick={() => dispach({type: 'decrement'})}>-</button>
      <button onClick={() => dispach({type: 'increment'})}>+</button>
    </>
  )
}
```

## 6. useMemo()

Améliore les perfs : permet de mettre en cache certaines valeurs sans recalculer à chaque fois que le composent re-render.

> NB: utiliser avec parcimonie

```jsx
function App() {
  const [count, setCount] = useState(60);

  const expensiveCount = useMemo(() => {
    return count ** 2;
  }, [count]); // 2ème argument = tableau de dépendances
  // "expensiveCount" est recalculé seulement lorsque "count" change

  return <></>;
}
```

## 7. useCallback()

c'est un `useMemo()` mais pour une fonction entière

Ca peut être utile lorsqu'on passe une fonction à d'autres composants enfants, si on utilise `useCallback()` on peut éviter des re-render inutiles de composants enfants.

```jsx
function App() {
  const [count, setCount] = useState(60);

  const showCount = useCallback(() => {
    alert(`Count: ${count}`);
  }, [count]);

  return (
    <>
      <ComposantEnfant handler={showCount} />
    </>
  );
}
```

## 8. useImperativeHandle()

Peut être utile lorsqu'on construit une librairie de composants réutilisables : focntionne de pair avec le hook `useRef()`.

Dans ce cas, `useImperativeHandle()` permet de modifier le comportement du `ref` exposé.

Ce hook est quand même rarement utile..!

## 9. useLayoutEffect()

Fonctionne un peu comme `useEffect()`, mais le moment d'exécution du callback difère: le callback sera lancé après avoir "render" le composant, mais avant que le "paint" ait été affiché à l'écran.

CAD que React va attendre que le code ait fini de s'exécuter pour mettre à jour l'UI pour l'utilisateur.

Peut être utile, par exemple, lorsqu'on doit calculer la position d'un élément visuel dans le callback..

## 10. useDebugValue()

N'est utile que si l'on crée ses propres hooks. Et il faut aussi avoir **"React dev tool"** installé sur son navigateur.

Dans React devtool, chaque composant dans l'arbre donne des infos sur les hooks utilisés.

`useDebugValue()` permet de définir ce qui sera affiché ici pour ses hooks customs.

## 11. Bonus: Créer un hook custom (et utiliser useDebugValue..)

```jsx
function useDisplayName() {
  const [displayName, setDisplayName] = useState();

  useEffect(() => {
    const data = fetchFromDatabase(props.userId);
    setDisplayName(data.displayName);
  }, []);

  useDebugValue(displayName ?? "loading..."); // affiche displayName ou "loading" dans react devtool

  return displayName;
}

function App() {
  const displayName = useDisplayName();
  return <button>{displayName}</button>;
}
```
