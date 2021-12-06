---
jupyter:
  jupytext:
    formats: ipynb,md
    split_at_heading: true
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.13.3
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

<!-- LTeX: language=fr -->

<!-- #region slideshow={"slide_type": "slide"} -->

# Cours 3 : Graphes aléatoires et réseaux

**Loïc Grobol** [&lt;lgrobol@parisnanterre.fr>](mailto:lgrobol@parisnanterre.fr)

2021-12-06

<!-- #endregion -->

```python
from IPython.display import display, Markdown
```

On pourra visualiser les graphes créés avec [networkx](https://networkx.org)

```python
import networkx as nx
```

```python
g = nx.Graph({1: [2, 3, 4]})
nx.draw_networkx(g)
```

```python
g = nx.Graph([(1,2), (2,3), (4, 5)])
nx.draw_networkx(g)
```

## Modèle d'Erdős-Rényi

1\. Écrire une fonction en Python qui étant donné un entier $n$ et une probabilité $p$, renvoie un graphe aléatoire d'Erdős–Rényi de paramètre $(n, p)$ sous la forme que vous voulez.


2\. Écrire une fonction en Python qui étant donné deux entiers positifs $n$ et $k$ trace la courbe d'évolution de la taille
moyenne de la plus grande composante connexe maximale d'un échantillon de $k$ graphes aléatoires d'Erdős–Rényi de
paramètre $(n, p)$ pour $p$ allant de $0$ à $1$.


3\. Écrire une fonction en Python qui étant donné deux entiers positifs $n$ et $k$ et $p$ compris entre $0$ et $1$ donne une représentation du nombre moyen de nœuds de degré $d$ en fonction $d$ sur un échantillon de $k$ graphes aléatoires d'Erdős–Rényi de paramètre $(n, p)$.

## Modèle de Barabási–Albert

Écrire une fonction en Python qui étant donné deux entiers positifs $m$ et $n$ avec $m<n$, renvoie un graphe alétoire de Barabási–Albert à $n$ nœuds et $m$ nœuds de départ.

## Modèle de Watts–Strogatz

Écrire une fonction en Python qui étant donné deux entiers positifs $n$ et $k$ avec $k⩽n$ et un nombre $p$ compris entre $0$ et $1$, renvoie un graphe alétoire de Watts–Strogatz à $n$ nœuds, de degré moyen $k$ et de probabilité de reconnexion $p$.
