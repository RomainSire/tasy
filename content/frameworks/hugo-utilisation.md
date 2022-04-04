---
title: "Hugo CMS - utilisation"
date: 2022-04-01T21:55:44+02:00
draft: true
---

Hugo est un générateur de site statique (= rapide et bon SEO), open soucre et codé en GO.

## Installation sur linux et commandes de base

```bash
# Installer hugo à partir des repos (! pas la dernière version !)
sudo apt install hugo

# Créer un nouveau projet
hugo new site le-nom-de-mon-projet

# aller dans le dossier du projet
cd le-nom-de-mon-projet

# créer un article
hugo new mon-premier-article.md

# créer un article dans un sous dossier
hugo new mon-dossier/mon-sous-dossier/mon-article.md

# lancer le site sur localhost:1313
hugo server

# lancer le site sur localhost:1313, y compris les articles "draft"
hugo server -D
```

## Organisation des dossiers

- **archetypes**: détermine quelles "front matter" (=metadata) seront présentes par défaut dans les posts
- **content**: ici sont regroupés les articles, le contenu
- **data**: fait office de base de données, où on peut mettre des fichiers json par exemple.
- **layouts**: ici on peut créer des templates (html, JS) pour nos pages
- **resources**: utilisé de manière interne par Hugo, pour service de cache lors de la génération du site
- **static**: ressources statiques (images, css, etc.)
- **theme**: si on choisit d'utiliser un theme, il va aller là !
- **config.toml** : le fichier de config du site

## Installer un theme

Chaque thème est différent, il faut suivre les instructions sur le github du thème qu'on a choisit sur [la page des thèmes officiels de Hugo](https://themes.gohugo.io/)

## Organisation du contenu

### URL

Les dossiers/sous-dossiers à l'intérieur de **content** correspondent à l'url de l'article:  
Par exemple, si je crée un article dans le dossier `dir1/articel1.md`, alors par défaut son url sera `localhost:1313/dir1/articel1`

### Types de pages

Il y a 2 grands types de contenus dans Hugo:

- **single pages** : un article/page du site
- **list pages** : permet de lister les articles/pages du site

### Création de "list pages"

- dossier "content" et dossiers enfants de 1er niveau:
  - **list page** générée automatiquement
  - possible d'ajouter un contenu (texte, image, etc.) à ces listes en ajoutant un fichier **\_index.md** à l'endroit voulu, et en ajoutant un contenu à ce fichier => overwrite la "list page" par défaut (puis l'afifchage sera customisé par le theme / les layouts!)
- sous-dossiers de niveau plus "bas"
  - pas de list page automatique
  - possibilité de forcer la génération d'une "list page" en créant un fichier "\_index.md" à l'entroit voulu

## Front matter

Ce sont les meta-datas en haut d'un fichier de post. Par défaut : "title", "date", et "draft", mais il est possible d'en rajouter autant qu'on veut.
Il est possible de récupérer ces **front matter** dans les layouts.

## Archetypes
Ils sont définis dans le dossier "archetypes".
**default.md** à la racine permet de fdéfinir les front matter présents par défaut lorsqu'on crée un nouveau post.
