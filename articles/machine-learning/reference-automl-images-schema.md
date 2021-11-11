---
title: Format JSON pour les tâches de vision par ordinateur
titleSuffix: Azure Machine Learning
description: Découvrez comment mettre en forme vos fichiers JSONL pour la consommation des données dans des expériences de ML automatisées pour les tâches de vision par ordinateur.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: reference
ms.reviewer: nibaccam
ms.author: rvadthyavath
author: vadthyavath
ms.date: 10/13/2021
ms.custom: ''
ms.openlocfilehash: 725e8ae80d29a352bd2146906a782d4c8c2e6e38
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131442542"
---
# <a name="data-schemas-to-train-computer-vision-models-with-automated-machine-learning"></a>Schémas de données pour former des modèles de vision par ordinateur avec Machine Learning automatisé

Découvrez comment mettre en forme vos fichiers JSONL pour la consommation des données dans des expériences de ML automatisées pour les tâches de vision par ordinateur pendant l’apprentissage et l’inférence.


## <a name="data-schema-for-training"></a>Schéma de données pour l’apprentissage 

Azure Machine Learning AutoML pour les images requiert la préparation des données d’image d’entrée au format [JSON](https://jsonlines.org/) (Lignes JSON). Cette section décrit les formats de données d’entrée ou le schéma pour la classification d’images multiclasse, la classification d’images multi-étiquette, la détection d’objets et la segmentation d’instance. Nous fournirons également un exemple de fichier de lignes JSON d’apprentissage ou de validation final.

### <a name="image-classification-binarymulti-class"></a>Classification d’images (binaire/multiclasse)

**Format/schéma des données d’entrée dans chaque ligne JSON :**
```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":"class_name",
}
```

| Clé       | Description  | Exemple |
| -------- |----------|-----|
| `image_url` | Emplacement de l’image dans le magasin de données AML<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| `image_details` | Détails d’image<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| `format`  | Type d’image (tous les formats d’images disponibles dans la bibliothèque [Pillow](https://pillow.readthedocs.io/en/stable/releasenotes/8.0.1.html) sont pris en charge)<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif","bmp", "tif", "tiff"}`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| `width` | Largeur de l'image<br>`Optional, String or Positive Integer`  | `"400px" or 400`|
| `height` | Hauteur de l’image<br>`Optional, String or Positive Integer` | `"200px" or 200` |
| `label` | Classe/étiquette de l’image<br>`Required, String` | `"cat"` |


Exemple de fichier JSON pour la classification d’images multiclasse :
```json
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details":{"format": "jpg", "width": "400px", "height": "258px"}, "label": "can"}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "397px", "height": "296px"}, "label": "milk_bottle"}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "1024px", "height": "768px"}, "label": "water_bottle"}
  ```

![Exemple d’image pour une classification d’images multiclasse.](media/reference-automl-images-schema/multiclass-predictions.jpg)

### <a name="image-classification-multi-label"></a>Classification d’images multi-étiquette

Voici un exemple de format/schéma de données d’entrée dans chaque ligne JSON pour la classification d’images.


```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":[
      "class_name_1",
      "class_name_2",
      "class_name_3",
      "...",
      "class_name_n"
        
   ]
}
```

| Clé       | Description  | Exemple |
| -------- |----------|-----|
| `image_url` | Emplacement de l’image dans le magasin de données AML<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| `image_details` | Détails d’image<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| `format`  | Type d’image (tous les formats d’images disponibles dans la bibliothèque [Pillow](https://pillow.readthedocs.io/en/stable/releasenotes/8.0.1.html) sont pris en charge)<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif", "bmp", "tif", "tiff"}`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| `width` | Largeur de l'image<br>`Optional, String or Positive Integer`  | `"400px" or 400`|
| `height` | Hauteur de l’image<br>`Optional, String or Positive Integer` | `"200px" or 200` |
| `label` | Liste des classes/étiquettes dans l’image<br>`Required, List of Strings` | `["cat","dog"]` |


Exemple de fichier JSON pour la classification d’images multi-étiquette :

```json
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details":{"format": "jpg", "width": "400px", "height": "258px"}, "label": ["can"]}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "397px", "height": "296px"}, "label": ["can","milk_bottle"]}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "1024px", "height": "768px"}, "label": ["carton","milk_bottle","water_bottle"]}
  ```

![Exemple d’image pour une classification d’images multi-étiquette.](media/reference-automl-images-schema/multilabel-predictions.jpg)

### <a name="object-detection"></a>Détection d’objets

Voici un exemple de fichier JSONL pour la détection d’objets.

```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":[
      {
         "label":"class_name_1",
         "topX":"xmin/width",
         "topY":"ymin/height",
         "bottomX":"xmax/width",
         "bottomY":"ymax/height",
         "isCrowd":"isCrowd"
      },
      {
         "label":"class_name_2",
         "topX":"xmin/width",
         "topY":"ymin/height",
         "bottomX":"xmax/width",
         "bottomY":"ymax/height",
         "isCrowd":"isCrowd"
      },
      "..."
   ]
}
```

Ici, 
- `xmin` = coordonnée x de l’angle supérieur gauche du cadre englobant
- `ymin` = coordonnée y de l’angle supérieur gauche du cadre englobant
- `xmax` = coordonnée x de l’angle inférieur droit du cadre englobant
- `ymax` = coordonnée y de l’angle inférieur droit du cadre englobant



| Clé       | Description  | Exemple |
| -------- |----------|-----|
| `image_url` | Emplacement de l’image dans le magasin de données AML<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| `image_details` | Détails d’image<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| `format`  | Type d’image (tous les formats d’images disponibles dans la bibliothèque [Pillow](https://pillow.readthedocs.io/en/stable/releasenotes/8.0.1.html) sont pris en charge. Toutefois, pour YOLO, seuls les formats d’images autorisés par [opencv](https://pypi.org/project/opencv-python/4.3.0.36/) sont pris en charge)<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif", "bmp", "tif", "tiff"}`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| `width` | Largeur de l'image<br>`Optional, String or Positive Integer`  | `"499px" or 499`|
| `height` | Hauteur de l’image<br>`Optional, String or Positive Integer` | `"665px" or 665` |
| `label` (clé externe) | Liste de cadres englobante, où chaque zone est un dictionnaire de \leurs coordonnées en haut à gauche et en bas à droite `label, topX, topY, bottomX, bottomY, isCrowd`<br>`Required, List of dictionaries` | `[{"label": "cat", "topX": 0.260, "topY": 0.406, "bottomX": 0.735, "bottomY": 0.701, "isCrowd": 0}]` |
| `label` (clé interne)| Classe/étiquette de l’objet dans le cadre englobant<br>`Required, String` | `"cat"` |
| `topX` | Taux de coordonnée x du coin supérieur gauche du cadre englobant et de la largeur de l’image<br>`Required, Float in the range [0,1]` | `0.260` |
| `topY` | Taux de coordonnée y du coin supérieur gauche du cadre englobant et de la hauteur de l’image<br>`Required, Float in the range [0,1]` | `0.406` |
| `bottomX` | Taux de coordonnée x du coin inférieur droit du cadre englobant et de la largeur de l’image<br>`Required, Float in the range [0,1]` | `0.735` |
| `bottomY` | Taux de coordonnée y du coin inférieur droit du cadre englobant et de la hauteur de l’image<br>`Required, Float in the range [0,1]` | `0.701` |
| `isCrowd` | Indique si le cadre englobant est autour de la foule d’objets. Si cet indicateur spécial est défini, nous ignorons ce cadre englobant particulier lors du calcul de la métrique.<br>`Optional, Bool` | `0` |


Exemple de fichier JSON pour la détection d’objets :

```json
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "can", "topX": 0.260, "topY": 0.406, "bottomX": 0.735, "bottomY": 0.701, "isCrowd": 0}]}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "carton", "topX": 0.172, "topY": 0.153, "bottomX": 0.432, "bottomY": 0.659, "isCrowd": 0}, {"label": "milk_bottle", "topX": 0.300, "topY": 0.566, "bottomX": 0.891, "bottomY": 0.735, "isCrowd": 0}]}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "carton", "topX": 0.0180, "topY": 0.297, "bottomX": 0.380, "bottomY": 0.836, "isCrowd": 0}, {"label": "milk_bottle", "topX": 0.454, "topY": 0.348, "bottomX": 0.613, "bottomY": 0.683, "isCrowd": 0}, {"label": "water_bottle", "topX": 0.667, "topY": 0.279, "bottomX": 0.841, "bottomY": 0.615, "isCrowd": 0}]}
```

![Exemple d’image pour la détection d’objet.](media/reference-automl-images-schema/object-detect-predictions.jpg)

### <a name="instance-segmentation"></a>Segmentation d’instances

Pour la segmentation d’instances, le ML automatisé prend uniquement en charge le polygone comme entrée et sortie, sans aucun masque.

Voici un exemple de fichier JSON pour la segmentation d’instance.

```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":[
      {
         "label":"class_name",
         "isCrowd":"isCrowd",
         "polygon":[["x1", "y1", "x2", "y2", "x3", "y3", "...", "xn", "yn"]]
      }
   ]
}
```

| Clé       | Description  | Exemple |
| -------- |----------|-----|
| `image_url` | Emplacement de l’image dans le magasin de données AML<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| `image_details` | Détails d’image<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| `format`  | Type d’image<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif", "bmp", "tif", "tiff" }`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| `width` | Largeur de l'image<br>`Optional, String or Positive Integer`  | `"499px" or 499`|
| `height` | Hauteur de l’image<br>`Optional, String or Positive Integer` | `"665px" or 665` |
| `label` (clé externe) | Liste de masques, où chaque masque est un dictionnaire de `label, isCrowd, polygon coordinates` <br>`Required, List of dictionaries` | ` [{"label": "can", "isCrowd": 0, "polygon": [[0.577, 0.689,`<br> ` 0.562, 0.681,`<br> `0.559, 0.686]]}]` |
| `label` (clé interne)| Classe/étiquette de l’objet dans le masque<br>`Required, String` | `"cat"` |
| `isCrowd` | Indique si le masque est autour de la foule d’objets<br>`Optional, Bool` | `0` |
| `polygon` | Coordonnées de polygone pour l’objet<br>`Required,  List of list for multiple segments of the same instance. Float values in the range [0,1]` | ` [[0.577, 0.689, 0.567, 0.689, 0.559, 0.686]]` |


Exemple de fichier JSON pour la segmentation d’instance :

```python
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "can", "isCrowd": 0, "polygon": [[0.577, 0.689, 0.567, 0.689, 0.559, 0.686, 0.380, 0.593, 0.304, 0.555, 0.294, 0.545, 0.290, 0.534, 0.274, 0.512, 0.2705, 0.496, 0.270, 0.478, 0.284, 0.453, 0.308, 0.432, 0.326, 0.423, 0.356, 0.415, 0.418, 0.417, 0.635, 0.493, 0.683, 0.507, 0.701, 0.518, 0.709, 0.528, 0.713, 0.545, 0.719, 0.554, 0.719, 0.579, 0.713, 0.597, 0.697, 0.621, 0.695, 0.629, 0.631, 0.678, 0.619, 0.683, 0.595, 0.683, 0.577, 0.689]]}]}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "carton", "isCrowd": 0, "polygon": [[0.240, 0.65, 0.234, 0.654, 0.230, 0.647, 0.210, 0.512, 0.202, 0.403, 0.182, 0.267, 0.184, 0.243, 0.180, 0.166, 0.186, 0.159, 0.198, 0.156, 0.396, 0.162, 0.408, 0.169, 0.406, 0.217, 0.414, 0.249, 0.422, 0.262, 0.422, 0.569, 0.342, 0.569, 0.334, 0.572, 0.320, 0.585, 0.308, 0.624, 0.306, 0.648, 0.240, 0.657]]}, {"label": "milk_bottle",  "isCrowd": 0, "polygon": [[0.675, 0.732, 0.635, 0.731, 0.621, 0.725, 0.573, 0.717, 0.516, 0.717, 0.505, 0.720, 0.462, 0.722, 0.438, 0.719, 0.396, 0.719, 0.358, 0.714, 0.334, 0.714, 0.322, 0.711, 0.312, 0.701, 0.306, 0.687, 0.304, 0.663, 0.308, 0.630, 0.320, 0.596, 0.32, 0.588, 0.326, 0.579]]}]}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "water_bottle", "isCrowd": 0, "polygon": [[0.334, 0.626, 0.304, 0.621, 0.254, 0.603, 0.164, 0.605, 0.158, 0.602, 0.146, 0.602, 0.142, 0.608, 0.094, 0.612, 0.084, 0.599, 0.080, 0.585, 0.080, 0.539, 0.082, 0.536, 0.092, 0.533, 0.126, 0.530, 0.132, 0.533, 0.144, 0.533, 0.162, 0.525, 0.172, 0.525, 0.186, 0.521, 0.196, 0.521 ]]}, {"label": "milk_bottle", "isCrowd": 0, "polygon": [[0.392, 0.773, 0.380, 0.732, 0.379, 0.767, 0.367, 0.755, 0.362, 0.735, 0.362, 0.714, 0.352, 0.644, 0.352, 0.611, 0.362, 0.597, 0.40, 0.593, 0.444,  0.494, 0.588, 0.515, 0.585, 0.621, 0.588, 0.671, 0.582, 0.713, 0.572, 0.753 ]]}]}
```

![Exemple d’image pour la segmentation d’instance.](media/reference-automl-images-schema/instance-segmentation-predictions.jpg)

## <a name="data-format-for-inference"></a>Format de données pour l’inférence

Dans cette section, nous documentons le format de données d’entrée requis pour effectuer des prédictions lors de l’utilisation d’un modèle déployé. Tout format d’image mentionné plus haut est accepté avec le type de contenu `application/octet-stream`.

### <a name="input-format"></a>Format d’entrée

Voici le format d’entrée nécessaire pour générer des prédictions sur n’importe quelle tâche à l’aide d’un point de terminaison de modèle spécifique à la tâche. Après avoir [déployé le modèle](how-to-auto-train-image-models.md#register-and-deploy-model), nous pouvons utiliser l’extrait de code suivant pour obtenir des prédictions pour toutes les tâches.

```python
# input image for inference
sample_image = './test_image.jpg'
# load image data
data = open(sample_image, 'rb').read()
# set the content type
headers = {'Content-Type': 'application/octet-stream'}
# if authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'
# make the request and display the response
response = requests.post(scoring_uri, data, headers=headers)
```
### <a name="output-format"></a>Format de sortie

Les prédictions effectuées sur les points de terminaison de modèle suivent une structure différente selon le type de tâche. Cette section explore les formats de données de sortie pour les tâches de classification d’image multiclasse et multi-étiquette, de détection d’objets et de segmentation d’instance.  

#### <a name="image-classification"></a>Classification d’images

Le point de terminaison de la classification d’image retourne toutes les étiquettes du jeu de données et leurs scores de probabilité pour l’image d’entrée au format suivant.

```json
{
   "filename":"/tmp/tmppjr4et28",
   "probs":[
      2.098e-06,
      4.783e-08,
      0.999,
      8.637e-06
   ],
   "labels":[
      "can",
      "carton",
      "milk_bottle",
      "water_bottle"
   ]
}
```

#### <a name="image-classification-multi-label"></a>Classification d’images multi-étiquette

Pour la classification d’image à multi-étiquettes, le point de terminaison de modèle retourne les étiquettes et leurs probabilités.

```json
{
   "filename":"/tmp/tmpsdzxlmlm",
   "probs":[
      0.997,
      0.960,
      0.982,
      0.025
   ],
   "labels":[
      "can",
      "carton",
      "milk_bottle",
      "water_bottle"
   ]
}
```

#### <a name="object-detection"></a>Détection d’objets

Le modèle de détection d’objets retourne plusieurs zones avec les coordonnées de l’angle supérieur gauche et de l’angle inférieur droit, ainsi que l’étiquette de zone et le score de confiance.

```json
{
   "filename":"/tmp/tmpdkg2wkdy",
   "boxes":[
      {
         "box":{
            "topX":0.224,
            "topY":0.285,
            "bottomX":0.399,
            "bottomY":0.620
         },
         "label":"milk_bottle",
         "score":0.937
      },
      {
         "box":{
            "topX":0.664,
            "topY":0.484,
            "bottomX":0.959,
            "bottomY":0.812
         },
         "label":"can",
         "score":0.891
      },
      {
         "box":{
            "topX":0.423,
            "topY":0.253,
            "bottomX":0.632,
            "bottomY":0.725
         },
         "label":"water_bottle",
         "score":0.876
      }
   ]
}
```
#### <a name="instance-segmentation"></a>Segmentation d’instances

Dans la segmentation de l’instance, la sortie est constituée de plusieurs zones avec les coordonnées en haut à gauche et en bas à droite, les étiquettes, les scores de confiance et les polygones (pas les masques). Ici, les valeurs de polygone sont au même format que nous avons abordé dans la section des schémas.

```json
{
   "filename":"/tmp/tmpi8604s0h",
   "boxes":[
      {
         "box":{
            "topX":0.679,
            "topY":0.491,
            "bottomX":0.926,
            "bottomY":0.810
         },
         "label":"can",
         "score":0.992,
         "polygon":[
            [
               0.82, 0.811, 0.771, 0.810, 0.758, 0.805, 0.741, 0.797, 0.735, 0.791, 0.718, 0.785, 0.715, 0.778, 0.706, 0.775, 0.696, 0.758, 0.695, 0.717, 0.698, 0.567, 0.705, 0.552, 0.706, 0.540, 0.725, 0.520, 0.735, 0.505, 0.745, 0.502, 0.755, 0.493
            ]
         ]
      },
      {
         "box":{
            "topX":0.220,
            "topY":0.298,
            "bottomX":0.397,
            "bottomY":0.601
         },
         "label":"milk_bottle",
         "score":0.989,
         "polygon":[
            [
               0.365, 0.602, 0.273, 0.602, 0.26, 0.595, 0.263, 0.588, 0.251, 0.546, 0.248, 0.501, 0.25, 0.485, 0.246, 0.478, 0.245, 0.463, 0.233, 0.442, 0.231, 0.43, 0.226, 0.423, 0.226, 0.408, 0.234, 0.385, 0.241, 0.371, 0.238, 0.345, 0.234, 0.335, 0.233, 0.325, 0.24, 0.305, 0.586, 0.38, 0.592, 0.375, 0.598, 0.365
            ]
         ]
      },
      {
         "box":{
            "topX":0.433,
            "topY":0.280,
            "bottomX":0.621,
            "bottomY":0.679
         },
         "label":"water_bottle",
         "score":0.988,
         "polygon":[
            [
               0.576, 0.680, 0.501, 0.680, 0.475, 0.675, 0.460, 0.625, 0.445, 0.630, 0.443, 0.572, 0.440, 0.560, 0.435, 0.515, 0.431, 0.501, 0.431, 0.433, 0.433, 0.426, 0.445, 0.417, 0.456, 0.407, 0.465, 0.381, 0.468, 0.327, 0.471, 0.318
            ]
         ]
      }
   ]
}
```

> [!NOTE]
> Les images utilisées dans cet article proviennent du jeu de données Fridge Objects, copyright© Microsoft Corporation, et sont disponibles sur [computervision-recipes/01_training_introduction.ipynb](https://github.com/microsoft/computervision-recipes/blob/master/scenarios/detection/01_training_introduction.ipynb) en vertu de la [licence MIT](https://github.com/microsoft/computervision-recipes/blob/master/LICENSE). 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [Préparation des données pour les tâches d’apprentissage de vision par ordinateur avec le ML automatisé](how-to-prepare-datasets-for-automl-images.md).
