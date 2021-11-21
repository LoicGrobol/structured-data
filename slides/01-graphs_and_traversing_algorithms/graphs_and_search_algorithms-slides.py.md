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
assert tester.assertEqual(matrix_to_list(graph_matrix), graph_list)
assert tester.assertEqual(list_to_edges(graph_lists), graph_edges)
```
