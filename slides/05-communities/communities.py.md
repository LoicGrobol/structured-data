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

# Cours 5 : clusters et communautés

**Loïc Grobol** [&lt;lgrobol@parisnanterre.fr>](mailto:lgrobol@parisnanterre.fr)

2022-01-03

<!-- #endregion -->

## Clustering

Objectif : on a des données sous formes d'un ensemble d'éléments liés par des relations et on
cherche à y repérer des structures latentes sous la forme d'une partition en sous-ensembles — les
fameux *clusters*.

Les relations peuvent être explicites (relations sémantiques, voisinage dans un graphe…) ou induites
par le type des éléments (par exemple la distance entre les points dans un espace métrique).

On va voir quelques exemples intéressants, mais il y en a **plein**, vraiment *plein* d'autres.

## $K$-moyennes

Ou *$K$-means*. Il s'agit d'un problème de clustering très courant, essentiellement parce que :

- Les hypothèses à faire sur les données sont très légères : on a juste besoin d'une représentation
  vectorielle des données.
- Il y a un algorithme facile à comprendre et implémenter (pas forcément exact ni optimal, mais peu importe),
  avec une représentation visuelle agréable.
- Ça donne des résultats immédiatement, pas toujours top, mais lisibles.

Le revers de la médaille, comme on ne fait pas beaucoup d'hypothèses, c'est que ça n'a que la
qualité des représentations vectorielles : *garbage in, garbage out*. Mais de fait, ce n'est pas un
mauvais test de la qualité de ces représentations.

- **Hypothèse** On a un ensemble de données $E=\{x₁, …, x_n\}$ sous forme de vecteurs de dimension $d$ et un entier strictement positif $k$.
- **Objectif** Trouver une $K$-partition $\{S₁, …, S_k\}$ de $E$ (i.e. $⋃_i Sᵢ=E$ et $S_i∩S_j=∅$ si $i≠j$) telle que la somme des carrés des écarts inter-clusters à la moyenne $s$ soit minimale, avec
  
  $$\begin{equation}
    s = \sum_i\sum_{x∈S_i}\lVert x-μ_i \rVert² = \sum_i\sum_{x∈S_i}\mathop{d}(x, μᵢ)²
  \end{equation}$$

  et
  $$\begin{equation}
    μ_i = \frac{1}{\lvert S_i\rvert}\sum_{x∈S_i}x_i
  \end{equation}$$
  
On appelle les $μᵢ$ les *centroïdes* des clusters $Sᵢ$, et l'algorithme classique pour construire les $Sᵢ$ (l'algorithme de Lloyd-Max) repose dessus.

### Algorithme de Lloyd-Max


1. Choisir (en général aléatoirement) $k$ centroides $μ_1, …, μ_k$
2. Construire les $S₁, …, S_k$ tels que pour tout $i$, $S_i=\{x_j∈E \text{ tels que} \mathop{d}(x,μ_i) < \mathop{d}(x,μ_j) \text{ pour tout $j≠i$}\}$. Autrement dit, on affectecte chaque point de $E$ au centroïde le plus proche, ce qu'on appelle aussi la partition de Voronoï (Георгій Феодосійович Вороний) de $E$ suivant $μ_1, …, μ_k$.
3. Mettre à jour les centroides : $μᵢ := \frac{1}{\lvert Sᵢ\rvert}\sum_{x\in S_i}x_i$
4. Retour à 2

## Clustering hiérarchique

Idée : construire non pas directement une partition en cluster mais une série de partitions allant du plus grossier au plus fin.

- Au plus fin, les clusters sont les singletons
- Au plus grossier, il n'y a qu'un seul cluster qui contient tous les points de données

On coupe ensuite la hiérarchie pour obtenir des compromis.

Deux techniques : agglomération et division.

<!-- #region -->
## Clustering dans un graphe

### Clustering par propagation d'étiquettes

Raghavan et al. ([2007](https://link.aps.org/doi/10.1103/PhysRevE.76.036106)).

On dispose d'un graphe $Γ$ dont l'ensemble des sommets est $S=\{s₁, …, s_n\}$. Notre objectif va être de construire des clusters en affectant une étiquette $l(s)$ à chaque sommet $s∈S$ et prenant ensuite comme clusters les ensembles de sommets ayant la même étiquette.

On dit qu'un nœud $s$ *suit le consensus local* si $l(s)$ est majoritaire au sein des voisins de $s$. L'algorithme consiste à faire en sorte que chacun des nœuds de $Γ$ suive le consensus local.

- Pour tout $sᵢ∈S$, on choisit $l(sᵢ):=i$
- Tant qu'il existe des nœuds qui ne suivent pas le consensus local
  - Pour chaque $s∈S$
    - On détermine $k$, l'étiquette majoritaire au sein des voisins de $s$
    - $l(s) := k$

<!-- #endregion -->

## Appplication à la représentation vectorielles de mots


Connes et Dugué ([2019](https://hal.archives-ouvertes.fr/hal-02408156))

- Lire
- Résumer
  - Principe général en quelques phrases
  - Choix techniques avec leurs justifications et leurs conséquences
- Reproduire sur [CIDRE](https://www.ortolang.fr/market/corpora/cidre)
