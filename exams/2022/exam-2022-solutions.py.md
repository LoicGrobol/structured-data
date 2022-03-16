---
jupyter:
  jupytext:
    formats: ipynb,md
    split_at_heading: true
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.13.6
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

<!-- LTeX: language=fr -->
Examen Données structurées 2022
===============================

## ATTENTION

<small>Quelques rappels bien voyants pour ne pas que vous les manquiez</small>

- **PENSEZ À TESTER VOTRE NOTEBOOK/SCRIPT EN L'EXÉCUTANT AVANT DE LE RENDRE**
  - Dans le cas d'un notebook, vérifiez que ça marche aussi si vous faites « restart kernel and run
    all »
- **SAUVEGARDEZ RÉGULIÈREMENT VOTRE TRAVAIL**
- **LISEZ LES CONSIGNES CI-DESSOUS ET VÉRIFIEZ QUE VOUS LES RESPECTEZ**

## Consignes

Vous travaillerez avec le fichier

Vous produirez un **script** ou un **notebook** Python compatible avec Python 3.9

Votre script ou notebook devra :

- Ouvrir les fichiers
  [`fr_sequoia-ud-train.conllu`](https://raw.githubusercontent.com/UniversalDependencies/UD_French-Sequoia/r2.9/fr_sequoia-ud-train.conllu)
  et
  [`fr_sequoia-sud-train.conllu`](https://sharedocs.huma-num.fr/wl/?id=lpoZ2kAIwONHyAD3tyS9pfE3Jl464Zlp&download=1),
  supposés se trouver dans le même dossier que lui et représentant le sous-ensemble d'entraînement
  du corpus Sequoia, respectivement dans sa version [Universal Dependencies
  2.9](https://github.com/UniversalDependencies/UD_French-Sequoia/tree/r2.9) et [SUD
  2.9](https://grew.fr/download/sud-treebanks-v2.9.tgz).
  - Voir ci-dessous pour des commandes permettant de les télécharger sur vos machines.
  - En cas de problème de téléchargement, appelez-moi tout de suite : je les ai aussi sur clé USB.
- Afficher sur la sortie standard (par exemple avec `print`) une ligne et une seule par question.
  - **N'afficher que la réponse et pas d'autre texte**
  - Par exemple pour la question « Combien y a-t-il de jours dans une semaine ? » et que la variable
  `days` contient `7`, répondez avec `print(days)`.
  - Si vous n'avez pas réussi à répondre, affichez une ligne vide).
- Ne produire aucune autre sortie
- Autrement dit : un `print` et un seul par question, que vous rendiez un script ou un notebook.
  - Astuce : si vous travaillez avec un notebook, vous pouvez le télécharger comme script et ainsi
    tester plus facilement s'il affiche bien une ligne et une seule par question.
- Termine en un temps raisonnable (disons moins de deux minutes sur votre machine).
- Les réponses doivent bien évidemment être déduites du fichier par votre script et non pas codées
  en dur.

Votre rendu ne doit comporter qu'un seul fichier, nommé `{prénom}_{nom}.py` ou
`{prénom}_{nom}.ipynb`, le prénom et le nom étant ceux sous lesquels vous êtes inscrit⋅e, en
minuscules, sans diacritiques et le cas échéant transcrit en alphabet latin. Par exemple, en ce qui
me concerne, ce serait `loic_grobol.py`.

Vous pouvez utiliser tous les modules de la bibliothèque standard, tous les modules listés dans
[`requirements.txt`](../../requirements.txt). Vous pouvez aussi utiliser n'importe quel autre module
disponible sur [PyPI](https://pypi.org) (ce n'est pas nécessaire, ni utile à mon avis, mais si vous
le faites, pensez à l'indiquer dans un commentaire ou une cellule markdown).

Une fois terminé, envoyez ce script à <lgrobol@parisnanterre.fr> avec en objet `Examen données
structurées` ou appelez-moi pour que je le récupère directement sur clé USB.

Pour les questions auxquelles vous n'aurez pas fourni de réponse (et donc affiché une ligne vide),
vous pouvez indiquer dans votre script des éléments de réponse partiels par des fragments de code ou
des commentaires. Dans ce cas, assurez-vous que vous n'affichez bien qu'une ligne vide en réponse à
cette question.


Commandes pour télécharger les treebanks :

```python
!wget -nc "https://raw.githubusercontent.com/UniversalDependencies/UD_French-Sequoia/r2.9/fr_sequoia-ud-train.conllu" -O "fr_sequoia-ud-train.conllu"
```

```python
!wget -nc "https://sharedocs.huma-num.fr/wl/?id=lpoZ2kAIwONHyAD3tyS9pfE3Jl464Zlp&download=1" -O "fr_sequoia-sud-train.conllu"
```

## Questions

- *Écrire des fonctions pour les opérations qui reviennent souvent est très fortement recommandé.*
- *Dans tout ce qui suit, on parle de **mots** au sens de UD : les signes de ponctuation en font
  partie et certains tokens peuvent correspondre à plusieurs mots — comme « du » en français qui
  correspond aux mots « de » et « le ».*

1. Quels sont vos noms et prénoms ?
   - Répondre sous la forme d'une chaîne de caractères, comme `Loïc Grobol`.
2. Combien d'arbres y a-t-il dans `fr_sequoia-ud-train.conllu` ?
   - Répondre par un entier, par exemple `76`.
3. Combien d'arbres y a-t-il dans `fr_sequoia-sud-train.conllu` et quelle est la longueur (en nombre
   de mots, pas de tokens) du plus long ?
   - Répondre par deux entiers séparés par un `|`, par exemple `1024|31`.
4. Lister les arcs de l'arbre `annodis.er_00008` dans `fr_sequoia-ud-train.conllu` sous la forme
   d'un dictionnaire dont les clés sont les dépendants et les valeurs sont les gouverneurs.
   - Par exemple pour un arbre dont `1` est la racine et dont les arcs sont `1→4`, `4→2`et `4→3`,
   afficher `{1: 0, 2: 4, 3: 4, 4: 1}`.
5. Lister les arcs de l'arbre `annodis.er_00008` dans `fr_sequoia-ud-train.conllu` sous la forme
   d'un dictionnaire dont les clés sont les gouverneurs et les valeurs sont les listes des
   dépendants.
   - Par exemple pour un arbre dont `1` est la racine et dont les arcs sont `1→4`, `4→2`et `4→3`
   `{1: [4], 2: [], 3: [], 4: [2, 3]}`.
6. Lister les sous-arbres de l'arbre `annodis.er_00008` dans `fr_sequoia-ud-train.conllu` sous la
   forme d'un dictionnaire dont les clés sont les gouverneurs et les valeurs sont les listes des
   descendants.
   - Par exemple pour un arbre dont `1` est la racine et dont les arcs sont `1→4`, `4→2`et `4→3`,
   afficher `{1: [2, 3, 4], 2: [], 3: [], 4: [2, 3]}`.
7. On appelle **profondeur** d'un arbre, la longueur du plus long chemin allant de la racine de
   l'arbre à un autre nœud. Afficher la profondeur moyenne des arbres des corpus
   `fr_sequoia-ud-train.conllu` et `fr_sequoia-sud-train.conllu`.
   - Afficher la réponse sous forme de deux nombres arrondis à deux chiffres après la virgule et
     séparés par `|`, par exemple `10.05|11.22`.
8. On dit qu'un arbre syntaxique en dépendances est **projectif** s'il n'a pas d'arcs qui se
   croisent quand les mots sont alignés dans leur ordre d'apparition dans la phrase. Afficher la
   proportion d'arbres projectifs dans les corpus `fr_sequoia-ud-train.conllu` et
   `fr_sequoia-sud-train.conllu`.
   - Afficher la réponse sous forme de deux pourcentages arrondis à deux chiffres après la virgule
     et séparés par `|`, par exemple `98.05%|90.22%`.

9. Pour les cracks :

   Étant donné un arbre syntaxique en dépendances $A$ de $n$ mots et un entier $i$ tel que $1⩽i<n$,
   on appelle **taille du flux de dépendances à la position $i$** le nombre $f_i(A)$ d'arcs $a⟷b$
   (peu importe la direction) de cet arbre tel que $a⩽i<b$, et on note $f(A)=\max_i f_i(A)$ la
   valeur maximale de cette taille. Calculer les valeurs moyennes de $f$ pour les arbres du corpus
   `fr_sequoia-ud-train.conllu` et du corpus `fr_sequoia-sud-train.conllu`.

   Afficher le résultat sous forme de deux nombres arrondis à deux chiffres après la virgule et
   séparés par `|`, par exemple `5.32|8.27`



## Imports

```python
from itertools import combinations
from statistics import mean
from typing import Dict, List, Tuple
```

## Charger un corpus arboré

```python
import conllu
```

### Version UD

```python
with open("fr_sequoia-ud-train.conllu") as in_stream:
    ud_sentences = list(conllu.parse_incr(in_stream))
```

```python
print(len(ud_sentences))
```

### Version SUD

```python
with open("fr_sequoia-sud-train.conllu") as in_stream:
    sud_sentences = list(conllu.parse_incr(in_stream))
```

```python
longest_sent_len = max(
    sum(1 for t in s if t["head"] is not None)
    for s in sud_sentence
)
```

```python
print(len(sud_sentences), longest_sent, sep="|")
```

## Interlude : un dict pour se rendre la vie facile

```python
ud_dict = {s.metadata["sent_id"]: s for s in ud_sentences}
```

```python
sud_dict = {s.metadata["sent_id"]: s for s in sud_sentences}
```

```python
ud_dict
```

```python
list(ud_sentences[6])
```

## Représentations d'arbres

### Trouver la racine

```python
def root_id(sentence: conllu.models.TokenList) -> int:
    return next(t for t in sentence if t["head"] == 0)["id"]
```

### Nœud → tête

```python
def head_dict(sentence: conllu.models.TokenList) -> Dict[int, int]:
    return {int(t["id"]): int(t["head"]) for t in sentence if t["head"] is not None}
```

Les `int` ne sont pas nécessaire, mais nous assurent qu'une exception est levée
`id` ou `head` n'est pas un entier. Ça ne devrait jamais arriver puisqu'on
enlève les tokens multimots avec la condition `head is not None`, donc si ça
arrive, c'est qu'il y a un problème quelque part et il vaut mieux qu'on soit
au courant.

Note : c'est plutôt sale comme façon de faire. Je le fais là pour avoir une réponse rapide à coder,
mais ce n'est pas à faire pour un projet que vous envisagez de maintenir.

```python
print(head_dict(ud_sentences[6]))
```

### Nœud → enfants

```python
def children_dict(sentence: conllu.models.TokenList) -> Dict[int, List[int]]:
    res = {t["id"]: [] for t in sentence if t["head"] is not None}
    for t in sentence:
        # On pourrait aussi tester `if t` mais explicite est mieux qu'implicite
        if t["head"] is None or t["head"] == 0:
            continue
        res[int(t["head"])].append(t["id"])
    return res
```

```python
print(children_dict(ud_sentences[6]))
```

### Nœuds → descendants

```python
def descendant_dict(sentence: conllu.models.TokenList) -> Dict[int, List[int]]:
    children = children_dict(sentence)
    
    def aux(node_id: int) -> Dict[int, List[int]]:
        # **LA COPIE AVEC `list` EST TRÈS IMPORTANTE ICI**
        # On va faire muter cette liste, si on ne la copie
        # pas, les modifications vont se répercuter sur `children`
        node_children = list(children[node_id])
        res = {node_id: node_children}
        for c in node_children:
            res.update(aux(c))
            res[node_id].extend(res[c])
        res[node_id].sort()
        return res
    
    root = root_id(sentence)
    return aux(root)
```

```python
print(descendant_dict(ud_sentences[5]))
```

## Statistiques

### Profondeur

```python
def depth(sentence: conllu.models.TokenList) -> int:
    children = children_dict(sentence)
    def aux(node_id: int) -> int:
        # On peut faire plus court, mais ça devient peu lisible
        children_depth = [aux(c) for c in children[node_id]]
        return 1 + max(children_depth, default=0)
    return aux(root_id(sentence))
```

```python
depth(ud_sentences[5])
```

```python
mean((depth(s) for s in ud_sentences))
```

```python
mean((depth(s) for s in sud_sentences))
```

```python
print(
  round(mean((depth(s) for s in ud_sentences)), 2),
  round(mean((depth(s) for s in sud_sentences)), 2),
  sep="|",
)
```

### Projectivité


Astuce pour déterminer si des arcs $a→b$ et $c→d$ se croisent : on se ramène au cas où $a<b$, $c<d$
et $a⩽c$, ce qu'on peut faire sans perte de généralité, puisque :

- Les rôles du gouverneur et du gouverné sont symétriques.
- La relation « se croiser » est symétrique.

Or dans ce cas, la seule possibilité pour que les arcs se croisent, c'est si $a<b<c<d$, puisque

- Si $a=c$ ou $b=d$, il n'y a pas de croisement.
- Idem si $b≥c$.

```python
def crossing(arc_1: Tuple[int, int], arc_2: Tuple[int, int]) -> bool:
    (a, b), (c, d) = sorted([sorted(arc_1), sorted(arc_2)])
    return a < c < b < d
```

```python
def projective(sentence: conllu.models.TokenList) -> bool:
    arc_pairs = combinations(head_dict(sentence).items(), 2)
    return not any(crossing(arc_1, arc_2) for arc_1, arc_2 in arc_pairs)
```

```python
s = next(s for s in ud_sentences if not projective(s))
s
```

```python
proj_ratio_ud = sum(1 for s in ud_sentences if projective(s))/len(ud_sentences)
proj_ratio_ud
```

```python
proj_ratio_sud = sum(1 for s in sud_sentences if projective(s))/len(sud_sentences)
proj_ratio_sud
```

(On aurait aussi pu faire ça avec `mean`, mais c'est crade)

```python
print(f"{proj_ratio_ud:.02%}|{proj_ratio_sud:.02%}")
```

### Flux de dépendances

```python
def flux(sentence: conllu.models.TokenList, position: int) -> int:
    """Renvoie le flux de dépendances **after** the node `position`"""
    max_id = sentence[-1]["id"]
    if not 1 <= position < max_id:
        raise ValueError(f"{position} is not a valid interstice for a sentence of {max_id} words")
    res = []
    # On pourrait utiliser une compréhension et  mais c'est moins lisible
    for d, h in head_dict(sentence).items():
        # On ne compte pas la racine
        if h == 0:
            continue
        # Sympa du point de vue typage. On crée une liste inutile mais
        # c'est négligeable en termes de perfs
        a, b = sorted([d, h])
        if a <= position < b:
            res.append((d, h))
    return res
```

```python
def max_flux_size(sentence: conllu.models.TokenList) -> int:
    return max(len(flux(sentence, i)) for i in range(1,  sentence[-1]["id"]))
```

```python
ud_sentences[4]
```

```python
max_flux_size(ud_sentences[4])
```

```python
print(
    round(mean((max_flux_size(s) for s in ud_sentences if len(s) > 1)), 2),
    round(mean((max_flux_size(s) for s in sud_sentences if len(s) > 1)), 2),
    sep="|",
)
```
