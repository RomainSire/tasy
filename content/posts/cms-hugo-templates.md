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

## Variables

Ce sont des infos concernant les articles que l'on peut utiliser dans les templates.
