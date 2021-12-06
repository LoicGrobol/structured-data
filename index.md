---
title: Données structurées — M2 PluriTAL 2021
---

[comment]: <> "LTeX: language=fr"

## Infos pratiques

- **Quoi** « Données structurées »
- **Où** Salle 219, bâtiment Paul Ricœur
- **Quand** 8 séances, les lundi de 13:30 à 16:30, du 29/11 au 31/01
  - Voir [le
    planning](http://www.tal.univ-paris3.fr/plurital/admin/Calendrier_M2_TAL_PX_2021_22.xlsx) pour
    les dates exactes
- **Contact** Loïc Grobol [<loic.grobol@parisnanterre.fr>](mailto:loic.grobol@parisnanterre.fr)

## Séances

Tous les supports sont sur [github](https://github.com/loicgrobol/structured-data), voir
[Utilisation en local](#utilisation-en-local) pour les utiliser sur votre machine comme des
notebooks. À défaut, ce sont des fichiers Markdown assez standards, qui devraient se visualiser
correctement sur la plupart des plateformes (mais ne seront pas dynamiques).

Les slides et les notebooks ci-dessous ont tous des liens Binder pour une utilisation interactive
sans rien installer. Les slides ont aussi des liens vers une version HTML statique utile si Binder
est indisponible.

### 2021-11-20 — Graphes et algorithmes d'exploration

- [Slides Graphes et algorithmes
  d'exploration](slides/01-graphs_and_traversing_algorithms/graphs_and_search_algorithms-slides.py.html)
  [![Launch in Binder
  badge](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/loicgrobol/structured-data/main?urlpath=tree/slides/01-graphs_and_traversing_algorithms/graphs_and_search_algorithms-slides.py.md)

### 2021-12-01 — Graphes particuliers et réseaux lexicaux

- [Slides Graphes et algorithmes
  d'exploration](slides/01-graphs_and_traversing_algorithms/graphs_and_search_algorithms-slides.py.html)
  [![Launch in Binder
  badge](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/loicgrobol/structured-data/main?urlpath=tree/slides/01-graphs_and_traversing_algorithms/graphs_and_search_algorithms-slides.py.md)
- [Slides Réseaux lexicaux](slides/02-wordnet/wordnet.py.html)
  [![Launch in Binder
  badge](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/loicgrobol/structured-data/main?urlpath=tree/slides/02-wordnet/wordnet.py.md)

### 2021-12-06 — Graphes aléatoires

- [Slides Graphes aléatoires](slides/03-random_graphs/random_graphs.py.html)
  [![Launch in Binder
  badge](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/loicgrobol/structured-data/main?urlpath=tree/slides/03-random_graphs/random_graphs.py.md)

## Lire les slides en local

Les supports de ce cours sont écrits en Markdown, convertis en notebooks avec
[Jupytext](https://github.com/mwouts/jupytext). C'est entre autres une façon d'avoir un historique
git propre, malheureusement ça signifie que pour les ouvrir en local, il faut installer les
extensions adéquates. Le plus simple est le suivant

1. Récupérez le dossier du cours, soit en téléchargeant et décompressant
   [l'archive](https://github.com/LoicGrobol/structured-data/archive/refs/heads/main.zip)
   soit en le clonant avec git : `git clone
   https://github.com/LoicGrobol/tructured-data.git` et placez-vous dans ce dossier.
2. Créez un environnement virtuel pour le cours

   ```console
   python3 -m virtualenv .venv
   source .venv/bin/activate
   ```

3. Installez les dépendances

   ```console
   pip install -U -r requirements.txt
   ```

4. Lancez Jupyter

   ```console
   jupyter notebook
   ```

   JupyterLab est aussi utilisable, mais la fonctionnalité slide n'y fonctionne pas pour l'instant.

## Ressources

### Python général

Il y a beaucoup, beaucoup de ressources disponibles pour apprendre Python. Ce qui suit n'est qu'une sélection.

#### Livres

- How to think like a computer scientist, by Jeffrey Elkner, Allen B. Downey, and Chris Meyers. Vous
  pouvez l'acheter. Vous pouvez aussi le lire
  [ici](http://openbookproject.net/thinkcs/python/english3e/)
- Dive into Python, by Mark Pilgrim. [Ici](http://www.diveintopython3.net/) vous pouvez le lire ou
  télécharger le pdf.
- Learning Python, by Mark Lutz.
- Beginning Python, by Magnus Lie Hetland.
- Python Algorithms: Mastering Basic Algorithms in the Python Language, by Magnus Lie Hetland.
  Peut-être un peu costaud pour des débutants.
- Programmation Efficace. Les 128 Algorithmes Qu'Il Faut Avoir Compris et Codés en Python au Cours
  de sa Vie, by Christoph Dürr and Jill-Jênn Vie. Si le cours vous paraît trop facile. Le code
  Python est clair, les difficultés sont commentées. Les algos sont très costauds.

#### Web

Il vous est vivement conseillé d'utiliser un (ou plus) des sites et tutoriels ci-dessous.

- [Real Python](https://realpython.com), des cours et des tutoriels souvent de très bonne qualité et
  pour tous niveaux.
- [Coding Game](https://www.codingame.com/home). Vous le retrouverez dans les exercices
  hebdomadaires.
- [Code Academy](https://www.codecademy.com/fr/learn/python)
- [newcoder.io](http://newcoder.io/). Des projets commentés, commencer par 'Data Visualization'
- [Google's Python Class](https://developers.google.com/edu/python/). Guido a travaillé chez eux.
  Pas [ce
  Guido](http://vignette2.wikia.nocookie.net/pixar/images/1/10/Guido.png/revision/latest?cb=20140314012724),
  [celui-là](https://en.wikipedia.org/wiki/Guido_van_Rossum#/media/File:Guido_van_Rossum_OSCON_2006.jpg)
- [Mooc Python](https://www.fun-mooc.fr/courses/inria/41001S03/session03/about#). Un mooc de
  l'INRIA, carré.
- [Code combat](https://codecombat.com/)


### Divers

- La chaîne YouTube [3blue1brown](https://www.youtube.com/c/3blue1brown) pour des vidéos de maths
  générales.
- La chaîne YouTube de [Freya Holmér](https://www.youtube.com/c/Acegikmo) plutôt orientée *game
  design*, mais avec d'excellentes vidéos de géométrie computationnelle.

## Licences

[![CC BY Licence badge](https://i.creativecommons.org/l/by/4.0/88x31.png)](http://creativecommons.org/licenses/by/4.0/)

Copyright © 2021 Loïc Grobol [\<loic.grobol@gmail.com\>](mailto:loic.grobol@gmail.com)

Sauf indication contraire, les fichiers présents dans ce dépôt sont distribués selon les termes de
la licence [Creative Commons Attribution 4.0
International](https://creativecommons.org/licenses/by/4.0/). Voir [le README](README.md#Licences)
pour plus de détails.

 Un résumé simplifié de cette licence est disponible à <https://creativecommons.org/licenses/by/4.0/>.

 Le texte intégral de cette licence est disponible à <https://creativecommons.org/licenses/by/4.0/legalcode>
