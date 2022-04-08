---
title: "Hugo: Templates"
date: 2022-04-01T23:27:10+02:00
draft: true
categories: ["CMS"]
tags: ["CMS", "site statique", "open source", "GO"]
---

Il est possible de personnaliser le look & feel du site en utilisant un theme ou bien en codant des templates pour les single pages, les list pages, et la home page.

## List pages templates

Pour créer un template pour les pages de type "liste", il faut créer le fichier: `/template/_default/list.html`.

On peut ensuite accéder au contenu de `content/path/_index.md` et l'afficher dans le template avec la variable entre double accolades : `.Content`

On peut aussi, bien sûr, afficher toute la liste des articles du dossier avec la fonction qui fait office de boucle **for**, toujours entre double accolades : `range .Pages` puis la fermer avec `end` entre double accolades toujours.  
Entre ces balises fermantes et ouvrantes, on peut accéder aux variables de l'article avec, par exemple, la variable `.Title` (entre double accolades), ou encore la variable `.URL`

## Single pages templates

Pour créer un template pour les pages de type "single", il faut créer le fichier: `/template/_default/single.html`.

Pour le reste, tout fonctionne comme le template des "list pages" avec l'accès aux variables, etc.

## Home page template

Pour créer un template pour la page d'accueil du site, il faut créer le fichier: `/template/index.html`.

Pour le reste, tout fonctionne comme les autres templates.

## Section templates

Permet d'utiliser un autre template au niveau d'un sous-dossier en particulier.  
Il faut créer `list.html` et `single.html` dans un sous dossier du dossier `layouts` qui porte le même nom que le sous-dossier du dossier `content` qu'on veut personnaliser.

Exemple:  
Je veux utiliser un layout particulier pour tous les articles du sous dossier `content/mon-dossier/`, alors je vais créer un fichier `layout/mon-dossier/single.html`

## Base template & blocks

Permet de créer un "meta layout" qui englobe tous les autres layouts (single, list, home) qui seront alors une successions de plusieurs blocks.  
Créer le fichier `layouts/_default/baseof.html`  
Dans ce fichier il faut ouvrir la fonction entre double accolades `block "main" .`, puis la fermer avec `end`.

Puis dans les blocks (càd dans single.html par exemple), on va créer un nouveau bloc avec la fonction: `define "main"` et la fermer avec `end`. Ce qui est compris dans ce block "main" sera affiché entre les accolades du fichier `baseof.html`.

Il est possible de créer plusieurs blocks pour chaque template un pour le "head" par exemple qui permettre de personnaliser le title et les mete data.

Dans le fichier `baseof.html` entre le début du block et le "end", il est possible d'ajouter le texte affiché par défaut, si le block n'est pas redéfini dans les templates.

## Page 404

Créer le fichier `404.html` dans le dossier `layout`.  
Il focntionne comme tous les autres layouts! :)

## Variables

### Définitaion et usage

Ce sont des infos concernant les articles que l'on peut utiliser dans les templates.

Ces variables sont accessibles en les appelant entre doubles accolades, puis le nom de la variable avec une majuscule, précédé d'un point.

Si une variable n'est pas définie, rien ne s'affiche sur la page.

Toutes les variables disponibles sont répertoriées dans [la documentation de Hugo](https://gohugo.io/variables/), mais les plus communes sont les suivantes:

### Variables de pages

- `{{.Title}}`: le titre de l'article
- `{{.Date}}`: date de l'article
- `{{.URL}}`: url de l'article
- `{{.Params.myVariableName}}` : une variable custom "myVariableName" définie dans les front matters de l'article

### Création de variables au niveau du template

On peut créer des variables utilisées au niveau du template en les faisant précéder par un **$**, et en assignant avec **:=**. On peut lui donner une valeur numérique, boolean, ou string

- `{{$myNewVariableName := "une valeur"}}` : pour définir la variable
- `{{$myNewVariableName}}` : pour accéder à la variable et l'afficher

## Fonctions

### Définition et usage

Permet de faire des actions spécifiques, et peuvent être utilisées dans les layouts.

On appelle ces fonctions, comme d'habitude, entre double accolades, puis on met les parametres en suivant séparés par des espaces: `{{ funcName param1 param2 }}`

Toutes les fonctions sont répertoriées dans [la documentation de Hugo](https://gohugo.io/functions/)

### Exemple de fonctions:

- `{{ truncate 10 "super long string qui sera coupé à 10 caractères" }}`: tronque à n caractères et rajoute 3 petits points
- `{{ add 1 5 }}`: ajoute
- `{{ sub 6 1 }}`: soustraie
- `{{ singularize "trucs" }}`: met le mot au singulier
- `{{ range .Pages }} ... {{ end }}`: boucle sur toutes les .Pages => possible d'accéder aux variables de la page en cours entre les 2 balises (comme `{{ .Title }}` par exemple)

## Conditions

### Syntaxe

Il faut tapper le mot clé **if**, puis l'opérateur, puis la condition: `{{ if op cond }}`

### Quelques opérateurs

- **eq**: égal
- **ne**: not equal
- **lt**: lower than
- **le**: lower or equal than
- **gt**: greater than
- **ge**: greater or equal than
- **not (...)**: négation de ce qu'il y a entre parenthèses
- **isset**: le paramètre est défini
- d'autres opérateurs existent: voir [l'intro aux templates](https://gohugo.io/templates/introduction/) et [les fonctions](https://gohugo.io/functions/) de Hugo

### Exemples

**Exemple bidon:**

```go
{{ $var1 := 1 }}
{{ $var2 := 2 }}
{{ $var3 := "toto" }}

{{ if eq $var1 $var2 }}
  var1 est égar à var2
{{ else if lt $var1 $var2 }}
  var1 < à var2
{{ else if and (gt $var1 $var2) (eq $var3 "toto") }}
  var1 > var2 ET var3 = toto
{{ else }}
  toutes les autres conditions sont fausses
{{ end }}
```

**Liste toutes les pages du site, et mets une couleur particulière à l'item si c'est la page actuelle sur laquelle on est:**

```go
{{ $title := .Title }}

{{ range .Site.Pages }}
  <li>
    <a href="{{ .URL }}" style="{{ if eq .Title $title}}color:red;{{ end }}">
      {{ .Title }}
    </a>
  </li>
{{ end }}
```

## Les fichiers data

Ils sont situés dans le dossier **"Data"**, et font office de mini base de données. On peut y mettre des fichiers **json**, **yaml**, ou encore **toml**.

On peut accéder à ces données dans les templates avec `.Site.Data.fileName`

### Exemple

On peut créer un fichier `countries.json` dans le dossier `data`, dans lequel on va répertorier des infos sur tous les pays :

```json
{
  "france": {
    "language": "french",
    "flag": "blue white red"
  },
  "spain": {
    "language": "spanish",
    "flag": "red yellow"
  }
}
```

Pour les lister toutes les langues dans le template, on va faire :

```go
{{ range .Site.Data.countries }}
  <li>{{ .language }}</li>
{{ end }}
```

## Partials

Permet d'injecter un "bout" de html dans un autre template.

Dans le dossier `layout` on va créer un nouveau sous-dossier `partials` puis créer le partial que l'on veut en créant un fichier html, par exemple: `header.html`.

On écrit le html que l'on veut dans le partial, puis dans un autre templte, on peut injecter ce partial avec :

```go
{{ partial "header" . }}
```

Le point représente le scope dont aura accès le partial. Dans ce cas, on lui passe le scope actuel. On aurait pu lui passer le scope d'une page en particulier, par exemple.

Plutôt que le scope, on peut lui passer un dictionnaire, en précisant quelles variables exactement seront passées au partial. Exemple pour passer le titre et la date d'un article seulement:

```go
{{ partial "header" (dict "myTitle" .Title "myDate" .Date) }}
```

Dans le partial, on récupère ces propriétés avec `{{.myTitle}}` et `{{.myDate}}`

> NB: l'exemple du header est un peu nul, car il vaut mieux les définir avec les **"base templates"**.  
> Il vaut mieux imaginer les partials comme des "composants" réutilisables. On pourrait alors avoir un partial 'card' par exemple.

## Shortcodes

C'est des petits bouts de code qu'on peu ajouter à notre contenu dans les fichiers markdown pour faire des actions particulières.  
En gros, c'est des partials, mais pour le content et non pas pour le layout.

Il faut créer le sous-dossier `shortcodes` dans le dossier `layouts`, puis créer un fichier html. Par exemple `myShortcode.html`, à l'intérieur on y met le html qu'on veut.

### Shortcode à tag simple

Ensuite dans l'un de nos article, on appelle ce shortcode avec:

```go
{{ < myShortcode > }} // nb: enlever les espaces entre les accolades et les < > !!
```

On peut aussi lui passer des variables, par exemple:

```go
{{ < myShortcode color="blue"; > }} // nb: enlever les espaces entre les accolades et les < > !!
```

Dans le shortcode, on récupère la variable avec `{{ .Get "varName" }}`. Exemple:

```go
<h1 style="color:{{.Get `color`}};">Mon titre coloré</h1>
// utiliser le backtick autour de color pour ne pas avoir de conflit avec les guillemets
```

On peut aussi passer un paramètre "positionnel". En reprenant l'exemple ci-dessus:  
Dans le content (fichier \*.md):

```go
{{ < myShortcode blue > }} // nb: enlever les espaces entre les accolades et les < > !!
```

Et dans le shortcode, on lui dit de prendre le paramètre 0:

```go
<h1 style="color:{{.Get 0}};">Mon titre coloré</h1>
// utiliser le backtick autour de color pour ne pas avoir de conflit avec les guillemets
```

### Shortcodes à double tags

Dans ce cas, dans template, on l'appelle avec :

```go
{{ < myShortcode > }}
  texte entre les balises
{{ < /myShortcode > }}
// nb: enlever les espaces entre les accolades et les < > !!
```

Puis dans notre shortcode myShortcode.html, on peut accéder à ce qu'il y a entre les balises avec:

```go
{{ .Inner }}
```

> NB: si on met du markdown entre ces balises il ne sera pas interprété. Pour qu'il soit interprété, remplacer les **< >** par des **%**

### Cas pratique
Une utilisation très pratique de ces shortcode, serait de décliner les images en images adaptatives avec les balises `<pictures>` et `<source>` en passant l'url ou la filename de l'image en paramètre de shortcode.

## Build & host !

Pour build, rien de plus simple:

```bash
hugo
```

Le dossier `public` apparait avec tous les fichier html générés.

Il suffit de copier ce dosiser sur un serveur web! :)
