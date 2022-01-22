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

<!-- #region slideshow={"slide_type": "slide"} -->

# Cours 7 : Coréférence

**Loïc Grobol** [&lt;lgrobol@parisnanterre.fr>](mailto:lgrobol@parisnanterre.fr)

2022-01-26

<!-- #endregion -->

## Définitions générales

## Représentation des données

## Techniques de traitement


- [🤗 Neuralcoref](https://huggingface.co/coref/?text=The%20kiwi%20was%20on%20the%20table.%20It%20seemed%20ripe%20but%20you%20can%27t%20ever%20be%20sure.)

### Stanza

```python
import stanza
```

```python
stanza.install_corenlp(dir="local/corenlp")
```

```python
import os
os.environ["CORENLP_HOME"] = "local/corenlp"
```

```python
from stanza.server import CoreNLPClient
```

```python
with CoreNLPClient(
    annotators=["tokenize","ssplit","pos","lemma","ner", "parse", "depparse","coref"], 
    memory="4G",
    endpoint="http://localhost:9001",
    be_quiet=True,
) as client:
    text = "Albert Einstein was a German-born theoretical physicist. He was not a nice man."
    document = client.annotate(text)
```

```python
document.corefChain
```

```python
mentions_dict = dict()
for sent in document.sentence:
    for m in sent.mentions:
        mentions_dict[m.entityMentionIndex] = m
for chain in document.corefChain:
    print([mentions_dict[m.mentionID].entityMentionText for m in chain.mention])
```

```python
document.corefChain[0].mention
```

## Mesures d'évaluation
