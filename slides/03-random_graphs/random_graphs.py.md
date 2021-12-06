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

```python
from typing import Dict, Set
import random

def gen_er_graph(n: int, p: float) -> Dict[int, Set[int]]:
    res = {i: set() for i in range(1, n+1)}
    for i in range(1, n):
        for j in range(i+1, n+1):
            if random.random() < p:
                res[i].add(j)
                res[j].add(i)
    return res
```

```python
g = gen_er_graph(32, 0.1)
nx.draw_networkx(nx.Graph(g))
```

2\. Écrire une fonction en Python qui étant donné deux entiers positifs $n$ et $k$ trace la courbe d'évolution de la taille
moyenne de la plus grande composante connexe maximale d'un échantillon de $k$ graphes aléatoires d'Erdős–Rényi de
paramètre $(n, p)$ pour $p$ allant de $0$ à $1$.

```python
from typing import List

def connex_components(graph: Dict[int, Set[int]]) -> List[List[int]]:
    res: List[List[int]] = []
    to_visit = [next(iter(graph.keys()))]
    visited = set()
    current_component = []
    while len(visited) < len(graph):
        if not to_visit:
            res.append(current_component)
            current_component = []
            current_node = next(n for n in graph.keys() if n not in visited)
        else:
            current_node = to_visit.pop()
            if current_node in visited:
                continue
        visited.add(current_node)
        current_component.append(current_node)
        for neighbour in graph[current_node]:
            # On pourrait éviter `visited` et se contenter de regarder si les
            # voisins sont danc `current_component`
            if neighbour in visited:
                continue
            else:
                to_visit.append(neighbour)
    res.append(current_component)
    return res
```

```python
def er_giant_component_size_average(n: int, p: float, k: int) -> float:
    sizes_sum = 0
    for _ in range(k):
        g = gen_er_graph(n, p)
        components = connex_components(g)
        size = max(len(c) for c in components)
        sizes_sum += size
    return sizes_sum / (n*k)
```

```python
import numpy as np
import matplotlib.pyplot as plt

P = np.linspace(0, 1, 100)
S = [er_giant_component_size_average(128, p, 32) for p in P]
plt.plot(P, S)
plt.show()
```

Le seuil d'explosion de la taille de la composante géante est à $p=\frac{1}{n}$, on le voit mieux en ne prenant qu'une partie de l'échelle en abscisse :

```python
P = np.linspace(0, 4/128, 100)
S = [er_giant_component_size_average(128, p, 32) for p in P]
plt.plot(P, S)
plt.show()
```

3\. Écrire une fonction en Python qui étant donné deux entiers positifs $n$ et $k$ et $p$ compris entre $0$ et $1$ donne une représentation du nombre moyen de nœuds de degré $d$ en fonction $d$ sur un échantillon de $k$ graphes aléatoires d'Erdős–Rényi de paramètre $(n, p)$.

## Modèle de Barabási–Albert

Écrire une fonction en Python qui étant donné deux entiers positifs $m$ et $n$ avec $m<n$, renvoie un graphe alétoire de Barabási–Albert à $n$ nœuds et $m$ nœuds de départ.

## Modèle de Watts–Strogatz

Écrire une fonction en Python qui étant donné deux entiers positifs $n$ et $k$ avec $k⩽n$ et un nombre $p$ compris entre $0$ et $1$, renvoie un graphe alétoire de Watts–Strogatz à $n$ nœuds, de degré moyen $k$ et de probabilité de reconnexion $p$.
