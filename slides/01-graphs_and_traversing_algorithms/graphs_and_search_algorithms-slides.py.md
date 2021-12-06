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
Cours 1 : Graphes et algorithmes de parcours
============================================

**Loïc Grobol** [<lgrobol@parisnanterre.fr>](mailto:lgrobol@parisnanterre.fr)

2021-11-22
<!-- #endregion -->

```python
from IPython.display import display, Markdown
```

```python
import matplotlib.pyplot as plt
```

```python
import networkx as nx
```

## Graphes et représentations

## 😴 Exo 😴

Écrire une fonction en Python qui étant donné un graphe $G$, donné sous la forme d'un ensemble de
nœuds et d'un ensemble d'arêtes et un nœud $a$ de $G$, renvoie l'ensemble des voisins de $a$ dans
$G$.


```python
from typing import Any, Collection, Set, Tuple

def neighbours(nodes: Collection, edges: Collection[Tuple[Any, Any]], a) -> Set:
    pass # À toi de jouer
```

```python
def neighbours(nodes: Collection, edges: Collection[Tuple[Any, Any]], a) -> Set:
    res = []
    for edge_start, edge_end in edges:
        if a == edge_start:
            res.append(edge_end)
        elif a == edge_end:
            res.append(edge_start)
    return set(res)
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

```python
nx.draw_networkx(nx.Graph(graph_edges))
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

```python
def edges_to_matrix(edges: Collection[Tuple[int, int]]) -> ArrayLike:
    graph_size = max(n for e in edges for n in e)
    res = np.zeros((graph_size, graph_size))
    for edge_start, edge_end in edges:
        res[edge_start-1, edge_end-1] = 1
        res[edge_end-1, edge_start-1] = 1
    return res

def matrix_to_list(matrix: ArrayLike) -> Sequence[Sequence[int]]:
    res = [[] for _ in range(matrix.shape[0])]
    for i in range(matrix.shape[0]):
        for j in range(matrix.shape[1]):
            if matrix[i, j] == 1:
                res[i].append(j+1)
    return res

def list_to_edges(lst: Sequence[Sequence[int]]) -> Collection[Tuple[int, int]]:
    res = []
    for i, neighbours in enumerate(lst, start=1):
        for j in neighbours:
            if j > i:
                res.append((i, j))
    return res
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
    [1, 3],
    [2, 4, 5],
    [3, 5],
    [3, 4]
]

assert (edges_to_matrix(graph_edges) == graph_matrix).all()
tester.assertEqual(matrix_to_list(graph_matrix), graph_list)
tester.assertEqual(list_to_edges(graph_list), graph_edges)
```

```python
nx.draw_networkx(nx.Graph(graph_edges))
```

## Chemins et connexité

## 🏝️ Exo 🏝️

### Recherche de chemin

Écrire une fonction en Python qui étant donné un graphe $G$ sur $\{1, …, n\}$, donné sous la forme
de votre choix, et deux entiers $a$ et $b$ compris entre $1$ et $n$ détermine s'il existe un chemin
entre $a$ et $b$ dans $G$.

```python
# Si le graphe est donné comme liste d'arêtes, il faut préciser n,
# sinon, ce n'est pas la peine
def has_path(graph, n: int, a: int, b: int) -> bool:
    pass # À toi de coder
```

```python
from typing import Dict, Optional, Set

# On utilise un dict pour ne pas avoir à se préoccuper de shifter les indices,
# notre graphe étant sur {1, …, n} et les indices de listes de taille n étant
# 0, …, n-1.
# On utilise des ensembles plutôt que des listes d'adjacence pour éviter de se
# poser des questions si jamais on a des arêtes répétées
def edges_to_adjacency_dict(graph: Collection[Tuple[int, int]], n: Optional[int]=None) -> Dict[int, Set[int]]:
    if n is None:
        n = max(idx for edge in graph for idx in edge)
    res = {i: set() for i in range(1, n+1)}
    for a, b in graph:
        res[a].add(b)
        res[b].add(a)
    return res
```

```python
def has_path(graph: Dict[int, Set[int]], a: int, b: int) -> bool:
    stack = [a]
    visited = set()
    while stack:
        current_node = stack.pop()
        visited.add(current_node)
        for neighbour in graph[current_node]:
            if neighbour == b:
                return True
            elif neighbour in visited:
                continue
            else:
                stack.append(neighbour)
    return False
```

Pour tester


```python
graph = edges_to_adjacency_dict([(1, 2), (3, 4), (3, 5), (4, 5)], 5)
assert has_path(graph, 1, 2)
assert has_path(graph, 2, 1)
assert has_path(graph, 3, 5)
assert not has_path(graph, 1, 3)
assert not has_path(graph, 5, 2)
graph[6] = set()
assert not has_path(graph, 1, 6)
```

```python
nx.draw_networkx(nx.Graph(graph))
```

### Test de connexité

Écrire une fonction en Python qui détermine si un graphe sur $\{1, …, n\}$, donné sous la forme de
votre choix, est connexe.

```python
def is_connex(graph, n: int) -> bool:
    pass # À toi de coder
```

```python
def is_connex(graph: Dict[int, Set[int]]) -> bool:
    start = next(iter(graph.keys()))
    to_visit = [start]
    visited = set()
    while to_visit:
        current_node = to_visit.pop()
        visited.add(current_node)
        for neighbour in graph[current_node]:
            if neighbour in visited:
                continue
            else:
                to_visit.append(neighbour)
    return len(visited) == len(graph)
```

```python
assert is_connex(edges_to_adjacency_dict([(1, 2), (2, 3), (3, 4), (3, 5), (4, 5)]))
assert not is_connex(edges_to_adjacency_dict([(1, 2), (2, 3), (3, 4), (3, 5), (4, 5)], 6))
assert not is_connex(edges_to_adjacency_dict([(1, 2), (3, 4), (3, 5), (4, 5)]))
```

```python
nx.draw_networkx(nx.Graph(edges_to_adjacency_dict([(1, 2), (2, 3), (3, 4), (3, 5), (4, 5)])))
```

```python
nx.draw_networkx(nx.Graph(edges_to_adjacency_dict([(1, 2), (2, 3), (3, 4), (3, 5), (4, 5)], 6)))
```

```python
nx.draw_networkx(nx.Graph(edges_to_adjacency_dict([(1, 2), (3, 4), (3, 5), (4, 5)])))
```

### Composantes connexes

Écrire une fonction en Python qui étant donné un graphe sur $\{1, …, n\}$, donné sous la forme de
votre choix, renvoie ses composantes connexes maximales sous forme d'une liste de listes d'entiers.

```python
from typing import List

def connex_components(graph, n: int) -> List[List[int]]:
    pass # À toi de coder
```

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
connex_components(edges_to_adjacency_dict([(1, 2), (2, 3), (3, 4), (3, 5), (4, 5)]))
```

```python
nx.draw_networkx(nx.Graph(edges_to_adjacency_dict([(1, 2), (2, 3), (3, 4), (3, 5), (4, 5)])))
```

```python
connex_components(edges_to_adjacency_dict([(1, 2), (3, 4), (3, 5), (4, 5)]))
```

```python
nx.draw_networkx(nx.Graph(edges_to_adjacency_dict([(1, 2), (3, 4), (3, 5), (4, 5)])))
```

## Graphes particuliers

## 🌳 Exo 🌳

### Graphes connexes

Écrire des fonctions en Python qui déterminent si un graphe connexe donné est

- Un graphe complet
- Une étoile
- Une chaîne

```python
def is_complete(graph) -> bool:
    pass # À toi de coder
```

On exploite le fait qu'un graphe complet simple non-orienté à $n$ nœuds a $\frac{n(n-1)}{2}$ arêtes.

```python
def is_complete(graph) -> bool:
    n = max(e for edge in graph for e in edge)
    return len(graph) == n*(n-1)/2
```

```python
assert is_complete([(1, 2), (1, 3), (1, 4), (2, 3), (2, 4), (3, 4)])
assert not is_complete([(1, 2), (1, 3), (1, 4), (2, 4), (3, 4)])
```

```python
nx.draw_networkx(nx.Graph([(1, 2), (1, 3), (1, 4), (2, 3), (2, 4), (3, 4)]))
```

```python
nx.draw_networkx(nx.Graph([(1, 2), (1, 3), (1, 4), (2, 4), (3, 4)]))
```

Si jamais il risque d'y avoir des doublons dans la représentation

```python
def is_complete(graph) -> bool:
    n = max(e for edge in graph for e in edge)
    dedup = set(sorted(e) for e in graph)
    return len(dedup) == n*(n-1)/2
```

```python
def is_star(graph) -> bool:
    pass # À toi de coder
```

Il y a plusieurs façon de faire, mais la plus simple, comme on sait déja que le graphe est connexe c'est de compter les degrés des nœuds.

```python
def is_star(graph) -> bool:
    adj_dict = edges_to_adjacency_dict(graph)
    center_found = False
    for neighbours in adj_dict.values():
        if len(neighbours) != 1:
            if center_found:
                return False
            else:
                center_found = True
    return True
```

```python
assert is_star([(1, 2), (1, 3), (1, 4), (1, 5)])
assert not is_star([(1, 2), (1, 3), (1, 4), (1, 5), (2, 3)])
assert not is_star([(1, 2), (2, 3), (3, 4), (4, 5)])
```

```python
graphs = [
    [(1, 2), (1, 3), (1, 4), (1, 5)],
    [(1, 2), (1, 3), (1, 4), (1, 5), (2, 3)],
    [(1, 2), (2, 3), (3, 4), (4, 5)],
]
fig, axes = plt.subplots(len(graphs))
for g, ax in zip(graphs, axes):
    nx.draw_networkx(nx.Graph(g), ax=ax)
```

```python
def is_chain(graph) -> bool:
    pass # À toi de coder
```

Même idée que pour les étoiles : on compte les degrés

```python
def is_chain(graph) -> bool:
    adj_dict = edges_to_adjacency_dict(graph)
    ends_found = 0
    for neighbours in adj_dict.values():
        if len(neighbours) != 2:
            if ends_found == 2:
                return False
            else:
                ends_found += 1
    # Si le graphe est un cercle, il se peut que tous
    # les nœuds soient de degré 2
    return ends_found == 2
```

```python
assert is_chain([(1, 2), (2, 3), (3, 4), (4, 5)])
assert not is_chain([(1, 2), (2, 3), (3, 4), (4, 5), (5, 1)])
assert not is_chain([(1, 2), (1, 3), (1, 4), (1, 5)])
```

```python
graphs = [
    [(1, 2), (1, 3), (1, 4), (1, 5)],
    [(1, 2), (1, 3), (1, 4), (1, 5), (2, 3)],
    [(1, 2), (2, 3), (3, 4), (4, 5)],
]
fig, axes = plt.subplots(len(graphs))
for g, ax in zip(graphs, axes):
    nx.draw_networkx(nx.Graph(g), ax=ax)
plt.show()
```

### Recherche de gouverneur

Écrire une fonction en Python qui étant donné une arborescence (sous la forme d'un arbre et d'une
racine dans cet arbre) renvoie un dictionnaire qui associe à chaque nœud son gouverneur.


On fait simplement un parcours en profondeur en partant de la racine et en gardant trace du chemin
suivi, c'est-à-dire que pour chaque nœud, on note le nœud à partir duquel on y a accédé. Comme dans
un arbre, il n'y a qu'un seul chemin élémentaire qui mène de la racine à un nœud donné, ce nœud est
forcément le parent recherché.

```python
def get_parent(tree: Dict[int, Set[int]], root: int) -> Dict[int, int]:
    to_visit = [root]
    # Pour marquer la racine on choisit de lui mettre une boucle, ainsi tous les
    # nœuds visités ont un parent.
    # On aurait aussi pu commencer avec un dictionnaire vide et utiliser
    # `parent.value` pour vérifier si un nœud a déjà été visité
    parent: Dict[int, int] = {root: root}
    while to_visit:
        current_node = to_visit.pop()
        for neighbour in tree[current_node]:
            # N'arrivera que pour le parent de current_node
            if neighbour in parent:
                assert neighbour == parent[current_node]
            else:
                parent[neighbour] = current_node
                to_visit.append(neighbour)
    return parent
```

```python
tree = edges_to_adjacency_dict([(1, 2), (1, 3), (2, 4), (2, 5)])
```

```python
nx.draw_networkx(nx.Graph(tree))
```

```python
get_parent(tree, 1)
```

### Détection de cycles

Écrire une fonction en Python qui étant donné un graphe connexe indique s'il contient au moins un
cycle. En déduire une fonction qui détermine si un graphe connexe donné est un arbre.


```python
def has_cycle(graph) -> bool:
    pass # À toi de coder
```

On a vu dans l'exercice précédent que l'algorithme de parcours en profondeur nous permettait de
parcourir une arborescence en gardant trace des nœuds parents en se servant du fait qu'il n'y avait
qu'un seul chemin élémentaire allant de la racine à un nœud donné. On va faire exactement la même
chose ici, sauf qu'on va au contraire rechercher les cas où cette propriété n'est pas vérifiée.


L'idée est la suivante : il y a un cycle si et seulement s'il existe un nœud pour lequel il y a deux
chemins élémentaires distincts menant à la racine, si et seulement s'il existe un nœud pour lequel
l'algorithme de parcours en profondeur trouve deux parents.

```python
def has_cycle(graph: Dict[int, Set[int]]) -> bool:
    """ATTENTION: ne marche que pour un graphe connexe"""
    root = next(iter(graph.keys()))
    to_visit = [root]
    parent: Dict[int, int] = {root: root}
    while to_visit:
        current_node = to_visit.pop()
        for neighbour in graph[current_node]:
            if neighbour in parent:
                if neighbour != parent[current_node]:
                    return True
            else:
                parent[neighbour] = current_node
                to_visit.append(neighbour)
    return False
```

Une fois qu'on ça, comme pour un graphe connexe, être un arbre et être acyclique sont équivalents,
il suffit juste de vérifier si le graphe donné a un cycle.

```python
def is_tree(graph: Dict[int, Set[int]]) -> bool:
    return not has_cycle(graph)
```

```python
assert is_tree(edges_to_adjacency_dict([(1, 2), (1, 3), (1, 4), (1, 5)]))
assert is_tree(edges_to_adjacency_dict([(1, 2), (2, 3), (3, 4), (4, 5)]))
assert is_tree(edges_to_adjacency_dict([(1, 2), (1, 3), (2, 4), (2, 5)]))
assert not is_tree(edges_to_adjacency_dict([(1, 2), (1, 3), (2, 4), (2, 5), (5, 4)]))
assert not is_tree(edges_to_adjacency_dict([(1, 2), (1, 3), (2, 4), (2, 5), (4, 3)]))
```

```python
graphs = [
   [(1, 2), (1, 3), (1, 4), (1, 5)],
    [(1, 2), (2, 3), (3, 4), (4, 5)],
    [(1, 2), (1, 3), (2, 4), (2, 5)],
    [(1, 2), (1, 3), (2, 4), (2, 5), (5, 4)],
    [(1, 2), (1, 3), (2, 4), (2, 5), (4, 3)],
]
fig, axes = plt.subplots(len(graphs))
for g, ax in zip(graphs, axes):
    nx.draw_networkx(nx.Graph(g), ax=ax)
```

### Forêts

Écrire une fonction en Python qui détermine si un graphe donné est une forêt.


Une solution facile c'est de d'abord trouver les composantes connexes maximales, puis de déterminer si chacune d'entre elles est un arbre. Comme ça on a pas à réécrire de fonction. Mais on peut aussi combiner les deux : pendant qu'on est en train de parcourir les composantes, on peut aussi bien en profiter pour vérifier qu'elles sont acycliques. Concrètement ça va se passer exactement pareil que précédemment, simplement on a aura potentiellement plusieurs racines.

```python
def is_forest(graph: Dict[int, Set[int]]) -> bool:
    visited = set()
    to_visit = []
    parent: Dict[int, int] = dict()
    while len(visited) < len(graph):
        if not to_visit:
            current_node = next(n for n in graph.keys() if n not in visited)
            parent[current_node] = current_node
        else:
            current_node = to_visit.pop()
        visited.add(current_node)
        for neighbour in graph[current_node]:
            if neighbour in parent:
                if neighbour != parent[current_node]:
                    return False
            else:
                parent[neighbour] = current_node
                to_visit.append(neighbour)
    return True
```

```python
# Les arbres sont des forêts
assert is_forest(edges_to_adjacency_dict([(1, 2), (1, 3), (1, 4), (1, 5)]))
assert is_forest(edges_to_adjacency_dict([(1, 2), (2, 3), (3, 4), (4, 5)]))
assert is_forest(edges_to_adjacency_dict([(1, 2), (1, 3), (2, 4), (2, 5)]))
# Retirer une arête à un arbre donne une forêt
assert is_forest(edges_to_adjacency_dict([(1, 2), (1, 4), (1, 5)]))
assert is_forest(edges_to_adjacency_dict([(1, 2), (3, 4), (4, 5)]))
assert is_forest(edges_to_adjacency_dict([(1, 2), (1, 3), (2, 5)]))
# Ajouter une arête à un arbre crée un cycle : pas de forêt !
assert not is_forest(edges_to_adjacency_dict([(1, 2), (1, 3), (2, 4), (2, 5), (5, 1)]))
# Et d'autres non-forêts
assert not is_forest(edges_to_adjacency_dict([(1, 3), (2, 4), (2, 5), (5, 4)]))
```

```python
graphs = [
   [(1, 2), (1, 3), (1, 4), (1, 5)],
    [(1, 2), (2, 3), (3, 4), (4, 5)],
    [(1, 2), (1, 3), (2, 4), (2, 5)],
    [(1, 2), (1, 4), (1, 5)],
    [(1, 2), (3, 4), (4, 5)],
    [(1, 2), (1, 3), (2, 5)],
    [(1, 2), (1, 3), (2, 4), (2, 5), (5, 1)],
    [(1, 3), (2, 4), (2, 5), (5, 4)],
]
fig, axes = plt.subplots(len(graphs))
for g, ax in zip(graphs, axes):
    nx.draw_networkx(nx.Graph(g), ax=ax)
```

### ⚠️ Génération d'arbres ⚠️

Écrire une fonction en Python qui étant donné un entier $n$ génère tous les arbres sur $\{0, …, n-1\}$. C'est une bonne idée de commencer par raisonner par induction et d'utiliser la récursivité.
