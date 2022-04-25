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

## Calls API

On va combiner les hooks `useState()` et `useEffect()` pour effectuer des calls API, il est également facile de créer un loader qui sera affiché pendant le chargement des données.

**Exemple:**

```jsx
function StuffsList() {
  const [stuffs, setStuffs] = useState({});
  const [isDataLoading, setDataLoading] = useState(false);

  useEffect(() => {
    setDataLoading(true);
    fetch(`http://localhost:8000/stuffs`)
      .then((response) => response.json())
      .then(({ stuffs }) => {
        setStuffs(stuffs);
        setDataLoading(false);
      });
  }, []);

  return (
    <div>
      <h1>Liste de trucs</h1>
      {isDataLoading ? (
        <Loader />
      ) : (
        <ul>
          {stuffs.map((stuff) => (
            <li>{stuff.title}</li>
          ))}
        </ul>
      )}
    </div>
  );
}

export default StuffsList;
```

## Le contexte

Le contexte permet de partager des props (le state), sans devoir les passer d'un composant parent, à son enfant, à son petit-enfant, etc.

[cf. l'article concernant 10 hooks de react, au chapitre concernant `useContext()`](https://tasy.fr/posts/react-10-hooks/)

## Créer ses propres hooks

Il est possible de créer ses propres hooks pour mettre en commun du code répétitif utilisé pas plusieurs composants.

**Exemple concret: hook pour un call API**

```jsx
// Dans un fichier à part:

export function useFetch(url) {
  const [data, setData] = useState({});
  const [isLoading, setLoading] = useState(true);
  const [error, setError] = useState(false);

  useEffect(() => {
    if (!url) return;
    setLoading(true);

    async function fetchData() {
      try {
        const response = await fetch(url);
        const data = await response.json();
        setData(data);
      } catch (err) {
        console.log(gerr);
        setError(true);
      } finally {
        setLoading(false);
      }
    }

    fetchData();
  }, [url]);

  return { isLoading, data, error };
}

// Dans un composant:
const { data, isLoading, error } = useFetch(`http://localhost:8000/trucmuche`);
```

## Tests unitaires avec Jest

**Jest** est installé par défaut avec create-react-app, sinon il faut l'installer.
Pour tester un composant, simplement créer un fichier **`MonComposant.test.js`**

**Exemple d'un test**

```jsx
// Fonction utilitaire testée:
export function add(a, b) {
  if (typeof a !== "number" || typeof b !== "number") null;
  return a + b;
}

// la fonction de test
import { add } from "./";

describe("The add function", () => {
  it("should add two numbers", () => {
    expect(add(3, 2)).toEqual(5);
  });

  it("should return null if one of the two arguments is not a number", () => {
    expect(add(2, "coucou")).toBeNull();
  });
});
```

Lancer le test avec

```bash
yarn run test
```

Pour calculer la couverture de code, lancer:

```bash
yarn test -- --coverage
```

## Test d'intagration avec React Testing Library

React Testing Library permet d'intéragir avec le dom, faire un render, etc.
Il faut installer cette librairie.

Exemple, on peut tester qu'un' composant est bien render sans bug:

```jsx
// dans le fichier de test
import Footer from "./"; // import du composant
import { render } from "@testing-library/react";
import { ThemeProvider } from "../../utils/context"; // si le contexte est utilisé par le composant, il ne faut pas oublier de l'importer aussi !

describe("Footer", () => {
  it("should render without crashing", async () => {
    render(
      <ThemeProvider>
        <Footer />
      </ThemeProvider>
    );
  });
});
```

On peut aussi par exemple tester les évènements:

```jsx
import { render, screen, fireEvent } from "@testing-library/react";
import { ThemeProvider } from "../../utils/context";
import Footer from "./";

it("should change theme", async () => {
  render(
    <ThemeProvider>
      <Footer />
    </ThemeProvider>
  );
  const nightModeButton = screen.getByRole("button");
  expect(nightModeButton.textContent).toBe("Changer de mode : ☀️");
  fireEvent.click(nightModeButton);
  expect(nightModeButton.textContent).toBe("Changer de mode : 🌙");
});
```

Pour plus d'infis, se référer à [la documentation de react tsting libraty](https://testing-library.com/docs/queries/bytestid/)

## Créer des mock pour nos tests avec MSW (mock service worker)

MSW permet de créer des mocks pour tester les composants plus facilement. Il fait d'abord l'installer avec `yarn add msw --dev`

cf. la [documentation de la librairie MSW](https://github.com/mswjs/msw), ainsi que [le chapitre dédié à MSW sur le cours React d'OpenClassrooms](https://openclassrooms.com/fr/courses/7150606-creez-une-application-react-complete/7257071-allez-plus-loin-dans-vos-tests) pour avoir plus d'infos.

## "Vieilles" syntaxes : composant classe

À la base react n'utilisait pas de hooks et pas de coposant fonction, mais tout était géré avec des composants classe. Cette syntaxe est de moins en moins utilisée, mais il faut savoir que ça existe. D'ailleurs, (à l'heure de l'écriture de cette note de cours..) le tuto principal de la documentation de react utilise principalement des composant classes. S'y référer si besoin.

Mais en 2 mots, les props sont passées en argument du constructeur pour ces composant classes.

## Bonnes pratiques en vrac

- De manière générale, c'est mieux de créer des composants les plus simples possibles. Toute la logique sera alors écrite à part dans des hooks customs
- Éviter de déclarer et utiliser un composant enfant dans un composant parent : sinon à chaque fois que le composant parent est re-render, le composant enfant doit l'être également, ce qui peut entrainer des pb de mémoire.
