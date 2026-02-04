---
jupytext:
  notebook_metadata_filter: rise
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
rise:
  auto_select: first
  autolaunch: false
  backimage: fond.png
  centered: false
  controls: false
  enable_chalkboard: true
  height: 100%
  margin: 0
  maxScale: 1
  minScale: 1
  scroll: true
  slideNumber: true
  start_slideshow_at: selected
  transition: none
  width: 90%
---

+++ {"slideshow": {"slide_type": "slide"}, "editable": true, "tags": []}

# Classification de cartes

- Binôme: Rémi Malapert, Elios Morize
- Adresses mails: 
- [Dépôt GitLab](https://gitlab.dsi.universite-paris-saclay.fr/xxx.yyy/Semaine8/)

+++ {"slideshow": {"slide_type": "slide"}, "editable": true, "tags": []}

## Jeu de données

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: skip
tags: []
---
%load_ext autoreload
%autoreload 2
import os, re
from PIL import Image, ImageDraw, ImageFont
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
import scipy
from scipy import signal
from scipy.ndimage import gaussian_filter
import pandas as pd
import seaborn as sns; sns.set()
from glob import glob as ls
import sys
from sklearn.neighbors import KNeighborsClassifier
from sklearn.metrics import balanced_accuracy_score as sklearn_metric
import warnings
warnings.filterwarnings("ignore")

from utilities import *
from intro_science_donnees import data
from intro_science_donnees import *
```

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: ''
tags: []
---
extension = "jpeg"
dataset_dir = os.path.join('..', 'data')
images_a = load_images(dataset_dir, f"a*.{extension}")
images_b = load_images(dataset_dir, f"b*.{extension}")
images = pd.concat([images_a, images_b])

images1 = images
image_grid(images, titles=images.index)
```

+++ {"slideshow": {"slide_type": "slide"}, "editable": true, "tags": []}

## Prétraitement 

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: ''
tags: []
---
img = images.iloc[0]
img
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: skip
tags: []
---
foreground = my_foreground_filter(img) 
show_source(my_foreground_filter)
```

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: ''
tags: []
---
coordinates = np.argwhere(foreground)
xy = np.argwhere(foreground)
center = np.mean(xy, axis=0)
plt.scatter(coordinates[:,1], -coordinates[:,0], marker="x");
plt.scatter(center[1], -center[0], 300, c='r', marker='+',linewidth=5);
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: slide
tags: []
---
crop_around_center(img, center)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---
img1 = images.iloc[0]
crop_image(img1)
```

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: slide
tags: []
---
images_cropped_around_center = [crop_around_center(img, center) for img in images]
image_grid(images_cropped_around_center)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: skip
tags: []
---
images = pd.Series([img for img in images_cropped_around_center], index=images1.index)
image_grid(images)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: slide
tags: []
---
show_source(white_pixel)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---
images.apply(white_pixel)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: slide
tags: []
---
show_source(True_or_False)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---
images.apply (True_or_False)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: slide
tags: []
---
img = images.iloc[0]
def little_preprocessing(img):
    foreground = my_foreground_filter(img)
    img = transparent_background(img, foreground)
    return img
little_preprocessing(img)
```

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: ''
tags: []
---
images_clean = images.apply(little_preprocessing)
image_grid(images_clean)
```

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: skip
tags: []
---
df_clean = images_clean.apply(image_to_series)
df_clean['class'] = df_clean.index.map(lambda name: 1 if name[0] == 'a' else -1)
```

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: skip
tags: []
---
os.makedirs('clean_data2', exist_ok=True)
for name, img in images_clean.items():
    img.save(os.path.join('clean_data2', os.path.splitext(name)[0]+".png"))

os.path.splitext("machin.jpeg")

df_clean.to_csv('clean_data2.csv')
df_clean = pd.read_csv('clean_data2.csv', index_col=0)
```

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: slide
tags: []
---
df_features = pd.DataFrame({
                'redness': images_clean.apply(redness),
                'greenness': images_clean.apply(greenness),
                'blueness' : images_clean.apply(blueness),
                'elongation': images_clean.apply(elongation),
                'perimeter': images_clean.apply(perimeter),
                'surface': images_clean.apply(surface),
                'white' : images_clean.apply(white_pixel),
                'true?' : images_clean.apply(True_or_False),
})

df_features["class"] = df_clean["class"]
header = ['R','G','B','M=maxRGB', 'm=minRGB', 'C=M-m', 'R-(G+B)/2', 'G-B', 'G-(R+B)/2', 'B-R', 'B-(G+R)/2', 'R-G', '(G-B)/C', '(B-R)/C', '(R-G)/C', '(R+G+B)/3', 'C/V']


df_features_large = df_features.drop("class", axis = 1)

df_features_large = pd.concat([df_features_large, images_clean.apply(get_colors)], axis=1)


epsilon = sys.float_info.epsilon #pour éviter une division par 0 au cas où une colonne soit constante
df_features_large = (df_features_large - df_features_large.mean())/(df_features_large.std() + epsilon)


df_features_large["class"] = df_clean["class"]
df_features_large.style.background_gradient(cmap='coolwarm')
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: slide
tags: []
---
corr_large = df_features_large.corr()
corr_large.style.format(precision=2).background_gradient(cmap='coolwarm')
```

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: ''
tags: []
---
sval = corr_large['class'][:-1].abs().sort_values(ascending=False)
ranked_columns = sval.index.values
col_selected = ranked_columns[0:4]
df_features_final = pd.DataFrame.copy(df_features_large)
df_features_final = df_features_final[col_selected]

df_features_final['class'] = df_features_large["class"]
g = sns.pairplot(df_features_final, hue="class", markers=["o", "s"], diag_kind="hist")
```

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: slide
tags: []
---
sklearn_model = KNeighborsClassifier(n_neighbors=3)
performances = pd.DataFrame(columns = ['Traitement', 'perf_tr', 'std_tr', 'perf_te', 'std_te'])

df_raw = images1.apply(image_to_series)
df_raw['class'] = df_raw.index.map(lambda name: 1 if name[0] == 'a' else -1)
```

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: ''
tags: []
---
p_tr, s_tr, p_te, s_te = df_cross_validate(df_raw, sklearn_model, sklearn_metric)
metric_name = sklearn_metric.__name__.upper()
performances.loc[0] = ["Images brutes", p_tr, s_tr, p_te, s_te]

p_tr, s_tr, p_te, s_te = df_cross_validate(df_clean, sklearn_model, sklearn_metric)
metric_name = sklearn_metric.__name__.upper()
performances.loc[1] = ["Images clean", p_tr, s_tr, p_te, s_te]

p_tr, s_tr, p_te, s_te = df_cross_validate(df_features, sklearn_model, sklearn_metric)
metric_name = sklearn_metric.__name__.upper()
performances.loc[2] = ["Images features", p_tr, s_tr, p_te, s_te]

p_tr, s_tr, p_te, s_te = df_cross_validate(df_features_large, sklearn_model, sklearn_metric)
metric_name = sklearn_metric.__name__.upper()
performances.loc[3] = ["25 attributs ad-hoc", p_tr, s_tr, p_te, s_te]

performances.style.format(precision=2).background_gradient(cmap='Blues')
```

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: slide
tags: []
---
feat_lc_df, ranked_columns = feature_learning_curve(df_features_large, sklearn_model, sklearn_metric)
plt.errorbar(feat_lc_df.index+1, feat_lc_df['perf_tr'], yerr=feat_lc_df['std_tr'], label='Training set')
plt.errorbar(feat_lc_df.index+1, feat_lc_df['perf_te'], yerr=feat_lc_df['std_te'], label='Test set')
plt.xticks(np.arange(1, 26, 1)) 
plt.xlabel('Number of features')
plt.ylabel(sklearn_metric.__name__)
plt.legend(loc='lower right');
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: skip
tags: []
---
df_features_final.to_csv('features_data2.csv')
```

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: slide
tags: []
---
p_tr, s_tr, p_te, s_te = df_cross_validate(df_features_final, sklearn_model, sklearn_metric)
metric_name = sklearn_metric.__name__.upper()
performances.loc[4] = ["3 attributs par analyse de variance univarié", p_tr, s_tr, p_te, s_te]
performances.style.format(precision=2).background_gradient(cmap='Blues')
```

+++ {"slideshow": {"slide_type": "slide"}, "editable": true, "tags": []}

## Classificateurs favoris et visualisation des données

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: skip
tags: []
---
??systematic_model_experiment
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: skip
tags: []
---
df_features = pd.read_csv("../Semaine7/features_data2.csv", index_col=0)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---
from sklearn.neural_network import MLPClassifier
from sklearn.neighbors import KNeighborsClassifier
from sklearn.neighbors import RadiusNeighborsClassifier
from sklearn.svm import SVC
from sklearn.gaussian_process import GaussianProcessClassifier
from sklearn.gaussian_process.kernels import RBF
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier, AdaBoostClassifier
from sklearn.naive_bayes import GaussianNB
from sklearn.discriminant_analysis import QuadraticDiscriminantAnalysis

model_name = ["Nearest Neighbors", "Parzen Windows",  "Linear SVM", "RBF SVM", "Gaussian Process",
         "Decision Tree", "Random Forest", "Neural Net", "AdaBoost",
         "Naive Bayes", "QDA"]
model_list = [
    KNeighborsClassifier(3),
    RadiusNeighborsClassifier(radius=12.0),
    SVC(kernel="linear", C=0.025, probability=True),
    SVC(gamma=2, C=1, probability=True),
    GaussianProcessClassifier(1.0 * RBF(1.0)),
    DecisionTreeClassifier(max_depth=10),
    RandomForestClassifier(max_depth=10, n_estimators=10, max_features=1),
    MLPClassifier(alpha=1, max_iter=1000),
    AdaBoostClassifier(),
    GaussianNB(),
    QuadraticDiscriminantAnalysis()]
```

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: ''
tags: []
---
from sklearn.metrics import balanced_accuracy_score as sklearn_metric
compar_results = systematic_model_experiment(df_features, model_name, model_list, sklearn_metric)
compar_results.style.format(precision=2).background_gradient(cmap='Blues')
```

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: slide
tags: []
---
tebad = compar_results.perf_te < compar_results.perf_te.median()
trbad = compar_results.perf_tr < compar_results.perf_tr.median()
overfitted = tebad & ~trbad

tebad = compar_results.perf_te < compar_results.perf_te.median()
trbad = compar_results.perf_tr < compar_results.perf_tr.median()
underfitted =  trbad & ~tebad
analyze_model_experiments(compar_results)
```

+++ {"editable": true, "slideshow": {"slide_type": "slide"}, "tags": []}

## Interprétations

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: skip
tags: []
---
class ClassifierCommittee():
    def __init__(self, model_list):
        self.model_list = model_list
        
    def fit(self,X,y):
        for model in self.model_list:
            model.fit(X,y)
    def predict(self,X):
        predictions = []
        for model in self.model_list:
            predictions.append(model.predict(X))
        predictions = np.mean(np.array(predictions),axis = 0)
        results = []
        for v in predictions:
            if v < 0:
                results.append(-1)
            else:
                results.append(1)
        return np.array(results)
    
    def predict_proba(self,X):
        predictions = []
        for model in self.model_list:
            predictions.append(model.predict_proba(X))
        return np.swapaxes(np.array(predictions), 0, 1)
    def score(self,X):
        scores = []
        for model in self.model_list:
            scores.append(model.score(X,y))
        return np.swapaxes(np.array(scores), 0, 1)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: skip
tags: []
---
sklearn_model = ClassifierCommittee(model_list)
X = df_features.iloc[:, :-1]
Y = df_features.iloc[:, -1]
#partition des images
train_index, test_index = split_data(X, Y, seed=3)

#partition de la table des attributs
Xtrain = X.iloc[train_index]
Xtest = X.iloc[test_index]
#partition de la table des étiquettes
Ytrain = Y.iloc[train_index]
Ytest = Y.iloc[test_index]

# Entraîner le comité de classificateurs
sklearn_model.fit(Xtrain, Ytrain)

# Prédire les classes sur l'ensemble de test
predicted_classes = sklearn_model.predict(Xtest)

# Calculer la précision (accuracy)
accuracy = np.mean(predicted_classes == Ytest)
```

```{code-cell} ipython3
---
editable: true
jupyter:
  source_hidden: true
slideshow:
  slide_type: ''
tags: []
---
X = df_features.iloc[:, :-1].to_numpy()
Y = df_features.iloc[:, -1].to_numpy()
sklearn_model.fit(X, Y)
prediction_probabilities = sklearn_model.predict_proba(X)
prediction_probabilities.shape
from scipy.stats import entropy
entropies_per_classifier = entropy(np.swapaxes(prediction_probabilities, 0, 2))
entropies = np.mean(entropies_per_classifier, axis = 0)
df_uncertainty = pd.DataFrame({"images" : images,
                           "aleatoric" : entropies})
df_uncertainty = df_uncertainty.sort_values(by=['aleatoric'],ascending=False)
df_uncertainty.style.background_gradient(cmap='RdYlGn_r')
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: slide
tags: []
---
images_clean.iloc[31]
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---
images_clean.iloc[15]
```

+++ {"slideshow": {"slide_type": "slide"}, "editable": true, "tags": []}

## Discussion et conclusion


```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---
image_grid(images1)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---
def crop_corner(img):
    return img.crop(box=(85, 75, 100, 90))
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---
img = images1.iloc[0]
crop_corner(img)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---

```
