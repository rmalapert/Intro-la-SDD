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

+++ {"deletable": false, "editable": true, "nbgrader": {"checksum": "56952047c4310bf0c313ca44bb5af720", "grade": false, "grade_id": "cell-883bbb5e1919ca1e", "locked": true, "schema_version": 3, "solution": false, "task": false}, "slideshow": {"slide_type": ""}, "tags": []}

# Analyse de données

:::{admonition} Consignes
:class: dropdown

Vous documenterez votre analyse de données dans cette feuille. Nous
vous fournissons seulement l'ossature. À vous de piocher dans les
feuilles et TPs précédents les éléments que vous souhaiterez
réutiliser et adapter pour composer votre propre analyse!

:::

+++ {"deletable": false, "editable": false, "nbgrader": {"checksum": "07b6f7ec89dbe99e092902adb7ff9cc8", "grade": false, "grade_id": "cell-e15fdd0116a9b9e2", "locked": true, "schema_version": 3, "solution": false, "task": false}}

## Import des bibliothèques

+++ {"deletable": false, "editable": false, "nbgrader": {"checksum": "96489bd4f670f5daa152f95fa18ce301", "grade": false, "grade_id": "cell-406ad2a159064cfa", "locked": true, "schema_version": 3, "solution": false, "task": false}}

On commence par importer les bibliothèques dont nous aurons besoin.

:::{admonition} Consignes
:class: dropdown

Inspirez vous des précédentes feuilles. N'oubliez pas d'importer
`utilities` et `intro_science_donnees`.

:::

```{code-cell} ipython3
---
deletable: false
editable: true
nbgrader:
  checksum: 8e6a7da13b6e1d41caa1c33a8c0104e5
  grade: false
  grade_id: cell-76e64ee7bcb96bb5
  locked: false
  schema_version: 3
  solution: true
  task: false
slideshow:
  slide_type: ''
tags: []
---
%load_ext autoreload
%autoreload 2
from PIL import Image, ImageDraw, ImageFont
import matplotlib.pyplot as plt
from scipy import signal
from scipy.ndimage import gaussian_filter

%matplotlib inline
from intro_science_donnees import *
from utilities import *
```

+++ {"deletable": false, "editable": false, "nbgrader": {"checksum": "e319f7fbbc65f7368fd94b546edb9e34", "grade": false, "grade_id": "cell-6ec03d05a4e1c693", "locked": true, "schema_version": 3, "solution": false, "task": false}, "slideshow": {"slide_type": ""}, "tags": []}

## Chargement des images

:::{admonition} Consignes
:class: dropdown

- Chargez vos images recentrées et réduites.
- Affichez les.

:::

```{code-cell} ipython3
---
deletable: false
editable: true
nbgrader:
  checksum: a842cb19c2f315d9a183cf8a17bd295f
  grade: false
  grade_id: cell-211e275ccc954714
  locked: false
  schema_version: 3
  solution: true
  task: false
slideshow:
  slide_type: ''
tags: []
---
extension = "jpeg"
dataset_dir = os.path.join('..', 'data')
images_a = load_images(dataset_dir, f"a*.{extension}")
images_b = load_images(dataset_dir, f"b*.{extension}")
images = pd.concat([images_a, images_b])

image_grid(images, titles=images.index)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---
img = images.iloc[0]
#img_cropped(img)
img
```

```{code-cell} ipython3
---
editable: false
slideshow:
  slide_type: ''
tags: []
---
foreground = my_foreground_filter(img) #img_crop
show_source(my_foreground_filter)
```

```{code-cell} ipython3
---
editable: true
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
crop_around_center(img, center)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---
images_cropped_around_center = [crop_around_center(img, center) for img in images]
image_grid(images_cropped_around_center)
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
editable: false
slideshow:
  slide_type: ''
tags: []
---
images_cropped = images.apply(crop_image)
show_source (crop_image)
```

```{code-cell} ipython3
image_grid(images_cropped)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---
images_cropped_around_center = images_cropped.apply(crop_around_center)
show_source(crop_around_center)
```

```{code-cell} ipython3
image_grid(images_cropped_around_center)
```

```{code-cell} ipython3
img = images.iloc[0]
img1 = images.iloc[1]
img.size
```

```{code-cell} ipython3
def crop_to_center(image, target_width = 200, target_height = 300):
    # Calcul du centre de masse de l'image
    centroid_x, centroid_y = 1.0*image.size[0] / 2, 1.0*image.size[1] / 2
    
    # Calcul des coordonnées de recadrage
    left = int(centroid_x - target_width / 2)
    top = int(centroid_y - target_height / 2)
    right = int(centroid_x + target_width / 2)
    bottom = int(centroid_y + target_height / 2)
    
    # Recadrage de l'image autour du centre
    cropped_image = image.crop((left, top, right, bottom))
    
    return cropped_image
```

```{code-cell} ipython3
"""# Charger les images
extension = "jpeg"
dataset_dir = os.path.join('..', 'data')
images_a = load_images(dataset_dir, f"a*.{extension}")
images_b = load_images(dataset_dir, f"b*.{extension}")
images = pd.concat([images_a, images_b])

target_width = 200
target_height = 300

# Liste pour stocker les images recadrées
cropped_images = []

cropped_image = images.apply(crop_to_center)
cropped_images.append(cropped_image)

# Convertir la liste d'images recadrées en DataFrame
cropped_images_df = pd.DataFrame(cropped_images, index=images.index)

# Afficher les images recadrées dans une grille
image_grid(cropped_images_df, titles=cropped_images_df.index)"""
```

```{code-cell} ipython3

```

```{code-cell} ipython3
images_crop = images.apply(img_cropped)
image_grid(images_crop)
```

Nous avons crop nos images afin de les recentrer plus proche de la carte, pour appliquer

```{code-cell} ipython3
#image_white = foreground_imgs.apply(white_pixel)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---
#foreground_imgs = [foreground_redness_filter(img, theta=.85)
  #          for img in images_crop]

#image_grid(foreground_imgs)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---
for e in foreground_imgs:
    print(True_or_False(e))
```

avec un crop plus précis, on pourrait obtenir des résultats plus distincs

```{code-cell} ipython3
"""img_barycentre = Image.fromarray(foreground_imgs).convert("RGB")

draw = ImageDraw.Draw(img_barycentre)
radius = 1
bbox = [center[1] - radius, center[0] - radius, center[1] + radius, center[0] + radius]
draw.ellipse(bbox, outline="red", width=2)

plt.imshow(img_barycentre)"""
```

+++ {"deletable": false, "editable": false, "nbgrader": {"checksum": "0e4e77fedcf1d467c6139e4c44ff1e5e", "grade": false, "grade_id": "cell-e723e3fb5001bd97", "locked": true, "schema_version": 3, "solution": false, "task": false}}

## Prétraitement : extraction des attributs

:::{admonition} Consignes
:class: dropdown

- Choisissez entre des attributs ad-hoc ou obtenus via une ACP depuis
  les données brutes (représentation en pixel).
- N'oubliez pas de normaliser votre table une fois les traitements
  effectués.
  
:::

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
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
images_cropped.apply(white_pixel)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
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
images_cropped.apply (True_or_False)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---

```

```{code-cell} ipython3

```

+++ {"deletable": false, "editable": false, "nbgrader": {"checksum": "0b04fc60580d53165e039ab88751a746", "grade": false, "grade_id": "cell-a69bb602ef2221a4", "locked": true, "schema_version": 3, "solution": false, "task": false}}

## Sauvegarde intermédiaire

+++ {"deletable": false, "editable": false, "nbgrader": {"checksum": "35a89b8dc927e0dd763e9cdf592c4bdf", "grade": false, "grade_id": "cell-8cb1846130b9cfe2", "locked": true, "schema_version": 3, "solution": false, "task": false}}

:::{admonition} Consignes
:class: dropdown

Une fois que vous êtes satisfaits des attributs obtenus, faites en une
sauvegarde intermédiaire.

:::

+++ {"deletable": false, "editable": false, "nbgrader": {"checksum": "b11c92757f734a72bf65f47b9513ca5f", "grade": false, "grade_id": "cell-4576e2c2723b724d", "locked": true, "schema_version": 3, "solution": false, "task": false}}

## Visualisation des données

+++ {"deletable": false, "editable": false, "nbgrader": {"checksum": "ba2beca9ed69fdecbf145787bb70f7af", "grade": false, "grade_id": "cell-2a67648ea9c07906", "locked": true, "schema_version": 3, "solution": false, "task": false}}

:::{admonition} Consignes
:class: dropdown

Visualisez les attributs et leur corrélation. Mettez en œuvre les
formes que vous jugerez les plus pertinentes (carte thermique, ...).

:::

+++ {"deletable": false, "editable": false, "nbgrader": {"checksum": "fed3b57a555c863751f511011266a6de", "grade": false, "grade_id": "cell-c103279002f68467", "locked": true, "schema_version": 3, "solution": false, "task": false}}

## Performance 

:::{admonition} Consignes
:class: dropdown

- Choisissez un ou plusieurs classificateurs et calculez leur
  performances.
- Comparez les performances selon les attributs utilisés (ad-hoc,
  ...).
- Si vous le souhaitez, vous pouvez aussi:
    - comparer un ou plusieurs classificateurs
	- comparer les performances selon le nombre de colonnes (pixels ou
      attributs) considérées.

:::

+++ {"deletable": false, "nbgrader": {"checksum": "ae7f04d9aea4689c8f25f33fd8cddf30", "grade": true, "grade_id": "cell-c5a8e3d90bb35375", "locked": false, "points": 0, "schema_version": 3, "solution": true, "task": false}}

VOTRE RÉPONSE ICI
