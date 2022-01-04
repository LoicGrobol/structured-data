---
jupyter:
  jupytext:
    formats: ipynb,md
    split_at_heading: true
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.13.5
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

<!-- LTeX: language=fr -->

<!-- #region slideshow={"slide_type": "slide"} -->

# Oracles pour parseurs à transition

**Loïc Grobol** [<lgrobol@parisnanterre.fr>](mailto:lgrobol@parisnanterre.fr)

2022-01-03

<!-- #endregion -->

L'objectif est d'écrire des oracles pour des parsers shift-reduce. On va les tester sur des données Universal Dependencies qu'on récupère avec la bibliothèque `datasets`.


On commence par installer nos dépendances

```python
%pip install -U conllu datasets
```

```python
from datasets import load_dataset
dataset = load_dataset(
   "universal_dependencies", "fr_sequoia"
)
```

Voyons ce qu'il a dans le ventre

```python
dataset
```

```python
train_dataset = dataset["train"]
print(train_dataset.info.description)
```

Les données auront cette tête

```python
train_dataset[5]
```

On va d'abord écrire une fonction qui va nous transformer ces données de façon à ne garder que ce qui nous intéresse : identifiants et têtes (et forme pour l'affichage).

```python
from typing import Union
from dataclasses import dataclass


# On pourrait simplement utiliser des tuples
# mais c'est plus lisible comme ça
@dataclass
class Node:
    identifier: int
    form: str
    # On pourrait aussi utiliser `Optional[int]` mais je trouve ceci plus lisible
    head: Union[int, None]


def buffer_from_dict(d):
    return [
        Node(
            identifier=i,
            form=w,
            head=h,
        )
        for i, (w, h) in enumerate(
            zip(
                ["ROOT", *d["tokens"]],
                [None, *(int(h) for h in d["head"])],
            )
        )
    ]

buffer_from_dict(train_dataset[5])
```

Un oracle pour le système de transition *arc-standard* ([Nivre, 2004](https://aclanthology.org/W04-0308))) :

```python
from typing import List, Iterable
from collections import Counter

def arc_standard_oracle(buffer: List[Node]) -> Iterable[str]:
    # On copie le buffer pour ne pas le détruire et on le retourne pour
    # avec `pop` optimalement
    buffer = buffer[::-1]
    # Comme d'habitude, une liste c'est une pile, on commence avec la racine
    # dessus
    stack = [buffer.pop()]
    # Pour ne pas réduire un nœud avant d'avoir trouvé tous ses dépendants,
    # on les compte (et on ignore la racine). (On pourrait utiliser un générateur
    # plutôt qu'une boucle)
    remaining_dependents = Counter()
    for node in buffer[:-1]:
        remaining_dependents[node.head] += 1

    while buffer or stack:
        print([t.form for t in stack], [t.form for t in reversed(buffer)], sep="\t")
        if len(stack) < 2:
            if buffer:
                yield "SHIFT"
                stack.append(buffer.pop())
                continue
            else:
                break
        stack_top = stack[-1]
        stack_under = stack[-2]
        if stack_top.head == stack_under.identifier and not remaining_dependents[stack_top.identifier]:
            yield "REDUCE-RIGHT"
            remaining_dependents[stack_under.identifier] -= 1
            stack.pop()
        elif stack_top.identifier == stack_under.head and not remaining_dependents[stack_under.identifier]:
            yield "REDUCE-LEFT"
            remaining_dependents[stack_top.identifier] -= 1
            stack.pop(-2)
        else:
            if buffer:
                yield "SHIFT"
                stack.append(buffer.pop())
            else:
                raise ValueError("Non-projective tree")

list(arc_standard_oracle(buffer_from_dict(train_dataset[5])))
```

```python
def arc_eager_oracle(buffer: List[Node]) -> List[str]:
    buffer = buffer[::-1]
    stack = [buffer.pop()]
    
    # On pourrait aussi le faire quand on pousse sur la stack
    has_left_dependents = set()
    for node in buffer[:-1]:
        if node.identifier < node.head:
            has_left_dependents.add(node.head)

    while buffer:
        print([t.form for t in stack], [t.form for t in reversed(buffer)], sep="\t")
        stack_top = stack[-1]
        buffer_top = buffer[-1]
        if stack_top.head == buffer_top.identifier:
            yield "LEFT-ARC"
            stack.pop()
        elif stack_top.identifier == buffer_top.head:
            yield "RIGHT-ARC"
            stack.append(buffer.pop())
        # Trick galaxy brain de la projectivité
        elif buffer_top.identifier in has_left_dependents:
            yield "REDUCE"
            stack.pop()
        else:
            yield "SHIFT"
            stack.append(buffer.pop())
    if len(buffer) > 1:
        raise ValueError("Non-projective tree")

list(arc_eager_oracle(buffer_from_dict(train_dataset[6])))
```

```python
train_dataset[6]
```
