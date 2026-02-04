---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "d6aea3002ca0fafcee45e60068a142d4", "grade": false, "grade_id": "cell-6a641665ea50c56d", "locked": true, "schema_version": 3, "solution": false, "task": false}, "slideshow": {"slide_type": ""}}

# Analyse de données sur la biodiversité des parcs nationaux américains

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "d2b7e15c2a4b66f4f5432f246bd1d10f", "grade": false, "grade_id": "cell-6a641665ea50c56e", "locked": true, "schema_version": 3, "solution": false, "task": false}, "slideshow": {"slide_type": ""}}

## Introduction

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "aa9f0d065b674118d65f8b10f2869aa3", "grade": false, "grade_id": "cell-6a641665ea50c56f", "locked": true, "schema_version": 3, "solution": false, "task": false}, "slideshow": {"slide_type": ""}}

L'objectif de ce TP est d'étudier la biodiversité au sein de parcs nationaux. Pour cela nous utiliserons des données d'observations de plusieurs espèces dans différents lieux.

En science des données vous devrez préparer les données, les analyser (statistiquement) et produire des figures pertinentes dans l'objectif de répondre à différentes questions.

:::{admonition} Sources

Les fichiers `Observations.csv` et `Species_info.csv` sont issus
originellement d'un projet de
[Kaggle](https://www.kaggle.com/code/karthikbhandary2/biodiversity-analysis/notebook).

Remarques: les données pour ce projet sont inventées bien
qu'*inspirées* par des données réelles.

:::

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "5eff63e9b178106b2b3103aefd274965", "grade": false, "grade_id": "cell-6ad4374b3e1cc45d", "locked": true, "schema_version": 3, "solution": false, "task": false}}

### Objectifs du projet

Vous êtes deux analystes de la biodiversité pour le service des parcs nationaux. Le service veut assurer la survie des espèces en péril et maintenir le niveau de biodiversité au sein de leurs parcs. Par conséquent, vos principaux objectifs seront de comprendre les caractéristiques des espèces et leur état de conservation, ainsi que ces espèces et leurs relations avec les parcs nationaux. Quelques questions qui se posent :

-   Quel animal est le plus répandu ? Quel parc possède le plus d'espèces ?
-   Quelle est la répartition des statuts de conservation des espèces ?
-   Certains types d'espèces sont-ils plus susceptibles d'être menacés ?

### Chargement des données

Ce TP contient deux ensembles de données. Le premier fichier CSV (*comma separated values*) contient des informations sur chaque espèce et un autre contient des observations d'espèces avec des emplacements de parc. Ces données seront utilisées pour répondre aux questions ci-dessus.

### Analyse des données

Des statistiques descriptives et des techniques de visualisation des données seront utilisées pour mieux comprendre les données. L'inférence statistique sera également utilisée pour tester si les valeurs observées sont statistiquement significatives. Certaines des mesures clés qui seront calculées incluent :

1.  distributions,
2.  comptage,
3.  relation entre les espèces,
4.  état de conservation des espèces.

### Évaluation et conclusion

Enfin, nous reviendrons aux questions posées. A-t-on pu répondre à toutes les questions? Peut-on aller plus loin? Nous réfléchirons aux limites et nous verrons si l'une des analyses aurait pu être effectuée à l'aide de méthodes différentes.

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "8b6f3d4436c1a1179eed971bf7b3c498", "grade": false, "grade_id": "cell-e7f5611c49adea51", "locked": true, "schema_version": 3, "solution": false, "task": false}}

## Chargement des données

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: a9f6f810d06d66d8d4bf22dc3f15a11d
  grade: false
  grade_id: cell-c1bfa68a968d0c01
  locked: true
  schema_version: 3
  solution: false
  task: false
slideshow:
  slide_type: ''
---
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline
```

```{code-cell} ipython3

```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "43e687d383113b8663936192861a8bb6", "grade": false, "grade_id": "cell-e7f5611c49adea52", "locked": true, "schema_version": 3, "solution": false, "task": false}}

Chargez les fichiers `media/species_info.csv` et
`media/observations.csv` sous forme de tables (*data frames*) appelées
`species` et `observations` respectivement.

**Indication:** La fonction `.head()` permet d'avoir un apercu du
contenu de chaque table.

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: d405d413540b91ed5abd5aa99346bdfa
  grade: false
  grade_id: cell-867461a5b5ba53e2
  locked: false
  schema_version: 3
  solution: true
  task: false
---
species = pd.read_csv("media/species_info.csv")

species.head()
```

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: cd64e94d6bc7a5fa41beac1b71b27f07
  grade: false
  grade_id: cell-48c4226568856ec4
  locked: false
  schema_version: 3
  solution: true
  task: false
---
observations = pd.read_csv("media/observations.csv")

observations.head()
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 3082227b6de8573028b1fadc2275ddbb
  grade: true
  grade_id: cell-4cabc03db76dbe7a
  locked: true
  points: 2
  schema_version: 3
  solution: false
  task: false
---
assert isinstance(observations, pd.DataFrame)
assert isinstance(species, pd.DataFrame)
assert observations.shape == (23296, 3)
assert species.shape == (5824, 4)
assert (observations.isna()).any(axis=None)
```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "68a89bd7b8d8acd023d6881845ecdc47", "grade": false, "grade_id": "cell-33d2c44e29b0dadb", "locked": true, "schema_version": 3, "solution": false, "task": false}}

### Caractéristiques des jeux de données

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "215d71d694739dbda4bcbc81f0292309", "grade": false, "grade_id": "cell-33d2c44e29b0dada", "locked": true, "schema_version": 3, "solution": false, "task": false}}

Quelles sont les dimensions des jeux de données?

:::{admonition} Consigne

Vous utiliserez la cellule de code ci-dessous pour mener les calculs
dont vous aurez besoin, puis vous rédigerez votre réponse dans la
cellule de texte qui suit, sous forme de phrase complète explicitant
les nombres de lignes et de colonnes.

Vous procéderez de même dans tout le reste du TP.

:::

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 36ecd4e0d574d0693bb568b6ea22bc58
  grade: true
  grade_id: cell-a9e1b8d3f6fe6b04
  locked: false
  points: 0
  schema_version: 3
  solution: true
  task: false
---
print(species.shape)

print(observations.shape)
```

+++ {"deletable": false, "nbgrader": {"cell_type": "markdown", "checksum": "52c31ef63659a7f1b763ffa2e6fc57e5", "grade": true, "grade_id": "cell-3af14f238a7ea5d1", "locked": false, "points": 2, "schema_version": 3, "solution": true, "task": false}}

Dans la table "species", il y a 5824 lignes et 4 colonnes. 

Dans la table "observations", il y a 23296 lignes et 3 colonnes.

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "251cd1220f13ff55293c50f782c5d106", "grade": false, "grade_id": "cell-65b6bfd5b959b473", "locked": true, "schema_version": 3, "solution": false, "task": false}}

### Jeu de données `species`

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "ea1152541d875ae2e94fb8330baf6f9a", "grade": false, "grade_id": "cell-65b6bfd5b959b474", "locked": true, "schema_version": 3, "solution": false, "task": false}}

Il est temps d'explorer un peu plus en profondeur la table
`species`.
1.  Répondez aux questions suivantes :
    - Combien y a-t-il d'espèces différentes?
    - Ce nombre est-il égal aux nombre de lignes? Pourquoi?
    - Proposez des hypothèses permettant d'expliquer cette observation.

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 230c0e7decd8c74f4700cbec51fe325e
  grade: true
  grade_id: cell-8e409954360e88d1
  locked: false
  points: 0
  schema_version: 3
  solution: true
  task: false
---
species.describe()
species.loc[species["common_names"] == "Lawrence's Warbler", :]
```

+++ {"deletable": false, "nbgrader": {"cell_type": "markdown", "checksum": "d2bac3f627d4434e8affc7d52da6e9a9", "grade": true, "grade_id": "cell-035e76b795b14676", "locked": false, "points": 4, "schema_version": 3, "solution": true, "task": false}}

Il y a 5504 espèces différentes dans la table.

Ce nombre est différent du nombre de lignes, en effet, il y a des doublons dans la table. En effet, il y a 5824 lignes mais seulement 5504 espèces différentes dans la table. Cela est dû à la présence de doublons dans la table, par exemple l'espèce "Lawrence's Warbler" se trouve à plusieurs reprises dans la table, notamment aux lignes 279 et 280.

La présence de doublon dans la table peut être dûe à différentes orthographes pour une même espèce. (Par exemple aux lignes 279 et 280 le "Lawrence's Warbler" a un nom scientifique écrit de deux manières différentes.). Une autre explication peut être le fait que les scientifiques ont observés la même espèce dans des réserves différentes.

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "2de815c5c2494860a6a0e707ca39618f", "grade": false, "grade_id": "cell-8d27ed348b4a754f", "locked": true, "schema_version": 3, "solution": false, "task": false}}

2.  Calculez dans la variable `ncat` le nombre de catégories présentes
    dans la table. À quoi cette colonne correspond-t-elle?

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 76ba4a6ac42e6dfe979435d57839ffd4
  grade: false
  grade_id: cell-5513563a0941f609
  locked: false
  schema_version: 3
  solution: true
  task: false
---
ncat = species["category"].unique()
ncat = len(ncat)
print(ncat)
```

+++ {"deletable": false, "nbgrader": {"cell_type": "markdown", "checksum": "bbfcd461acde3ab870ea089e37436adb", "grade": true, "grade_id": "cell-56c4d364a6c3ddfa", "locked": false, "points": 0, "schema_version": 3, "solution": true, "task": false}}

Elle correspond aux grandes classes (familles) d'êtres vivants.

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 15d18068b2c191ddb736748047fdf519
  grade: true
  grade_id: cell-18fc7ac0ca95a6a6
  locked: true
  points: 1
  schema_version: 3
  solution: false
  task: false
---
assert ncat**2+32 == 81
```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "64c978b805f5b83d4457537478791870", "grade": false, "grade_id": "cell-ecbf69c48c0617bf", "locked": true, "schema_version": 3, "solution": false, "task": false}}

3.  Calculez dans l'objet `species_cat` le nombre d'espèces par
    catégorie. Vous utiliserez la méthode `groupby` vue en
    cours. Faites un barplot pour représenter ce résultat. Quelle
    catégorie a le plus d'espèces? Est-ce surprenant?

:::{admonition} Remarque

Les Vascular Plant correspondent aux
[Trachéophytes](https://fr.wikipedia.org/wiki/Tracheophyta) et
regroupent les plantes à fleurs
[Angiospermes](https://fr.wikipedia.org/wiki/Angiosperme). Les
Nonvascular plant correspondent aux [plantes
non-vasculaires](https://fr.wikipedia.org/wiki/Plante_non_vasculaire)*

:::

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: e76d82cce290c26425e632d6c599e88b
  grade: true
  grade_id: cell-248b4d376d7716c4
  locked: false
  points: 3
  schema_version: 3
  solution: true
  task: false
---
import matplotlib.pyplot as plt

species_cat = species.groupby('category')["common_names"].count()

species_cat.plot(kind='bar');
plt.ylabel('Total')
plt.xlabel('category')
plt.title("Comparaison du nombre d'espèces selon la catégorie");
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 2124fe9d122afd98ec9b40bf6705956d
  grade: true
  grade_id: cell-42a30a9eee9814c4
  locked: true
  points: 2
  schema_version: 3
  solution: false
  task: false
---
assert isinstance(species_cat, pd.Series)
assert species_cat.size == 7
assert species_cat['Bird']>20
```

+++ {"deletable": false, "editable": true, "nbgrader": {"cell_type": "markdown", "checksum": "908b5734068425caa817500a174c11c6", "grade": true, "grade_id": "cell-0d83f9d55e19a5c7", "locked": false, "points": 1, "schema_version": 3, "solution": true, "task": false}, "slideshow": {"slide_type": ""}}

Les "Vascular Plant" sont la catégorie qui a le plus d'espèces mais cela n'est pas surprenant. En effet, le groupe des Tracheophyta comprend 391 000 espèces différentes, tandis que les oiseaux, qui est la deuxième catégorie avec le plus d'espèces, n'en comptent "que" 11 150 différentes. Il y a donc plus de chances de voir une nouvelles espèces de plantes que d'oiseaux (ou autre catégorie d'être vivant) dans la nature. (source: Wikipédia)

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "fce5709c10a7ed60ef3a3af083bf0b76", "grade": false, "grade_id": "cell-d8c24200712072c6", "locked": true, "schema_version": 3, "solution": false, "task": false}}

4.   Créer la variable `species_status`qui contient les différents
    statuts possibles de ces espèces. Dans un paragraphe de texte,
    décrivez chacune de ces catégories. A votre avis, que signifie une
    valeur `nan`?

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 45c49e1d4227a5c1e51d91257f303abd
  grade: false
  grade_id: cell-7c7a5b388b6a587e
  locked: false
  schema_version: 3
  solution: true
  task: false
---
species_status = species["conservation_status"].unique()
species_status
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: f6665d54889d592bc7a3577df605fb00
  grade: true
  grade_id: cell-1cde562156519625
  locked: true
  points: 2
  schema_version: 3
  solution: false
  task: false
---
import hashlib
assert hashlib.md5(species_status[1].encode("utf-8")).hexdigest() == '5b98e09fa16ca9ffb8a4e6481fab1ef7'
assert hashlib.md5(species_status[2].encode("utf-8")).hexdigest() == 'e8ffc938d2592b28a666523cc1c80a5a'
assert hashlib.md5(species_status[3].encode("utf-8")).hexdigest() == '667cebca285ad56866f34c25309d99f3'
assert hashlib.md5(species_status[4].encode("utf-8")).hexdigest() == 'dd283e78058345d5e2ad72b7c7769579'
```

+++ {"deletable": false, "nbgrader": {"cell_type": "markdown", "checksum": "f5112434eb167090dcb3b5fd4e6fc647", "grade": true, "grade_id": "cell-4ca50000b3bce35a", "locked": false, "points": 0, "schema_version": 3, "solution": true, "task": false}}

"Species of Concern" correspond aux espèces préoccupantes.

"Endangered" correspond aux espèces en danger.

"Threatened" correspond aux espèces menacées.

"'In Recover" correspond aux espèces en voie de rétablissement.

"nan" pourrait donc concerner les espèces qui ne sont pas menacées, qui prospèrent, ou bien tout simplement celle où il n'y a pas de spécification de conservation.

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "95797af20b64887b6d9ccbd37af22d5d", "grade": false, "grade_id": "cell-2ecf922e2d9a9904", "locked": true, "schema_version": 3, "solution": false, "task": false}}

### Jeu de données `observations`

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "01da75a8df6d137323341f62c84e98a8", "grade": false, "grade_id": "cell-2ecf922e2d9a9905", "locked": true, "schema_version": 3, "solution": false, "task": false}}

On passe à l'observation de l'autre table, `observations`.

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: bf6221893b64a382f769759b49ee3545
  grade: false
  grade_id: cell-a393a50850f08b68
  locked: true
  schema_version: 3
  solution: false
  task: false
---
observations.describe(include="all")
```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "18eea5a46640f6211766e87b7a019c9e", "grade": false, "grade_id": "cell-a1af70876ffbc084", "locked": true, "schema_version": 3, "solution": false, "task": false}}

1.   À partir de la commande ci-dessus répondez aux questions suivantes :

    - Décrivez le contenu de la colonne `observations`.
    - Combien y a-t-il de données manquantes dans la table `observations`?
    - Combien d'espèces ont été vues au Yosemite?

**Rappel:** utilisez systématiquement des phrases complètes.

+++ {"deletable": false, "nbgrader": {"cell_type": "markdown", "checksum": "36cb000f1e594f89894e262ada1ae603", "grade": true, "grade_id": "cell-a5b1befcc2103d06", "locked": false, "points": 3, "schema_version": 3, "solution": true, "task": false}}

La colonne "observations" représente le nombre d'individus observés pour chaque espèce. Elle donne des informations simples et chiffrées comme le nombre total d'individus observés (23292), le nombre de fois minimum qu'une espèce a été observée (9 fois), etc...

Il a 23296 lignes dans la table "observations" (on l'obtient grâce à len(observations)). Or Il n'y a que 23295 données dans la colonne "park_name" (donc il manque une donnée dans cette colonne) et 23292 données dans la colonne "observations" (donc il manque 4 données dans cette colonne). Au total, il manque donc 5 données dans la table 'observations'.

Le parc du Yosemite est présent 5824 fois dans la table (dans la colonne "park_name"), on en conclut que 5824 espèces ont été vues au Yosemite (il pourrait cependant y avoir des doublons).

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "5b2e098dd3fe761690fd526439b92058", "grade": false, "grade_id": "cell-b312eba78f698844", "locked": true, "schema_version": 3, "solution": false, "task": false}}

2.  Indiquez dans `npark` le nombre de parcs étudiés. Où se
    situent-ils (faites une recherche internet)?

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 8e2195043c79311b7c372135a4914bed
  grade: false
  grade_id: cell-c8ad74a1e0672681
  locked: false
  schema_version: 3
  solution: true
  task: false
---
n = observations["park_name"].unique()
npark = len(n) - 1
npark
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: a94adf736341de5ba4fdcc64c10a610d
  grade: true
  grade_id: cell-ad9440d5f3e79eee
  locked: true
  points: 1
  schema_version: 3
  solution: false
  task: false
---
assert npark**2+93 == 109
```

+++ {"deletable": false, "nbgrader": {"cell_type": "markdown", "checksum": "ef7b983269ed1413ddad62a5ed22736b", "grade": true, "grade_id": "cell-09589dc8f6717a02", "locked": false, "points": 0, "schema_version": 3, "solution": true, "task": false}}

Il y a 4 parcs étudiés:

Le Great Smoky Mountains qui se trouve en Caroline du Nord, Tennesse aux USA.

Le Yosemite qui se situe en Californie, aux USA.

Le Redwood qui est en Californie, aux USA.

Et le Yellowstone qui se trouve principalement dans le Wyoming mais aussi dans l'Idaho et dans le Montana, aux USA.

Ils sont donc tous présents aux Etats-Unis.

Nous avons fait len(npark)-1 car la fonction .unique() renvoie également la donnée manquante indiquée par "NaN" identifiée plus haut. Cette donnée manquante n'est pas un autre parc (étant donnée que de mentionner qu'une seule fois un autre parc semble peu pertinent pour comparer avec les autres données) mais bien l'un des quatres présentés dans cette réponse.

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "aa78d8c571028a699fd2a62e1f7c71c3", "grade": false, "grade_id": "cell-fa922f17285fd65e", "locked": true, "schema_version": 3, "solution": false, "task": false}}

3.  Indiquez dans `speciesMax` le nom scientifique de l'espèce la plus
    observée. Quel est son nom dans le langage courant?

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 24db69ae408f05f1c9d3b9046ad4eea4
  grade: false
  grade_id: cell-3d211c55f2065e25
  locked: false
  schema_version: 3
  solution: true
  task: false
---
species1 = observations.groupby("scientific_name")["observations"].sum()
species1 = species1.sort_values(ascending = False).reset_index()
speciesMax = species1["scientific_name"][0]
speciesMax
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: e035c20ef8cc1b0ca080fb9ff31d8555
  grade: true
  grade_id: cell-03dc827892b56b10
  locked: true
  points: 2
  schema_version: 3
  solution: false
  task: false
---
assert hashlib.md5(speciesMax.encode("utf-8")).hexdigest() == 'eb0f1d26ae3ad8053d8291c2c24ad349'
```

+++ {"deletable": false, "nbgrader": {"cell_type": "markdown", "checksum": "c5ee626604f6ea8ad58bc427465509e1", "grade": true, "grade_id": "cell-629835ce3100c1ba", "locked": false, "points": 0, "schema_version": 3, "solution": true, "task": false}}

Son nom commun est la tourterelle turque!

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "1e5442b712e07b333a002dccff305b86", "grade": false, "grade_id": "cell-aeea34fbde0ece03", "locked": true, "schema_version": 3, "solution": false, "task": false}}

4.  Mettez dans l'objet `parkMax` le nom du parc dans lequel on trouve
    le plus d'observations.

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: c8c052c7eeccdf774a74759cf2792102
  grade: false
  grade_id: cell-b5754761e45f4156
  locked: false
  schema_version: 3
  solution: true
  task: false
---
park = observations.groupby("park_name")["observations"].sum()
park = park.sort_values(ascending = False).reset_index()
parkMax = park["park_name"][0]

parkMax
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 94f05228a7d1fdefe7297caf0d3d92cf
  grade: true
  grade_id: cell-d1d16aef256c4519
  locked: true
  points: 1
  schema_version: 3
  solution: false
  task: false
---
assert hashlib.md5(parkMax.encode("utf-8")).hexdigest() == '5702641a382396a92985fd8020739b63'
```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "38b9bb5798ccb4f1542c220ec0bcfb87", "grade": false, "grade_id": "cell-2239335d40bf00dc", "locked": true, "schema_version": 3, "solution": false, "task": false}}

## Analyse des données

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "5421763bb4240f69fea0e49017922996", "grade": false, "grade_id": "cell-2239335d40bf00dd", "locked": true, "schema_version": 3, "solution": false, "task": false}}

La première étape est de nettoyer et préparer les données.

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "451273024316050ce6a38648c316c09b", "grade": false, "grade_id": "cell-5e82c6617c8b8e03", "locked": true, "schema_version": 3, "solution": false, "task": false}}

1.  Y a-t-il des valeurs manquantes dans la table `observations`?
    Supprimez les lignes avec des données manquantes et stockez le
    résultat dans `observations_cleaned`

```{code-cell} ipython3
---
deletable: false
editable: true
nbgrader:
  cell_type: code
  checksum: e5b0cc86615b2c080c998464c74c452e
  grade: false
  grade_id: cell-cdbcc205f67fcc29
  locked: false
  schema_version: 3
  solution: true
  task: false
slideshow:
  slide_type: ''
---
"""
Oui il y a des données manquantes (5) dans la table "observations"
"""
observations_cleaned = observations.dropna()
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: ef170be33bcff63bed13f4cf8c34b3f8
  grade: true
  grade_id: cell-08c0e8b6aa9253d0
  locked: true
  points: 1
  schema_version: 3
  solution: false
  task: false
---
assert observations_cleaned.shape == (23291, 3)
assert (observations_cleaned.notna()).all(axis=None)
```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "be6af578ade44ef905ad4f0cb49cb8e0", "grade": false, "grade_id": "cell-16c3c3af53c81683", "locked": true, "schema_version": 3, "solution": false, "task": false}}

2.  Dans la colonne `conservation_status` de la table `species`,
    remplacez les valeurs `NaN` par `No Intervention`. En effet, `NaN`
    signifie qu'il n'y a pas de spécification de conservation.

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 049253136ca4b09bc8f9d38a2068e82d
  grade: false
  grade_id: cell-17dee0f233f349d3
  locked: false
  schema_version: 3
  solution: true
  task: false
---
species["conservation_status"] = species["conservation_status"].fillna('No Intervention')
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: bbeedb2dece41e6b14e6bb021899d2ca
  grade: true
  grade_id: cell-cafe9e81ddc3a8d9
  locked: true
  points: 1
  schema_version: 3
  solution: false
  task: false
---
assert (species.notna()).all(axis=None)
```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "e9673fd5f775ac9395df337d15b17f95", "grade": false, "grade_id": "cell-417062c5e904254e", "locked": true, "schema_version": 3, "solution": false, "task": false}}

On calcule ensuite, sous la forme d'un tableau, le nombre d'espèces
pour chaque catégorie et chaque statut de conservation dans l'objet
`group_status`. Pour cela, on utilise *entre autres* les fonctions
`groupby()` et
[`unstack()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.unstack.html).

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 01a561f3f672a43a1e222ce40f61b1a0
  grade: false
  grade_id: cell-a5f0857ce456285d
  locked: true
  schema_version: 3
  solution: false
  task: false
---
group_status = species.groupby('category')['conservation_status'].value_counts().unstack()
```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "2a088849fcf85e0b18294e9bdc57a9d3", "grade": false, "grade_id": "cell-9965320addb512f5", "locked": true, "schema_version": 3, "solution": false, "task": false}}

3.  ♣ Faites une figure pour représenter ces données sous forme de
    carte de chaleur (heatmap).

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 1062a91643d21970838e7a5573ccfc0b
  grade: false
  grade_id: cell-caf28f30ccf1d0a2
  locked: false
  schema_version: 3
  solution: true
  task: false
---
group_status.style.background_gradient(cmap = "coolwarm", axis = None)
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: ba7772f06a4af0899479cebafacde918
  grade: true
  grade_id: cell-93cded1e48fd2a48
  locked: true
  points: 2
  schema_version: 3
  solution: false
  task: false
---
assert isinstance(group_status, pd.DataFrame)
assert group_status.shape == (7,5)
```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "66174c589018454e964fb94aa45d9518", "grade": false, "grade_id": "cell-8c36c732f4c67a98", "locked": true, "schema_version": 3, "solution": false, "task": false}}

4.  À quoi sert l'opération `unstack`?

+++ {"deletable": false, "nbgrader": {"cell_type": "markdown", "checksum": "82eb159e87fbee1276cbcc8e9a5d44a3", "grade": true, "grade_id": "cell-5f4bbe18f55d8e26", "locked": false, "points": 1, "schema_version": 3, "solution": true, "task": false}}

Elle fait partie des opérations de manipulation de données et est utilisée pour inverser l'effet de la fonction stack() (qui regroupe tout en un). En outre, elle fait pivoter un niveau des étiquettes d'index et retourne un DataFrame ayant un nouveau niveau de libellés de colonnes dont le niveau le plus proche est constitué des libellés d'index pivotés.

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "30bd6b31e8ae0db60cdb165fd66478fc", "grade": false, "grade_id": "cell-335905fb91e7c7d4", "locked": true, "schema_version": 3, "solution": false, "task": false}}

5.  ♣ Dans la pratique, la plupart des espèces sont sans intervention,
    notamment pour les plantes. Refaites la figure en éliminant les
    espèces sans intervention. Gardez le nombre d'espèces par
    catégorie et par statut de conservation dans l'objet
    `group_status_conserv`.

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: bc5425375456daec4516b716a6bcd100
  grade: false
  grade_id: cell-a6b086aba96f28b1
  locked: false
  schema_version: 3
  solution: true
  task: false
---
species_no_intervention = species.loc[species["conservation_status"] != "No Intervention", :]
group_status_conserv = species_no_intervention.groupby('category')['conservation_status'].value_counts().unstack()
group_status_conserv.style.background_gradient(cmap = "coolwarm", axis = None)
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 8d86104500ea512d0c6e0ca180c11e19
  grade: true
  grade_id: cell-5103410b77ba6bb2
  locked: true
  points: 1
  schema_version: 3
  solution: false
  task: false
---
assert isinstance(group_status_conserv, pd.DataFrame)
assert group_status_conserv.shape == (7,4)
```

6.  ♣ Faites une nouvelle figure à partir de `group_status_conserv` de
    type barplot avec l'option `stacked=True`.

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: f82a6d04fe3ebdd561576352bc0a36f3
  grade: false
  grade_id: cell-94ac4fae78de88b4
  locked: false
  schema_version: 3
  solution: true
  task: false
---
group_status_conserv.plot(kind='bar', stacked = True)
plt.xlabel("Catégories")
plt.ylabel('Total')
plt.title("Comparaison du statut de conservation des espèces selon leur catégorie");
```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "0982097dcfc20d6709f4e6c8aa219754", "grade": false, "grade_id": "cell-72d383ef1fc09d26", "locked": true, "schema_version": 3, "solution": false, "task": false}}

7.  A l'aide des analyses précédentes, répondez aux questions
    initiales en quelques phrases:

    - Quelle est la répartition des statuts de conservation des espèces?
    - Quel type d'être vivant est particulièrement en danger?

Commentez vos figures.

+++ {"deletable": false, "nbgrader": {"cell_type": "markdown", "checksum": "aaef064fe59059fd890c31afd3b511c8", "grade": true, "grade_id": "cell-32dc56440ea62824", "locked": false, "points": 4, "schema_version": 3, "solution": true, "task": false}}

Toutes les espèces étudiées sont au moins catégorisées comme "espèces concernées" mais ne sont pas toutes exposées équitablement au danger. Effectivement, on observe différents taux des statuts de conservation en fonction des espèces:

Chez les amphibiens qui nécessite une intervention, plus de la moitié sont des espèces concernées, tandis que 1/3 sont menacés et 1/6 sont en danger.

Chez les oiseaux, on constate qu'une grande majorité des espèces nécessitant une intervention sont également considérées comme espèces concernées, que 3% d'entre eux sont menacés, mais que cependant 3% sont en voie de rétablissement. 

Chez les mammifères on observe les mêmes catégories de statuts de conservation mais dans différentes proportion, en effet, la part des espèces en voie de rétablissement est plus faible, tandis que celle des espèces en danger est plus forte, par rapport à la part des espèces concernées, qui a, elle, diminuée. De plus une petite partie des mammifères nécessitant une intervention est aussi menacée.

Chez les poissons, les espèces observées se regroupent en trois catégories de stauts de conservation à peu près de part égales: ils sont menacées, en danger et sont également considérés comme espèces concernées.

On retrouve ces mêmes catégories de statuts de conservation chez les plantes vasculaires mais dans des proportions bien différentes. En effet, on constate une écrasante majoritée des plantes vasculaires nécéssitant une intervention est considérée comme concernée, tandis qu'une petite partie de ces plantes est menacée ou en danger.

Enfin, les reptiles et plantes non-vasculaires nécéssitant une intervention sont à 100% considérés comme des espèces concernées.

Nous avons pu comparer la part de chaque statut de conservation dans les espèces protégées, cependant il serait judicieux de garder les espèces catégorisées "No intervention" afin de calculer la part des espèces protégées dans la population totale de la catégorie.

Les mammifères, les oiseaux et les poissons sont particulièrement en danger. En effet, on remarque sur le graphe sont les catégories d'animaux qui ont les parts d'espèces en danger les plus importantes. On compte environ 8% des mammifères étudiés en danger, 5% des oiseaux et 4% des poissons (part bleue du graphique).

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "4af8484a09773b5ed8698dfcc5958dae", "grade": false, "grade_id": "cell-710ee6a737849476", "locked": true, "schema_version": 3, "solution": false, "task": false}}

#### Conservation

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "886462c83af8c1330143173983b09849", "grade": false, "grade_id": "cell-710ee6a737849477", "locked": true, "schema_version": 3, "solution": false, "task": false}}

On passe maintenant à la question : quelles sont les espèces
plus susceptibles d'être suivies dans le cadre de la conservation?

-   Créez une nouvelle colonne `is_protected` qui vaut `False` pour
    toutes les espèces de statut `No Intervention` et `True` pour les
    autres.

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 5f1e0d78909c3d904191b945da4522ab
  grade: false
  grade_id: cell-57e10ff3a863b08a
  locked: false
  schema_version: 3
  solution: true
  task: false
---
species["is_protected"] = species["conservation_status"] != "No Intervention"  
species
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 1de62723abe34dd2c94cb44136766f34
  grade: true
  grade_id: cell-5d7e2ea1de145b45
  locked: true
  points: 1
  schema_version: 3
  solution: false
  task: false
---
assert species.shape == (5824, 5)
assert species['is_protected'].mean() < 0.033
```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "0a2b965cd699f7abb72256ddb72e7b56", "grade": false, "grade_id": "cell-a9dfe0ed57a1b03e", "locked": true, "schema_version": 3, "solution": false, "task": false}}

Pour chaque catégorie calculez la proportion d'espèces protégées et
mettez le resultat dans la variable `prop_cat`. Observez les
résultats.

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: fc8416e3045bbf898b69778c0d2cff09
  grade: false
  grade_id: cell-d1863498f50a9e5e
  locked: false
  schema_version: 3
  solution: true
  task: false
---
total = species.groupby(["category"])["conservation_status"].count()
protected = species.loc[species["is_protected"] == True, :]
is_Protected = protected.groupby(["category"])["conservation_status"].count()

prop_cat = is_Protected/total

prop_cat
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 1db3207d274c440f545ff446d3fa643a
  grade: true
  grade_id: cell-6cdcfc4eec90da41
  locked: true
  points: 2
  schema_version: 3
  solution: false
  task: false
---
assert prop_cat.mean() == 0.08455896094419532
assert prop_cat.max() == 0.17757009345794392
```

Les grandes familles d'espèces les plus protégées, avec un taux de protection respectif de 17,8% et 15,2% sont les mammifères et les oiseaux. Les grandes familles d'espèces les moins protégées, avec un taux de protection respectif de 06,3%, 08,7% et 08,8% sont les reptiles, les poissons et amphibiens.

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "f5f0b37403bd21246a41f26659470986", "grade": false, "grade_id": "cell-a08ac62e82d594db", "locked": true, "schema_version": 3, "solution": false, "task": false}}

## Évaluation

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "279454da0ed3e432fa5dc1568df54822", "grade": false, "grade_id": "cell-a08ac62e82d594dc", "locked": true, "schema_version": 3, "solution": false, "task": false}}

Nous avons fini cette première partie de ce cours consacré aux
analyses de données, avec un accent mis sur la \[VI\]sualisation des
données. La semaine prochaine et jusqu'à fin du cours nous nous
concentrerons sur la classification d'image par apprentissage
statistiques, avec le schema d'analyse complet: VI-MÉ-BA-BAR.

Ce TP a permis d'analyser la composition en être vivants de quatre
parcs nationaux.

-  Répondez de facon succinte aux questions du début du TP:

      -   Quel animal est le plus répandu ? Quel parc possède le plus d'espèces ?
      -   Quelle est la répartition des statuts de conservation des espèces ?
      -   Certains types d'espèces sont-ils plus susceptibles d'être menacés ?

+++ {"deletable": false, "nbgrader": {"cell_type": "markdown", "checksum": "c929ab4ed24575ae3977d9adf9a00c72", "grade": true, "grade_id": "cell-4392623430a050aa", "locked": false, "points": 2, "schema_version": 3, "solution": true, "task": false}}

L'animal le plus répandu est la tourterelle turque. Le parc possédant le plus d'espèces est le parc Yellowstone.

Toutes les espèces étudiées sont au moins catégorisées comme "espèces concernées" mais ne sont pas toutes exposées équitablement au danger. Effectivement, on observe différents taux des statuts de conservation en fonction des espèces:

Chez les amphibiens qui nécessite une intervention, plus de la moitié sont des espèces concernées, tandis que 1/3 sont menacés et 1/6 sont en danger.

Chez les oiseaux, on constate qu'une grande majorité des espèces nécessitant une intervention sont également considérées comme espèces concernées, que 3% d'entre eux sont menacés, mais que cependant 3% sont en voie de rétablissement. 

Chez les mammifères on observe les mêmes catégories de statuts de conservation mais dans différentes proportion, en effet, la part des espèces en voie de rétablissement est plus faible, tandis que celle des espèces en danger est plus forte, par rapport à la part des espèces concernées, qui a, elle, diminuée. De plus une petite partie des mammifères nécessitant une intervention est aussi menacée.

Chez les poissons, les espèces observées se regroupent en trois catégories de stauts de conservation à peu près de part égales: ils sont menacées, en danger et sont également considérés comme espèces concernées.

On retrouve ces mêmes catégories de statuts de conservation chez les plantes vasculaires mais dans des proportions bien différentes. En effet, on constate une écrasante majoritée des plantes vasculaires nécéssitant une intervention est considérée comme concernée, tandis qu'une petite partie de ces plantes est menacée ou en danger.

Enfin, les reptiles et plantes non-vasculaires nécéssitant une intervention sont à 100% considérés comme des espèces concernées.

Les grandes familles d'espèces les plus protégées, avec un taux de protection respectif de 17,8% et 15,2% sont les mammifères et les oiseaux. En effet, étant les types d'espèces plus protégées, on peut en déduire qu'elles sont les plus susceptibles d'être menacés.

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "3769fd2cfb961a6e61e3dcd7bba6287d", "grade": false, "grade_id": "cell-40da5b7e37b2d8cb", "locked": true, "schema_version": 3, "solution": false, "task": false}, "slideshow": {"slide_type": ""}}

:::{admonition} Obtenez votre score

Bravo, vous avez fini le TP.

Depuis le tableau de bord :

1.  Déposez votre travail

2.  Vérifiez que votre binôme est bien configuré, et notamment que le
    dépôt principal, marqué par une étoile, est le bon. Au besoin,
    consultez la [page web](https://nicolas.thiery.name/Enseignement/IntroScienceDonnees/ComputerLab/travailBinome.html#indiquer-a-gitlab-que-vous-travaillez-en-binome)  
    **Rappel:** nous ne corrigerons que votre dépôt principal, tel que
    vous l'avez configuré!

3.  Consultez votre score.  
    **Rappel: ** le calcul du score peut prendre quelques
    minutes. Vous devez relancer le tableau de bord, ou consultez le
    dépôt pour le voir. Cliquez sur le badge avec le score pour avoir
    les détails et les commentaires. Les commentaires et donc le score
    total sera mis à jour seulement après correction par votre
    enseignant ou enseignante.

:::
