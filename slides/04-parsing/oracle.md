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

```python
%pip install -U conllu datasets
```

```python
from datasets import load_dataset
dataset = load_dataset(
   "universal_dependencies", "fr_sequoia"
)
```

```python
dataset
```

```python
train_dataset = dataset["train"]
print(train_dataset.info.description)
```

```python
train_dataset[5]
```

```python
def buffer_from_dict(d):
    return [
        (i, w, h)
        for i, (w, h) in enumerate(
            zip(
                ["ROOT", *d["tokens"]],
                [None, *d["head"]],
            )
        )
    ]

buffer_from_dict(train_dataset[5])
```

```python
from collections import Counter

def oracle(buffer):
    # On copie le buffer pour ne pas le détruire et on le retourne pour
    # avec `pop` optimalement
    buffer = reversed(buffer)
    # Comme d'habitude, une liste c'est une pile
    stack = []
    # On va construire le résultat à l'envers (pour utiliser `append` et on
    # retourna à la fin)
    res = []
    # Pour ne pas réduire un nœud avant d'avoir trouvé tous ses dépendants,
    # on les compte (et on ignore la racine)
    remaining_dependents = Counter()
    for _, _, head in buffer[:-1]:
        remaining_dependents[head] += 1

    while buffer or stack:
        if len(stack) < 2:
            if buffer:
                res.append("SHIFT")
                stack.append(buffer.pop())
            else:
                break
        stack_top = stack[-1]
        stack_under = stack[-2]
        if stack_top[2] == stack_under[0] and not remaining_dependents[stack_top[0]]:
            res.append("LEFT-ARC")
            stack.pop(-2)
        elif
```
