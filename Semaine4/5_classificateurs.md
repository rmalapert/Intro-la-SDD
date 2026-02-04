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

+++ {"deletable": false, "nbgrader": {"grade": false, "solution": false, "checksum": "afbf34e64e7471cefc07d9483063e7a8", "cell_type": "markdown", "grade_id": "cell-3876f910a24fe8a7", "locked": true, "schema_version": 3}, "editable": false}

# Classificateurs

+++ {"nbgrader": {"cell_type": "markdown", "grade_id": "cell-65c63f4be1820d2e", "task": false, "schema_version": 3, "solution": false, "locked": true, "checksum": "f1092a8183464dd2e0b498c1337d6626", "grade": false}, "editable": false, "deletable": false}

Dans cette feuille, nous allons explorer l'utilisation de plusieurs
classificateurs sur l'exemple des pommes et des bananes. Vous pourrez
ensuite les essayer sur votre jeu de données.

Commencons par charger les utilitaires et autres librairies:

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 1ea7da415eef57aad06d2165be7ca4ff
  grade: true
  grade_id: cell-a2ccd1e13b05762c
  locked: false
  points: 0
  schema_version: 3
  solution: true
  task: false
---
from intro_science_donnees import *
## les utilitaires
%load_ext autoreload
%autoreload 2
from utilities import *
# Graphs and visualization library
import seaborn as sns; sns.set()      
# Configuration intégration dans Jupyter
%matplotlib inline
```

+++ {"nbgrader": {"checksum": "d880dfccac7e7bb362d70f774bf09ebb", "schema_version": 3, "grade_id": "cell-875bebae978f4f5a", "solution": false, "cell_type": "markdown", "locked": true, "grade": false, "task": false}, "editable": false, "deletable": false}

## Chargement et préparation des données

+++ {"editable": false, "deletable": false, "nbgrader": {"checksum": "b4c6e614484358e629bcce73f49598b8", "grade": false, "cell_type": "markdown", "task": false, "locked": true, "grade_id": "cell-ce0810dd4273ea24", "schema_version": 3, "solution": false}}

On charge le jeu de données prétraité (attributs rougeur et élongation
et classes des fruits). Cela correspond au fichier `attributs.csv` utilisé en Semaine3 :

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 66b99e424464516b2382bde9cbcbc8de
  grade: false
  grade_id: cell-e6d48e8b35c2d42a
  locked: true
  schema_version: 3
  solution: false
  task: false
---
df = pd.read_csv("../Semaine3/attributs.csv", index_col=0)
df
# standardisation
dfstd =  (df - df.mean()) / df.std()
dfstd['class'] = df['class']
```

+++ {"editable": false, "nbgrader": {"cell_type": "markdown", "schema_version": 3, "grade": false, "solution": false, "checksum": "5e412381817b3618b8a7a7ca91094d61", "task": false, "locked": true, "grade_id": "cell-24210bafac4b3490"}, "deletable": false}

On partitionne le jeu de données en ensemble de test et
d'entraînement:

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 153d7ed14b1d95db0e1a369c646c7c44
  grade: false
  grade_id: cell-00b3f36097ec9704
  locked: true
  schema_version: 3
  solution: false
  task: false
---
X = dfstd[['redness', 'elongation']]
Y = dfstd['class']
#partition des images
train_index, test_index = split_data(X, Y, seed=3)

#partition de la table des attributs
Xtrain = X.iloc[train_index]
Xtest = X.iloc[test_index]
#partition de la table des étiquettes
Ytrain = Y.iloc[train_index]
Ytest = Y.iloc[test_index]
```

+++ {"nbgrader": {"cell_type": "markdown", "checksum": "741881738d7829a3b3d62de9cceb02f2", "task": false, "solution": false, "schema_version": 3, "grade_id": "cell-9e3cf5dde27fbb00", "locked": true, "grade": false}, "editable": false, "deletable": false}

## Classificateurs basés sur les exemples (*examples-based*)

+++ {"nbgrader": {"cell_type": "markdown", "grade_id": "cell-f7f11edbe1e13a7d", "task": false, "solution": false, "grade": false, "locked": true, "checksum": "45db778c1166c64282fd7d8e20a03d1b", "schema_version": 3}, "deletable": false, "editable": false}

Nous allons maintenant voir comment appliquer des classificateurs
fournis par la librairie `scikit-learn`.  Commençons par le
classificateur plus proche voisin déjà vu en semaines 3 et 4.

+++ {"editable": false, "deletable": false, "nbgrader": {"solution": false, "locked": true, "schema_version": 3, "checksum": "40979d861a5204582bc97eca2e359c92", "grade_id": "cell-28f37cb9aba3b0d6", "grade": false, "cell_type": "markdown", "task": false}}

### KNN : $k$-plus proche voisins

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: eedf6344cb5b6474a8330504c426b4c8
  grade: false
  grade_id: cell-786fada559c8c5e0
  locked: true
  schema_version: 3
  solution: false
  task: false
---
from sklearn.neighbors import KNeighborsClassifier

#définition du classificateur, ici on l'appelle classifier
# on choisit k=1
classifier = KNeighborsClassifier(n_neighbors=1)
# on l'ajuste aux données d'entrsainement
classifier.fit(Xtrain, Ytrain) 
# on calcule ensuite le taux d'erreur lors de l'entrainement et pour le test
Ytrain_predicted = classifier.predict(Xtrain)
Ytest_predicted = classifier.predict(Xtest)
# la fonction error_rate devrait etre présente dans votre utilities.py (TP3), sinon ajoutez-la
e_tr = error_rate(Ytrain, Ytrain_predicted)
e_te = error_rate(Ytest, Ytest_predicted)

print("Classificateur: 1 Nearest Neighbor")
print("Training error:", e_tr)
print("Test error:", e_te)
```

+++ {"deletable": false, "editable": false, "nbgrader": {"task": false, "locked": true, "grade": false, "solution": false, "checksum": "77059be22c3c54130c6b58a0a0a17ef2", "cell_type": "markdown", "schema_version": 3, "grade_id": "cell-4db77bd51d290099"}}

**Exercice :** Quels sont les taux d'erreur pour l'ensemble
d'entraînement et l'ensemble de test ?

+++ {"deletable": false, "nbgrader": {"task": false, "grade": true, "cell_type": "markdown", "grade_id": "cell-2bcc05988216d1c5", "checksum": "c84bc8bccecc65edc309a08bdbc00fa9", "points": 0, "schema_version": 3, "solution": true, "locked": false}}

Pour l'ensemble d'entraînement le taux d'erreur est de 0%.

Pour l'ensemble de test le taux d'erreur est de 20%.

+++

On mémorise ces taux dans une table `error_rates` que l'on complétera
au fur et à mesure de cette feuille:

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: be4ace641636b1b069ceaaa16204f33e
  grade: false
  grade_id: cell-84331f5cb22a0f87
  locked: true
  schema_version: 3
  solution: false
  task: false
---
error_rates = pd.DataFrame([], columns=['entrainement', 'test'])
error_rates.loc["1 Nearest Neighbor",:] = [e_tr, e_te]
error_rates
```

+++ {"deletable": false, "nbgrader": {"task": false, "grade": false, "schema_version": 3, "solution": false, "checksum": "1dd2e40abcbbaec8ad5dbd190a66d33a", "locked": true, "cell_type": "markdown", "grade_id": "cell-2034d3a7e9dbbfad"}, "editable": false}

### Fenêtres de Parzen (*Parzen window* ou *radius neighbors*)

Pour ce classificateur, on ne fixe pas le nombre de voisins mais un
rayon $r$; la classe d'un élément $e$ est prédite par la classe
majoritaire parmi les éléments de l'ensemble d'entraînement dans la
sphère de centre $e$ et de rayon $r$.

**Exercice :** Complétez le code ci-dessous:

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 075ee8d9c439da9d08817ef6753e3fd3
  grade: false
  grade_id: cell-64dfc640f023f84e
  locked: false
  schema_version: 3
  solution: true
  task: false
---
from sklearn.neighbors import RadiusNeighborsClassifier
classifier = RadiusNeighborsClassifier(radius=2.0)

# on l'ajuste aux données d'entrainement

classifier.fit(Xtrain, Ytrain)

# on calcule ensuite le taux d'erreur lors de l'entrainement et pour le test

Ytrain_predicted = classifier.predict(Xtrain)
Ytest_predicted = classifier.predict(Xtest)

# on calcule les taux d'erreurs

e_tr = error_rate(Ytrain, Ytrain_predicted)
e_te = error_rate(Ytest, Ytest_predicted)

print("Classificateur: Parzen Window")
print("Training error:", e_tr)
print("Test error:", e_te)
```

+++ {"deletable": false, "nbgrader": {"task": false, "cell_type": "markdown", "schema_version": 3, "solution": false, "locked": true, "grade": false, "grade_id": "cell-540f1e8f9850f69f", "checksum": "1e18644108e320442bc0b8634f81b21e"}, "editable": false}

**Exercice :** Complétez la table `error_rates` avec ce modèle, en
rajoutant une ligne d'index `Parzen Window`.

**Indication :** Utiliser `.loc` comme ci-dessus.

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 6c97291bbff6a5bfcdc72decb782bd3e
  grade: false
  grade_id: cell-4274f47e142d9ed1
  locked: false
  schema_version: 3
  solution: true
  task: false
---
error_rates.loc["Parzen Window",:] = [e_tr, e_te]
error_rates
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 94009d7f2c58ec5ad7e96258e1585d05
  grade: true
  grade_id: cell-6ac30547e91d336f
  locked: true
  points: 2
  schema_version: 3
  solution: false
  task: false
---
assert isinstance(error_rates, pd.DataFrame)
assert list(error_rates.columns) == ['entrainement', 'test']
assert list(error_rates.index) == ['1 Nearest Neighbor', 'Parzen Window']
assert (0 <= error_rates).all(axis=None), "Les taux d'erreurs doivent être positifs"
assert (error_rates <= 1).all(axis=None), "Les taux d'erreurs doivent être inférieur à 1"
```

+++ {"nbgrader": {"checksum": "efc91c583821071bd7d6708312997c0e", "cell_type": "markdown", "grade_id": "cell-7ca6c7b760e2f6d3", "grade": false, "solution": false, "task": false, "schema_version": 3, "locked": true}, "deletable": false, "editable": false}

**Exercice $\clubsuit$ :** Faites varier le rayon $r$. Que se passe-t-il quand le rayon est grand ? petit ? Vous pouvez ajouter des modèles à la table
`error_rates` s'ils vous semblent pertinents. A quoi correspond un taux d'erreur de 0.5 ?

+++ {"nbgrader": {"cell_type": "markdown", "grade": true, "schema_version": 3, "checksum": "907f7d1366791fc82015fecbb1f759ed", "grade_id": "cell-77915ac0dc089d51", "points": 3, "solution": true, "task": false, "locked": false}, "deletable": false}

Lorsque le rayon est plus grand, les taux d’erreur pour l’ensemble d’entraînement et l’ensemble de test augmentent (avec un rayon à $3.0$: Training error = $0.2$ et Test error = $0.5$ et avec un rayon à $5.0$: Training error = $0.5$ et Test error = $0.5$).


Lorsque le rayon est plus petit,le taux d'erreur pour l'ensemble d'entraînement reste à $0%$, tandis que celui pour l'ensemble de test augmente (avec un rayon à $1.0$: Training error = $0.0$ et Test error = $0.3$).
Lorsque le rayon est trop petit, (radius < $0.8$) aucun voisin n'a été trouvé pour les échantillons de test.

Un taux d'erreur de 0.5 correspond à une performance de classification aléatoire. Si le modèle prédit les classes de manière totalement aléatoire, alors il aura une probabilité de 0.5 (50%) de prédire correctement la classe pour chaque image. En moyenne, sur un ensemble de données suffisamment grand, le taux d'erreur sera donc de 0.5.
Un taux d'erreur de 0.5 indique également que le modèle n'apprend pas les relations entre les attributs et les classes cibles.

+++ {"nbgrader": {"solution": false, "grade_id": "cell-7474d5437867f93b", "task": false, "cell_type": "markdown", "schema_version": 3, "grade": false, "locked": true, "checksum": "11aa8b1bee7bca0e0941f5be75907e9b"}, "deletable": false, "editable": false}

## Classificateurs basés sur les attributs (*feature based*)

+++ {"deletable": false, "nbgrader": {"checksum": "7f142dd64f1cac9b6487260ca1cff01d", "solution": false, "task": false, "grade": false, "locked": true, "schema_version": 3, "cell_type": "markdown", "grade_id": "cell-bc351a36f5734e8b"}, "editable": false}

### Régression linéaire

+++ {"deletable": false, "nbgrader": {"schema_version": 3, "solution": true, "locked": false, "grade": true, "task": false, "checksum": "b6b80b5d2c6aa80c8a5ad69b4bddf7fb", "grade_id": "cell-aa0635012d566877", "points": 1, "cell_type": "markdown"}}

**Exercice :** Pourquoi ne peut-on pas appliquer la méthode de
régression linéaire pour classer nos pommes et nos bananes ?

La méthode de régression linéaire fonctionne bien lorsque la relation entre les variables peut être approximée par une ligne droite. Cependant, elle n'est pas adaptée aux problèmes de classification où la variable cible est une variable catégorique.

En effet, dans le cas de la classification des images de pommes et de bananes, la variable que nous essayons de prédire est binaire : c'est soit une pomme, soit une banane. La régression linéaire ne convient donc pas à ce type de problème car elle prédit des valeurs continues et non des classes discrètes.

+++ {"editable": false, "nbgrader": {"checksum": "6e75b4ffc13642cda57f2610ee0b93dc", "grade_id": "cell-f3fe417cf16e9bb2", "grade": false, "task": false, "schema_version": 3, "solution": false, "locked": true, "cell_type": "markdown"}, "deletable": false}

### Arbres de décision

+++ {"editable": false, "nbgrader": {"checksum": "ca97a3e6e5fb381ffa0cf367ac37dd72", "grade": false, "cell_type": "markdown", "schema_version": 3, "task": false, "locked": true, "solution": false, "grade_id": "cell-0c0e203b37439b49"}, "deletable": false}

Les arbres de décison correspondent à des modèles avec des décisions
imbriquées où chaque noeud teste une condition sur une variable.  Les
étiquettes se trouvent aux feuilles.

+++ {"editable": false, "nbgrader": {"schema_version": 3, "cell_type": "markdown", "grade": false, "task": false, "solution": false, "checksum": "65228b363cf73812186190baeb789507", "locked": true, "grade_id": "cell-da133a018c6e8417"}, "deletable": false}

**Exercice :** Complétez le code ci-dessous.

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: a123282b37a47fa67ced79dab08f4543
  grade: false
  grade_id: cell-8e19513e3f66e21a
  locked: false
  schema_version: 3
  solution: true
  task: false
---
from sklearn import tree

classifier = tree.DecisionTreeClassifier()
# on l'ajuste aux données d'entrainement

classifier.fit(Xtrain, Ytrain)

# on calcule les prédictions du classifieur sur les ensembles d'entrainement et de test

Ytrain_predicted = classifier.predict(Xtrain)
Ytest_predicted = classifier.predict(Xtest)

# on calcule les taux d'erreurs

e_tr = error_rate(Ytrain, Ytrain_predicted)
e_te = error_rate(Ytest, Ytest_predicted)


print("Classificateur: Arbre de decision")
print("Training error:", e_tr)
print("Test error:", e_te)
```

```{code-cell} ipython3
X
```

+++ {"editable": false, "nbgrader": {"checksum": "7349afd1535a78ef980d8f6760875d5d", "grade_id": "cell-d818ab75031ccf3c", "solution": false, "cell_type": "markdown", "locked": true, "schema_version": 3, "task": false, "grade": false}, "deletable": false}

**Exercice :** Complétez la table `error_rates` avec ce modèle.

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 54e602f2d585ff5dfc81c09fa54777d2
  grade: false
  grade_id: cell-b2218cbf456bd91c
  locked: false
  schema_version: 3
  solution: true
  task: false
---
error_rates.loc["Decision Tree",:] = [e_tr, e_te]
error_rates
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 33f0ea85f8cee192d6dc5c287456f00a
  grade: true
  grade_id: cell-57894376c13e530a
  locked: true
  points: 2
  schema_version: 3
  solution: false
  task: false
tags: []
---
assert isinstance(error_rates, pd.DataFrame)
assert list(error_rates.columns) == ['entrainement', 'test']
assert error_rates.shape[0] >= 3
assert (0 <= error_rates).all(axis=None), "Les taux d'erreurs doivent être positifs"
assert (error_rates <= 1).all(axis=None), "Les taux d'erreurs doivent être inférieur à 1"
```

+++ {"editable": false, "nbgrader": {"grade_id": "cell-f1f775a2ccb77c41", "task": false, "cell_type": "markdown", "checksum": "4fa7e91b5a9e8184312ecfe62340a032", "grade": false, "locked": true, "schema_version": 3, "solution": false}, "deletable": false}

**Exercice :** Représentez l'arbre de décision comme vu lors du CM5.

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: ab4ebccfd10bfb3b26cd7a85e24a3e5e
  grade: true
  grade_id: cell-e4ce3f1406e9fe61
  locked: false
  points: 1
  schema_version: 3
  solution: true
  task: false
tags: []
---
import matplotlib.pyplot as plt
 
plt.figure(figsize=(12,12)) 
tree.plot_tree(classifier, fontsize=10) 
plt.show()
```

+++ {"nbgrader": {"checksum": "13ee0f12287d5da70c3db74deb623101", "grade_id": "cell-316a87fad1287712", "solution": true, "schema_version": 3, "grade": true, "task": false, "cell_type": "markdown", "points": 2, "locked": false}, "deletable": false}

**Exercice :** Interprétez cette figure. Combien de critère(s) ont été pris en compte pour séparer les pommes des bananes ?  Quel est le premier critère (attribut et valeur seuil) de séparation des échantillons ? Que signifie une mesure de gini à 0.0 ?

Un seul critère a été pris en compte car il n'y a qu'une seule bifurcation: l'attribut elongation avec un seuil à -0.192.
La mesure gini à 0.0 signifie que toutes les images sont identiques, la population est homogène.

+++ {"editable": false, "nbgrader": {"checksum": "4d130da1537149b2d938f87bb679c7c1", "task": false, "schema_version": 3, "grade_id": "cell-ff5d2dccfd4a6505", "cell_type": "markdown", "grade": false, "solution": false, "locked": true}, "deletable": false}

### Perceptron

+++ {"editable": false, "deletable": false, "nbgrader": {"task": false, "grade": false, "solution": false, "locked": true, "cell_type": "markdown", "checksum": "e493f04249e130eb23a4e0d6b7f05a98", "grade_id": "cell-e4a08d06646c875e", "schema_version": 3}}

Le perceptron est un réseau de neurones artificiels à une seule couche
et donc avec une capacité de modélisation limitée; pour le problème
qui nous intéresse cela est suffisant. Pour plus de détails, revenez
au [cours](CM5.md)

**Exercice :** Complétez le code ci-dessous, où l'on définit un modèle
de type `Perceptron` avec comme paramètres $10^{-3}$ pour la tolérence, $36$ pour l'état aléatoire (*random state*) et 100 époques (*max_iter*)

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 1616f84f831b6981890b007c5c53afe7
  grade: false
  grade_id: cell-c6bfeee5a4a966cc
  locked: false
  schema_version: 3
  solution: true
  task: false
---
from sklearn.linear_model import Perceptron

# définition du modèle de classificateur

classifier = Perceptron(tol=1e-3, random_state=2, shuffle=True, max_iter=100)

# on l'ajuste aux données d'entrainement

classifier.fit(Xtrain, Ytrain)

# on calcule ensuite le taux d'erreur lors de l'entrainement et pour le test

Ytrain_predicted = classifier.predict(Xtrain)
Ytest_predicted = classifier.predict(Xtest)

# on calcule les taux d'erreurs

e_tr = error_rate(Ytrain, Ytrain_predicted)
e_te = error_rate(Ytest, Ytest_predicted)

print("Classificateur: Perceptron")
print("Training error:", e_tr)
print("Test error:", e_te)
```

+++ {"deletable": false, "nbgrader": {"task": false, "points": 1, "schema_version": 3, "solution": true, "grade": true, "locked": false, "cell_type": "markdown", "grade_id": "cell-f8994015ecc492d3", "checksum": "9976c26d3d2c1e44c76a11b3d7d6b95a"}}

**Exercice :** Lisez la documentation de `Perceptron`. À quoi
correspond le paramètre `random_state` ?

random_state est utilisé pour mélanger les données d'apprentissage, lorsque shuffle est défini sur True. Il transmet un entier pour que la sortie soit reproductible sur plusieurs appels de fonction.
Chaque fois que la randomisation fait partie d'un algorithme Scikit-learn, un paramètre random_state peut être fourni pour contrôler le générateur de nombres aléatoires utilisé.

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 6c06d000995285781775ef088b2f0bfb
  grade: false
  grade_id: cell-4f0cec434737cda7
  locked: false
  schema_version: 3
  solution: true
  task: false
---
#help(Perceptron)
```

+++ {"deletable": false, "editable": false, "nbgrader": {"grade": false, "grade_id": "cell-65470533b4335e5a", "cell_type": "markdown", "checksum": "4913e916d7d45477247b2487260702b3", "schema_version": 3, "task": false, "locked": true, "solution": false}}

**Exercice :** Complétez la table `error_rates` avec ce modèle.

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: a71a5f08c8bdf5f6e2efee8d4768eea4
  grade: false
  grade_id: cell-cfe370c6b70f6d27
  locked: false
  schema_version: 3
  solution: true
  task: false
---
error_rates.loc["Perceptron",:] = [e_tr, e_te]
error_rates
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 49ed7a76afdf1c5075ac8514f69c24b5
  grade: true
  grade_id: cell-b72fa191a2a82889
  locked: true
  points: 1
  schema_version: 3
  solution: false
  task: false
---
assert error_rates.shape[0] >= 4
assert error_rates.shape[1] == 2
```

+++ {"editable": false, "deletable": false, "nbgrader": {"grade": false, "locked": true, "checksum": "80d4edc5a25b3393f6278c3563c15406", "schema_version": 3, "cell_type": "markdown", "grade_id": "cell-b546e3375477b608", "solution": false, "task": false}}

## $\clubsuit$ Points bonus : construction du classificateur «une règle» (*One Rule*)

Faites cette partie ou bien passez directement à la conclusion.

En complétant le code ci-dessous, créez votre premier classificateur
qui:
* sélectionne le "bon" attribut (rougeur ou élongation pour le
  problème des pommes/bananes), appelé $G$ (pour *good*). C'est
  l'attribut qui est le plus corrélé (en valeur absolue, toujours !)
  aux valeurs cibles $y = ± 1$;
* détermine une valeur seuil (*threshold*);
* utilise l'attribut `G` et le seuil pour prédire la classe des éléments.
        
Un canevas de la classe `OneRule` est fournit dans la fichier
`utilities.py`; vous pouvez le compléter ou bien la programmer
entièrement vous même.

Ce classificateur est-il basé sur les attributs ou sur les exemples?

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: e10fefacfc33dd0acf640372d2790081
  grade: false
  grade_id: cell-f300c99cf16ddefb
  locked: true
  schema_version: 3
  solution: false
  task: false
tags: []
---
from utilities_cours_correction import *
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 9f7385d47625cbe5517b632691a023d4
  grade: false
  grade_id: cell-b72b2907842cb7fb
  locked: true
  schema_version: 3
  solution: false
  task: false
---
# Use this code to test your classifier
classifier = OneRule()
classifier.fit(Xtrain, Ytrain) 
Ytrain_predicted = classifier.predict(Xtrain)
Ytest_predicted = classifier.predict(Xtest)
e_tr = error_rate(Ytrain, Ytrain_predicted)
e_te = error_rate(Ytest, Ytest_predicted)
print("Classificateur: One rule")
print("Training error:", e_tr)
print("Test error:", e_te)
```

+++ {"deletable": false, "editable": false, "nbgrader": {"task": false, "cell_type": "markdown", "grade": false, "schema_version": 3, "grade_id": "cell-472a8c23ef803ec9", "checksum": "63825ae948ab016d3e3be1b480b0c325", "solution": false, "locked": true}}

**Exercice :** Complétez la table `error_rates` avec ce modèle.

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: code
  checksum: 48da8b079dc5d00daf3aa8b11b312858
  grade: true
  grade_id: cell-7b189e436af168e3
  locked: false
  points: 3
  schema_version: 3
  solution: true
  task: false
---
error_rates.loc["One Rule",:] = [e_tr, e_te]
error_rates
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: d9cc4e307aa25ea9db7429b85c63e193
  grade: true
  grade_id: cell-14c847f98a88a373
  locked: true
  points: 0
  schema_version: 3
  solution: false
  task: false
---
assert error_rates.shape[0] >= 5
assert error_rates.shape[1] == 2
```

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 9eac0c359d449ad1c0026e38854320af
  grade: false
  grade_id: cell-587a9581023e668d
  locked: true
  schema_version: 3
  solution: false
  task: false
---
# On charge les images
dataset_dir = os.path.join(data.dir, 'ApplesAndBananasSimple')
images = load_images(dataset_dir, "*.png")
# This is what you get as decision boundary.
# The training examples are shown as white circles and the test examples are blue squares.
make_scatter_plot(X, images.apply(transparent_background_filter),
                  [], test_index, 
                  predicted_labels='GroundTruth',
                  feat = classifier.attribute, theta=classifier.theta, axis='square')
```

+++ {"deletable": false, "editable": false, "nbgrader": {"solution": false, "locked": true, "grade_id": "cell-1b8779ac28a26c59", "checksum": "e9f6c1a409234b6d29ce5f38de89a7bf", "grade": false, "cell_type": "markdown", "task": false, "schema_version": 3}}

Comparez avec ce que vous auriez obtenu en utilisant les 2 attributs avec le même poids lors de la décision de classe.

```{code-cell} ipython3
---
deletable: false
editable: false
nbgrader:
  cell_type: code
  checksum: 5f6db3de94731a2fbf4749d55449b82f
  grade: false
  grade_id: cell-d0e8bd2a9132fea6
  locked: true
  schema_version: 3
  solution: false
  task: false
---
make_scatter_plot(X, images.apply(transparent_background_filter),
                  [], test_index, 
                  predicted_labels='GroundTruth',
                  show_diag=True, axis='square')
```

+++ {"editable": false, "nbgrader": {"cell_type": "markdown", "solution": false, "grade": false, "grade_id": "cell-28e6ef8e4fa12a3c", "checksum": "8db48dc81f852cf17c594ed3396acf42", "locked": true, "schema_version": 3, "task": false}, "deletable": false}

### Conclusion

```{code-cell} ipython3
error_rates.loc["One Rule",:] = [e_tr, e_te]
error_rates
```

+++ {"editable": false, "deletable": false, "nbgrader": {"solution": false, "grade_id": "cell-1b50f7767c53d39b", "grade": false, "task": false, "schema_version": 3, "checksum": "5100cb76750f75f261150a575aaf1019", "cell_type": "markdown", "locked": true}}

**Exercice :** Comparez les taux d'erreur de vos différents
classificateurs, lors de l'entraînement et du test et pour le problème
des pommes et des bananes. 

**Indications:**
- Vous commencerez par *observer* les différents taux d'erreurs d'entrainement et d'apprentissage de vos classificateurs.
- Vous interpréterez le résultat en expliquant quel est le meilleur classificateur pour les pommes/bananes.

+++ {"nbgrader": {"points": 2, "grade": true, "locked": false, "task": false, "cell_type": "markdown", "checksum": "e547d4f47e160f74d5df32a060f61b3a", "schema_version": 3, "grade_id": "cell-a326ec80e8758fb8", "solution": true}, "deletable": false}

Les taux d'erreur d'entrainement sont tous nuls. Les taux d'erreurs de test sont à 20% pour tous les classificateurs, sauf pour le classificateur Perceptron qui a un taux d'erreur de 10%.

Les taux d'erreur d'entrainement sont tous nuls car 


Pour les pommes et les bananes, le meilleur classificateur est le perceptron.

+++ {"editable": false, "deletable": false, "nbgrader": {"locked": true, "checksum": "623cd73082a3b65a692ad8b02da7e0ba", "grade": false, "grade_id": "cell-466bf7addb6e465a", "solution": false, "task": false, "schema_version": 3, "cell_type": "markdown"}}

Dans cette feuille vous avez découvert comment utiliser un certain
nombre de classificateurs, voire comment implanter le vôtre, et
comment jouer sur les paramètres de ces classificateurs (par exemple
la tolérance du perceptron ou le nombre de voisins du KNN) pour
essayer d'optimiser leur performance.

Mettez à jour votre rapport et déposez votre travail.

+++ {"nbgrader": {"grade": false, "locked": true, "solution": false, "task": false, "schema_version": 3, "grade_id": "cell-466bf7addb6e465b", "checksum": "3e3d916a7c8ce54f4dc14284f4de3fc4", "cell_type": "markdown"}, "editable": false, "deletable": false}

Vous êtes maintenant prêts pour revenir à votre [analyse de
données](4_analyse_de_donnees.md) pour mettre en œuvre ces
classificateurs sur votre jeu de données.

**Dans le projet 1, on vous demande de choisir un seul classificateur,
ainsi que ses paramètres**.  Nous verrons dans la seconde partie de
l'UE comment comparer systématiquement les classificateurs.
