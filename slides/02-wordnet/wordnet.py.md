---
jupyter:
  jupytext:
    formats: ipynb,md
    split_at_heading: true
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.13.2
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

<!-- LTeX: language=fr -->
<!-- #region slideshow={"slide_type": "slide"} -->
Cours 2 : Réseaux lexicaux
============================================

**Loïc Grobol** [<lgrobol@parisnanterre.fr>](mailto:lgrobol@parisnanterre.fr)

2021-12-01
<!-- #endregion -->

```python
from IPython.display import display, Markdown
```

## Wordnet

```python
%pip install -U wn
```

```python
import wn
```

```python
wn.download("ewn:2020")
```

```python
wn.words("cat")
```

```python
wn.synsets("cat")
```

```python
for ss in wn.synsets("cat"):
    print(ss.words())
```

```python
for ss in wn.synsets("cat"):
    print(ss.definition())
    print([w.lemma() for w in ss.words()])
    print()
```

```python
wn.synsets("cat")[0].hypernyms()[0].words()
```

```python
wn.synsets("cat")[0].hyponyms()[0].words()
```

```python
 wn.synsets("alphabet")[0].get_related("mero_member")[0].words()
```

```python
 wn.synsets("human")[0].get_related("holo_member")[0].words()
```

```python
[ss.words() for ss in wn.synsets("bagpipe")[0].shortest_path(wn.synsets("cat")[0])]
```

```python
import wn.similarity

wn.similarity.path(wn.synsets("bagpipe")[0], wn.synsets("cat")[0])
```

## Exo

### Similarités path et vectorielles

```python
%pip install gensim
```

```python
import gensim.downloader as api
wv = api.load("glove-wiki-gigaword-50")
```

```python
wv.similarity("cat", "bagpipe")
```

1\. Visualiser avec matplotlib la corrélation entre similarité path dans `ewn:2020` et similarité cosinus dans `glove-wiki-gigaword-50` en considérant des paires de mots choisies aléatoirement dans l'intersection de leurs vocabulaires.

2\. Déterminer numériquement cette corrélation, par exemple avec [`scipy.stats.linregress`](model.wv.similarity('france', 'spain')

### Recherche d'analogies

Une analogie est relation du type « *doigt* est à *main* ce que *orteil* est à *pied* », ce qu'on note parfois `doigt:main :: orteil:doigt`. Chercher dans WordNet des analogies de ce type en utilisant la méthode [`wn.Synset.relations`](https://wn.readthedocs.io/en/latest/api/wn.html?highlight=relation_paths#wn.Synset.relations).
