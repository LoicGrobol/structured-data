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

> **Sam Vimaire** soupira quand il entendit **le** cri.

---

<!-- LTeX: language=en -->

> **The Eyjafjallajökull volcano**, one of Iceland’s largest had been dormant
> for nearly two centuries before returning gently to life in the late evening
> of March 20, 2010, noticeable at first by the emergence of a red cloud
> glowing above the vast glacier that covers it. In the following days, fire
> fountains jetted from a dozen vents on the volcano, reaching as high as
> 100 meters.

<!-- LTeX: language=fr -->

---

La communication en langage naturel inclut des références à des **entités**, qui existent dans un
espace discursif.

- Objets, concepts, faits, évènements…
  - > **La pomme** est un fruit et **c'**est délicieux.
  - > « […] faut que je le note, d’ailleurs vous me **le** faites rappeler »
- Pas forcément du monde réel :
  - > **Morgan** a vu une licorne. **Elle** avait une crinière dorée
  - > Si j'avais **une voiture**, **elle** serait indigo.
- Même pas forcément possibles :
  - > **Un triangle à quatre côtés**, c'est quelque chose d'incroyable

Ces entités ne sont d'ailleurs pas fixes et peuvent évoluer au fil du discours :

> Prenez **une belle courgette bien mûre**. Préparez-**la** à passer au four, coupez-**la** en
> quatre morceaux et faites-**la** rôtir avec du thym et du paprika fumé pendant une heure.
> Servez-**la** avec du riz et savourez **son** parfum doux et délicatement épicé.

---

- En général, les éléments linguistiques qui désignent ces entités sont des constituants (ou des
  *catena*).
- Le cas typique : pronoms et syntagme nominaux (mais pas tous !)

On dit que ces éléments sont des **mentions** qui **réfèrent** à des entités et que des mentions qui
font référence à une même entité sont **coréférentes**.

- L'ensemble des mentions qui réfèrent à une entité est une **chaîne de coréférence(s)**.
- Une chaîne qui n'a qu'une seule mention (et par extension la mention en question) est un
  **singleton**.
- Si on considère une mention $m$, les mentions qui la précèdent dans la chaîne de coréférence où
  elle se trouve sont ses **antécédents**.

## En TAL

La tâche de « détection des chaînes de coréférences » (*coreference resolution*) est en général :

- Limitée aux seules mentions nominales et pronominales (ou des seuls évènements).
- Ne suppose pas de modélisation explicite des entités.

Autrement dit, elle peut être décomposée en deux sous-tâches

- Détection des mentions.
- Reconstruction des chaînes de coréférences.

Tâches connexes :

- NER / Entity linkink
- Résolution des anaphores pronominales
- Schémas de Winograd

## Données

Corpus principaux :

En anglais :

- MUC
- ACE
- OntoNotes/SemEval/CoNLL-201{1,2} (techiquement multilingue)

En français

- ANCOR
- DEMOCRAT

Multilingues : Universal Anaphora / [CorefUF](https://ufal.mff.cuni.cz/corefud)

## Techniques de traitement

### Représentations

- Mentions
- Paires de mentions
- Ensembles de mentions

### Détection des mentions

- Segmentation
- Détection dans une représentation hiérarchique
- Classification d'empans

### Détection des chaînes

Modèles :

- Orientée liens (paires de mentions)
  - Détection : classification vs. ranking vs. score
  - Décodage : glouton ou global
- Entité-mention
  - Orientée mentions
  - Orientée entités/chaînes

## Implémentations

Paradigme dominant : E2EC de Kenton Lee et al.

### Neuralcoref

[🤗 Neuralcoref](https://huggingface.co/coref/?text=The%20kiwi%20was%20on%20the%20table.%20It%20seemed%20ripe%20but%20you%20can%27t%20ever%20be%20sure.)

### Stanza/CoreNLP

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

**Exploration** :

- Testez des exemples avec CoreNLP et neuralcoref et comparez les sorties. Quelles différences
observe-t-on ?
- Testez leur comportement sur des [schémas de
  Winograd](https://cs.nyu.edu/~davise/papers/WinogradSchemas/WSCollection.html), si vous pouvez
  faites des statistiques !

## Mesures d'évaluation

Dans la suite, on considère qu'on dispose d'un ensemble $K$ (*key*) de chaînes de coréférences
*gold* et d'un ensembe $R$ (*response*) de chaînes de coréférence détectées par un système. Les
chaînes sont vues comme des ensembles de mentions (représentées par des symboles arbitraires, par
exemple des entiers.


On notera $\lvert E\rvert$ le *cardinal* d'un ensemble $E$, c'est-à-dire le nombre d'éléments qu'il contient.

### MUC

$$
\newcommand\card[1]{\vert#1\rvert}
\begin{equation}
    \begin{aligned}
        P &= \frac{\sum_{r∈R'}(\card{r}-\card{p(r, K)})}{\sum_{r∈R'}(\card{r}-1)}\\
        R &= \frac{\sum_{k∈K'}(\card{k}-\card{p(k, R)})}{\sum_{k∈K'}(\card{k}-1)}
    \end{aligned}
\end{equation}
$$

où $K'$ et $R'$ sont les ensembles des chaînes respectivement *gold* et système de longueurs au
moins $2$ et $p(x, E) = \{x\cap A | A∈E\}$.

Intuitivement : précision/rappel de la détection des liens entre une mention et son plus proche
antécédent.

### B³

$$
\newcommand\card[1]{\vert#1\rvert}
\begin{equation}
    \begin{aligned}
        P &= \frac{\sum_{m∈M_R}\frac{\card{r(m)∩k(m)}}{\card{r(m)}}}{\card{M_R}}\\
        R &= \frac{\sum_{m∈M_K}\frac{\card{r(m)∩k(m)}}{\card{k(m)}}}{\card{M_K}}
    \end{aligned}
\end{equation}
$$

Où $r(m)$ (resp. $k(r)$) la chaîne système (resp. *gold*) qui contient la mention $m$ et $M_R$ et
$M_K$ sont les ensembles des mentions *gold* et système.

Intuitivement, la précision, c'est la moyenne sur l'ensemble des mentions *gold* du taux de
couverture de la chaîne *gold* associée à chaque mention $m$ par la chaîne système qui contient $m$.

### CEAF

$$
\begin{equation}
    \begin{aligned}
        P &= \frac{\sum_{r∈R}ϕ(A⁻¹(r), r)}{\sum_{r∈R}ϕ(r, r)}\\
        R &= \frac{\sum_{k∈K}ϕ(k, A(k))}{\sum_{k∈K}ϕ(k, k)}
    \end{aligned}
\end{equation}
$$

Où $ϕ$ est une fonction qui évalue l'accord entre deux ensembles et l'alignement $A$ associe à
chaque chaîne *gold* une chaîne système de telle sorte que $\sum ϕ(k, A(k)$ soit maximale. Si la
fonction $ϕ$ est donnée, un tel alignement peut se trouver efficacement avec l'algorithme de
Kuhn-Munkres (implémenté par exemple par
[`scipy.optimize.linear_sum_assignment`](https://docs.scipy.org/doc/scipy-0.18.1/reference/generated/scipy.optimize.linear_sum_assignment.html).

En pratique, on utilise surtout CEAFe, pour lequel la fonction est :

$$
\newcommand\card[1]{\vert#1\rvert}
\begin{equation}
    ϕ₄: (k, r) ⟼ \frac{2\card{k∩r}}{\card{k}+\card{r}}
\end{equation}
$$

Autrement dit, l'indice de Sørensen-Dice.

La définition peut paraître tordue, mais l'idée est sûrement la plus simple : pour évaluer la
qualité de la partition en chaîne de l'ensemble des mention on choisit d'associer à chaque chaîne
*gold* une chaîne système (en choisissant celle qui ressemble le plus) et on mesure l'accord moyen.

### 😢 Exercice 😢

On considèrera que les mentions sont représentés par les entiers de $1$ à $n$ pour un $n$ donné

1. Écrire une fonction qui, étant donnés une liste de couples de mentions coréférentes et $n$,
   renvoie l'ensemble des chaînes de coréférences correspondant (en considérant que les nombres qui
   n'apparaissent pas dans un couple correspondent à des singletons).
2. Implémenter MUC sous la forme d'une fonction qui prend en entrée un ensemble de chaînes de
   coréférences *gold* et un ensemble de chaînes de coréférences système et renvoie la précision et
   le rappel définis plus haut.
3. Faire de même avec B³
4. Utiliser les fonctions précédentes pour évaluer la qualité des sorties $R_{\{1,2,3,4\}}$
   vis-à-vis de la référence $K$ :

```python
K = {(1, 2), (2, 3), (3, 4), (4, 5), (6, 7), (8, 9), (9, 10), (10, 11), (11, 12)}
R1 = set()
R2 = {(1, 2), (2, 3), (3, 4), (4, 5), (5, 6), (6, 7), (7, 8), (8, 9), (9, 10), (10, 11), (11, 12)}
R3 = {(1, 2), (2, 3), (3, 4), (4, 5), (6, 7), (7, 8), (8, 9), (9, 10), (10, 11), (11, 12)}
R4 = {(1, 2), (2, 3), (3, 4), (4, 5), (6, 7), (5, 8), (8, 9), (9, 10), (10, 11), (11, 12)}
```
