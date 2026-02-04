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

+++ {"deletable": false, "editable": false, "nbgrader": {"locked": true, "schema_version": 3, "checksum": "9cbb94a197161f9a51004766ab6ee0ab", "solution": false, "cell_type": "markdown", "grade": false, "grade_id": "cell-63247b06949c9806"}}

# Manipuler des tableaux

Dans cette feuille, vous allez apprendre à effectuer quelques
manipulations simples sur les tableaux, comme nous l'avions fait au
premier semestre avec les `vector` de C++. En Python, de tels tableaux
peuvent être représentés par les `array` de la bibliothèque `NumPy`
(usuellement abrégée en `np`):

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 9c3f5d5cb593afc848b35bc23508bf55
  grade: false
  grade_id: cell-8c751ead7518dfea
  locked: true
  schema_version: 3
  solution: false
---
import numpy as np
```

+++ {"editable": false, "deletable": false, "nbgrader": {"task": false, "grade": false, "checksum": "4fd5cbde5429f2982ac9751f22b2c960", "cell_type": "markdown", "grade_id": "cell-1063495ca725c5db", "locked": true, "schema_version": 3, "solution": false}}

## Tableaux à deux dimensions

+++ {"editable": false, "deletable": false, "nbgrader": {"cell_type": "markdown", "grade_id": "cell-9a29dafa51e1a013", "schema_version": 3, "grade": false, "checksum": "dfac298c8c6c4f205299b6cf310a5a53", "locked": true, "solution": false}}

Voilà un tableau à deux dimensions avec deux lignes et quatre
colonnes:

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 7cb43c2d929cc16a479bb323b5797149
  grade: false
  grade_id: cell-67fbacbcf94193b3
  locked: true
  schema_version: 3
  solution: false
---
T = np.array([[1, 2, 3, 4], 
              [5, 6, 7, 8]])
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 722eb1481c7f22f0a26b7b3a9b865113
  grade: false
  grade_id: cell-845dffdd69e40937
  locked: true
  schema_version: 3
  solution: false
---
T
```

+++ {"nbgrader": {"grade_id": "cell-47cf516abf13b964", "locked": true, "solution": false, "schema_version": 3, "grade": false, "cell_type": "markdown", "checksum": "e4b026ff926e4239ba5644d1dcd6dc9b"}, "deletable": false, "editable": false}

On peut retrouver les tailles de ce tableau avec:

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: a1ab35b251b03547c95bf160ea2bcdd1
  grade: false
  grade_id: cell-3cfd6fa0623e6f98
  locked: true
  schema_version: 3
  solution: false
---
T.shape
```

+++ {"editable": false, "nbgrader": {"locked": true, "solution": false, "checksum": "e305214dc702325e2f921792f8b094cc", "grade": false, "task": false, "cell_type": "markdown", "grade_id": "cell-c8f6590b61b5b276", "schema_version": 3}, "deletable": false}

Vous vous rappellez que les `vector` de C++ sont intrinsèquement des
tableaux à une dimension, et que l'on émule des tableaux à deux
dimensions avec des tableaux de tableaux. Ici, en revanche, les
tableaux `array` de numpy permettent de construire explicitement des
tableaux à deux dimensions.

+++ {"deletable": false, "editable": false, "nbgrader": {"solution": false, "grade_id": "cell-3080232c4e2fb2df", "grade": false, "cell_type": "markdown", "schema_version": 3, "checksum": "d58be99019c2400301bd7469b039b712", "locked": true}}

### Exercice

1. Construire un tableau à trois lignes et trois colonnes, contenant
   les entiers de 1 à 9 de gauche à droite et de haut en bas comme
   dans la figure suivante:

       1 2 3
       4 5 6
       7 8 9

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: bbd5ec8d0c08d45e4cec41fcff437281
  grade: false
  grade_id: cell-1b95ffffb22a38df
  locked: false
  schema_version: 3
  solution: true
  task: false
---
T2 = np.array([[1, 2, 3],
              [4, 5, 6], 
              [7, 8, 9]])
T2
```

+++ {"deletable": false, "nbgrader": {"cell_type": "markdown", "checksum": "ff72beaf7319a2e76325912258108167", "grade": false, "grade_id": "cell-f23c45dc4abb2118", "locked": true, "schema_version": 3, "solution": false}, "editable": false}

Nous testons la forme du tableau:

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: b192f2c627830d51a9eb10a8c6129145
  grade: true
  grade_id: cell-3dcbc34e7cdb8449
  locked: true
  points: 1
  schema_version: 3
  solution: false
  task: false
---
assert T2.shape == (3,3)
```

+++ {"deletable": false, "nbgrader": {"task": false, "checksum": "473b88c0c5b7366dc6b1e1e94fbccb3c", "schema_version": 3, "grade": false, "solution": false, "locked": true, "grade_id": "cell-96fc71e105b68b41", "cell_type": "markdown"}, "editable": false}

ainsi que son contenu :

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 63a383e3e61ecdd20d9bd680c6e7da1f
  grade: true
  grade_id: cell-b2598336ebcb56a2
  locked: true
  points: 1
  schema_version: 3
  solution: false
  task: false
---
assert [ T2[i,j] for i in range(3) for j in range(3) ] == [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

+++ {"nbgrader": {"checksum": "49c89155418473f66a056b5b7adb683e", "grade": false, "grade_id": "cell-21d9a01c8d293ab4", "schema_version": 3, "cell_type": "markdown", "locked": true, "solution": false}, "editable": false, "deletable": false}

Voici comment accéder au contenu d'une case individuelle du tableau :

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 2e3a1fee5a81d040573266cd4298f663
  grade: false
  grade_id: cell-9ca4451347ad1cf3
  locked: true
  schema_version: 3
  solution: false
---
T2[1,2]
```

+++ {"editable": false, "deletable": false, "nbgrader": {"checksum": "9c20aba9730f7d17da5db1b67008e594", "schema_version": 3, "solution": false, "grade": false, "cell_type": "markdown", "grade_id": "cell-0c42459abbd31832", "locked": true}}

Cette case est en deuxième ligne et troisième colonne: en effet, comme
en C++, les lignes et colonnes sont numérotées à partir de 0.

+++ {"deletable": false, "nbgrader": {"checksum": "24865da222354aead7858ac2ff96a31f", "grade_id": "cell-4aaf4afc9dab0881", "cell_type": "markdown", "locked": true, "schema_version": 3, "solution": false, "grade": false}, "editable": false}

Si l'on veut extraire toute une ligne, ou toute une colonne, on
remplace la coordonnée que l'on ne veut pas spécifier par `:`.

Voici donc la deuxième colonne :

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: f4c04730adc08ef40ade35dbf4e632c9
  grade: false
  grade_id: cell-6c91767340cf600d
  locked: true
  schema_version: 3
  solution: false
---
T2[:,1]
```

+++ {"nbgrader": {"grade": false, "grade_id": "cell-7d229a134ec6e867", "checksum": "d177fd337e84b0dea7f5a3b54a88c995", "task": false, "cell_type": "markdown", "solution": false, "locked": true, "schema_version": 3}, "deletable": false, "editable": false}

Extraire la deuxième ligne du tableau, et affectez-la à la variable
`li` dont vous afficherez le contenu :

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: dfe34b98659f9c914a7630187e43cdbf
  grade: false
  grade_id: cell-0abb1396ab91dd95
  locked: false
  schema_version: 3
  solution: true
  task: false
---
li = T2[1, :]
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: a512644996d219eb143ad64c92f8d748
  grade: true
  grade_id: cell-e22d3374bf68c443
  locked: true
  points: 1
  schema_version: 3
  solution: false
  task: false
---
assert isinstance(li, np.ndarray)
assert li.shape == (3,)
assert list(li) == [4,5,6]
```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "grade_id": "cell-990adcd36c40f8df", "locked": true, "schema_version": 3, "solution": false, "checksum": "efe72d3af5c1d8c82950b20179f3219d", "grade": false}}

## Tableaux à trois dimensions et plus

Pour le moment, nous avons utilisé des tableaux à deux dimensions.
Ultérieurement, notamment pour représenter des images, nous aurons
besoin de tableaux de plus grande dimension: un seul nombre ne suffit
en effet pas pour représenter un pixel.

`Numpy` permet de représenter des tableaux de toute dimension. Voici
un tableau de dimension 3 :

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 70013da6dfca06661b1d7226a04b209b
  grade: false
  grade_id: cell-34c03e0c6027d356
  locked: true
  schema_version: 3
  solution: false
---
T3D = np.array([[[ 1, 2, 3], [ 4, 5, 6], [ 7, 8, 9]],
                [[10,11,12], [13,14,15], [16,17,18]],
                [[19,20,21], [22,23,24], [25,26,27]]
                ])
```

+++ {"nbgrader": {"grade_id": "cell-796c1a5acbab1a2b", "cell_type": "markdown", "checksum": "8cd120c43caead46c5035b87c0991792", "grade": false, "locked": true, "solution": false, "schema_version": 3}, "deletable": false, "editable": false}

On peut le voir comme un tableau à trois couches :

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: eca7db301fc9a95c1223a90acbc9b073
  grade: false
  grade_id: cell-574e34b7ddc4509d
  locked: true
  schema_version: 3
  solution: false
---
T3D[:,:,0]
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: f527ab4e84f78ff2b0df65dd2ac0ea79
  grade: false
  grade_id: cell-dd11d154ece1aef6
  locked: true
  schema_version: 3
  solution: false
---
T3D[:,:,1]
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: c47db6d04707bcb3049d977cde2178c5
  grade: false
  grade_id: cell-a96fbf7480f42e12
  locked: true
  schema_version: 3
  solution: false
---
T3D[:,:,2]
```

+++ {"editable": false, "deletable": false, "nbgrader": {"checksum": "3f92c7990d33b386dc695fcf865ad270", "locked": true, "schema_version": 3, "solution": false, "grade": false, "grade_id": "cell-414edd834755639f", "cell_type": "markdown"}}

### Exercices

Extraire la première colonne de la deuxième couche de `T3D` et
stockez-la dans la variable `C`:

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 97c5408e00c8fbe1f2ac085b1411f6fd
  grade: false
  grade_id: cell-81c4d71ee7b1a54d
  locked: false
  schema_version: 3
  solution: true
  task: false
---
C = T3D[:, 0, 1]
```

+++ {"nbgrader": {"cell_type": "markdown", "solution": false, "schema_version": 3, "checksum": "863f9bb7af6a9d13186db7a08a232f59", "locked": true, "grade_id": "cell-868c3302aea64357", "grade": false, "task": false}, "deletable": false, "editable": false}

Notez que c'est un tableau à une dimension, donc noté en ligne !

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 48f39bf8c72b94c70a2e41d6a073957f
  grade: true
  grade_id: cell-995993eb1b58947e
  locked: true
  points: 1
  schema_version: 3
  solution: false
  task: false
---
assert list(C) == [2, 11, 20]
```

+++ {"editable": false, "nbgrader": {"checksum": "a6138439f3cd75d8b58a51df5dc3f706", "locked": true, "schema_version": 3, "solution": false, "cell_type": "markdown", "grade": false, "grade_id": "cell-414edd834755639g"}, "deletable": false}

Maintenant, extraire un tableau contenant la première colonne de chacune des trois
couches de `T3D` et stockez le dans la variable `C`. Notez que l'on
souhaite que ces colonnes soient bien représentées par des colonnes
dans `C` !

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 32e2460e22a0bfc0e5f0697e3caaf400
  grade: false
  grade_id: cell-abdcb2e9f73d6dfe
  locked: false
  schema_version: 3
  solution: true
  task: false
---
C = T3D[:, 0, :]
C
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: e262f80868e4db8544ea173aa973639d
  grade: true
  grade_id: cell-3227c3665bd8802d
  locked: true
  points: 1
  schema_version: 3
  solution: false
  task: false
---
for i in range(3):
    assert np.array_equal(T3D[:,0,i], C[:,i])
```

+++ {"deletable": false, "nbgrader": {"grade_id": "cell-72625c162aacdd71", "locked": true, "schema_version": 3, "solution": false, "checksum": "1ffe30a723d426e49c74c47beaf4a081", "cell_type": "markdown", "grade": false}, "editable": false}

## Statistiques simples sur les tableaux

+++ {"editable": false, "nbgrader": {"locked": true, "solution": false, "grade": false, "grade_id": "cell-202df26286a5b3a0", "cell_type": "markdown", "checksum": "8793482aa66bacbd22022602cc17dc9a", "schema_version": 3}, "deletable": false}

Numpy permet de faire des statistiques simples sur les
tableaux. Revenons à notre tableau `T` :

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: ecaceef2f7991bf4795869507af9995c
  grade: false
  grade_id: cell-e979baa940e6f47e
  locked: true
  schema_version: 3
  solution: false
---
T
```

+++ {"deletable": false, "nbgrader": {"locked": true, "checksum": "f6b580f520d20d84d004d9c7f40527f3", "grade_id": "cell-29729cfe62afb790", "schema_version": 3, "grade": false, "cell_type": "markdown", "solution": false, "task": false}, "editable": false}

Calculez à la main :
- la moyenne de chaque colonne de `T`;
- la moyenne de chaque ligne de `T`;
- la moyenne de tous les éléments du tableau `T`.

```{code-cell} ipython3
Mc = []
for i in range(len(T[0])):
    s = 0
    for e in T[:, i]:
        s += e 
    moy = s/len(T[:, i])
    Mc.append(moy)

print(Mc)
# la moyenne de chaque colonne de `T`: Mc = [3.0, 4.0, 5.0, 6.0]


Ml = []
for i in range(len(T)):
    s = 0
    for e in T[i, :]:
        s += e 
    moy = s/len(T[i, :])
    Ml.append(moy)

print(Ml)
# la moyenne de chaque ligne de `T`: Ml = [2.5, 6.5]


M = 0
s = 0
for i in range(len(T)):
    for j in range(len(T[0])):
        s += T[i, j]
M = s/(len(T[0, :])*len(T[:, 0]))

print(M)
# la moyenne de tous les éléments du tableau `T`: M = 
```

+++ {"nbgrader": {"grade": false, "cell_type": "markdown", "task": false, "locked": true, "schema_version": 3, "grade_id": "cell-00194d77bb863b7b", "solution": false, "checksum": "53a292fef70b49201d991c1f6784e251"}, "deletable": false, "editable": false}

Comparez vos résultats avec ceux des calculs suivants. Que calcule chaque commande ci-dessous?

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 89b3d8b2479b327aa146156f95a8b494
  grade: false
  grade_id: cell-1d9fbab6c508d984
  locked: true
  schema_version: 3
  solution: false
---
T.mean(axis=0)
```

On voit que les résultats obtenus manuellement sont les mêmes que ceux obtenus grâce à cette commande. 
Cette commande renvoie la liste des moyennes de chaque colonne.
On constate cependant qu'il n'y a pas les 0 après la virgule avec cette commande: c'est sûrement dû à la façon de calculer de la fonction mean.

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: d827b580b4fba02c8bee40488cc9b296
  grade: false
  grade_id: cell-899594980a692e52
  locked: true
  schema_version: 3
  solution: false
---
T.mean(axis=1)
```

On voit que les résultats obtenus manuellement sont les mêmes que ceux obtenus grâce à cette commande. 
Cette commande renvoie la liste des moyennes de chaque ligne.

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 33c3909e4504c6f5be8c28f7b79e6e5a
  grade: false
  grade_id: cell-e979baa940e6f47d
  locked: true
  schema_version: 3
  solution: false
---
T.mean()
```

+++ {"tags": []}

On obtient exactement la même moyenne globale manuellement.
Cette commande renvoie la moyenne des valeurs du tableau.

+++ {"deletable": false, "editable": false, "nbgrader": {"locked": true, "grade": false, "task": false, "grade_id": "cell-7fc37d52040f3366", "checksum": "4912757b09b55f784ea8777f5ceb0f76", "cell_type": "markdown", "schema_version": 3, "solution": false}}

## Conclusion

Voilà vous avez vu tous les éléments de manipulation des tableaux
`NumPy` dont nous aurons besoin aujourd'hui.
