---
jupyter:
  jupytext:
    formats: ipynb,md
    split_at_heading: true
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.13.1
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

<!-- LTeX: language=fr -->
<!-- #region slideshow={"slide_type": "slide"} -->
Cours 1 : Graphes et algorithmes de parcours
============================================

**Loïc Grobol** [<lgrobol@parisnanterre.fr>](mailto:lgrobol@parisnanterre.fr)

2021-11-22
<!-- #endregion -->

```python
from IPython.display import display, Markdown
```

## Graphes et représentations

## 😴 Exo 😴

Écrire une fonction en Python qui étant donné un graphe $G$, donné sous la forme d'un ensemble de
nœuds et d'un ensemble d'arêtes et un nœud $a$ de $G$, renvoie l'ensemble des voisins de $a$ dans
$G$.


```python
from typing import Collection, Set

def neighbours(nodes: Collection, edges: Collection, a) -> Set:
    pass # À toi de jouer
```

Pour tester

```python
import unittest
tester = unittest.TestCase()

graph_nodes = {1, 2, 3, 4, 5, 6}
graph_edges = {(1, 2), (2, 3), (3, 4), (3, 5), (4, 5)}

tester.assertEqual(neighbours(graph_nodes, graph_edges, 2), {1, 3})
tester.assertEqual(neighbours(graph_nodes, graph_edges, 3), {2, 4, 5})
tester.assertEqual(neighbours(graph_nodes, graph_edges, 6), set())
```

## 🕸️ Exo 🕸️

Écrire des fonctions en Python qui permettent de convertir des graphes non-orientés sur $\{1, …,
n\}$ entre les représentations suivantes :

- Un ensemble de couples représentant les arêtes.
- Une matrice d'adjacence (implémentée par un `array` numpy)
- Un ensemble de listes d'adjacence (implémenté en Python par une liste de listes)

On pourra supposer que les graphes en question ne contiennent pas de nœuds isolés

```python
from typing import Collection, Sequence, Tuple
from numpy.typing import ArrayLike
import numpy as np

def edges_to_matrix(edges: Collection[Tuple[int, int]]) -> ArrayLike:
    pass # À toi de jouer

def matrix_to_list(matrix: ArrayLike) -> Sequence[Sequence[int]]:
    pass # À toi de jouer

def list_to_edges(lst: Sequence[Sequence[int]]) -> Collection[Tuple[int, int]]:
    pass # À toi de jouer

# Évidemment on peut choisir d'autres directions
```

Pour tester

```python
import unittest
tester = unittest.TestCase()

graph_edges = [(1, 2), (2, 3), (3, 4), (3, 5), (4, 5)]
graph_matrix = np.array(
    [
        [0, 1, 0, 0, 0],
        [1, 0, 1, 0, 0],
        [0, 1, 0, 1, 1],
        [0, 0, 1, 0, 1],
        [0, 0, 1, 1, 0],
    ]
)
graph_list = [
    [2],
    [1, 2],
    [2, 4, 5],
    [3, 5],
    [3, 4]
]

assert (edges_to_matrix(graph_edges) == graph_matrix).all()
tester.assertEqual(matrix_to_list(graph_matrix), graph_list)
tester.assertEqual(list_to_edges(graph_lists), graph_edges)
```

## Chemins et connexité

## 🏝️ Exo 🏝️

### Recherche de chemin

Écrire une fonction en Python qui étant donné un graphe $G$ sur $\{1, …, n\}$, donné sous la forme
de votre choix, et deux entiers $a$ et $b$ compris entre $1$ et $n$n détermine s'il existe un chemin
entre $a$ et $b$ dans $G$.

### Test de connexité

Écrire une fonction en Python qui détermine si un graphe sur $\{1, …, n\}$, donné sous la forme de
votre choix, est connexe.

```python
# Si le graphe est donné comme liste d'arêtes, il faut préciser n, sinon, ce n'est pas la peine
def is_connex(graph, n: int) -> bool:
    pass # À toi de coder
```

```python
# On peut se servir des fonctions précédentes si on a choisi une autre représentation
assert is_connex([(1, 2), (2, 3), (3, 4), (3, 5), (4, 5)], 5)
assert not is_connex([(1, 2), (2, 3), (3, 4), (3, 5), (4, 5)], 6)
assert not is_connex([(1, 2), (3, 4), (3, 5), (4, 5)], 5)
```

### Composantes connexes

Écrire une fonction en Python qui étant donné un graphe sur $\{1, …, n\}$, donné sous la forme de
votre choix, renvoie ses composantes connexes sous forme d'une liste de listes d'entiers.

```python
def connex_components(graph, n: int) -> List[List[int]]:
    pass # À toi de coder
```

## Graphes particuliers

## 🌳 Exo 🌳

### Graphes connexes

Écrire des fonctions en Python qui déterminent si un graphe connexe donné est

- Un graphe complet
- Une étoile
- Une chaîne
- Un arbre

```python
def is_complete(graph) -> bool:
    pass # À toi de coder
```

```python
assert is_complete([(1, 2), (1, 3), (1, 4), (2, 3), (2, 4), (3, 4)])
assert not is_complete([(1, 2), (1, 3), (1, 4), (2, 4), (3, 4)])
```

```python
def is_star(graph) -> bool:
    pass # À toi de coder
```

```python
assert is_star([(1, 2), (1, 3), (1, 4), (1, 5)])
assert not is_star([(1, 2), (1, 3), (1, 4), (1, 5), (2, 3)])
assert not is_star([[(1, 2), (2, 3), (3, 4), (4, 5)]])
```

```python
def is_chain(graph) -> bool:
    pass # À toi de coder
```

```python
assert is_chain([(1, 2), (2, 3), (3, 4), (4, 5)])
assert not is_chain([(1, 2), (2, 3), (3, 4), (4, 5), (5, 1)])
assert not is_chain([(1, 2), (1, 3), (1, 4), (1, 5)])
```

```python
def is_tree(graph) -> bool:
    pass # À toi de coder
```

```python
assert is_tree([(1, 2), (1, 3), (1, 4), (1, 5)])
assert is_tree([(1, 2), (2, 3), (3, 4), (4, 5)])
assert is_tree([(1, 2), (1, 3), (2, 4), (2, 5)])
assert not is_tree([(1, 2), (1, 3), (2, 4), (2, 5), (5, 4)])
assert not is_tree([(1, 2), (1, 3), (2, 4), (2, 5), (4, 3)])
```

### Forêts

Écrire une fonction en Python qui détermine si un graphe donné est une forêt.

### Recherche de gouverneur

Écrire une fonction en Python qui étant donné une arborescence (sous la forme d'un arbre et d'une
racine dans cet arbre) renvoie une liste qui associe à chaque nœud son gouverneur.
