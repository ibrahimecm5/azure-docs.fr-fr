---
title: Inférence locale à l’aide d’ONNX pour une image AutoML
titleSuffix: Azure Machine Learning
description: Utilisez ONNX avec le ML automatisé Azure Machine Learning pour effectuer des prédictions sur des modèles de vision par ordinateur pour la classification, la détection d’objets et la segmentation.
author: vadthyavath
ms.author: rvadthyavath
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.date: 10/18/2021
ms.openlocfilehash: 01839f2a6f16584148d4cab86e07f3da0eefee43
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076544"
---
# <a name="make-predictions-with-onnx-on-computer-vision-models-from-automl"></a>Effectuez des prédictions avec ONNX sur des modèles de vision par ordinateur à parti d’AutoML 

Dans cet article, vous allez apprendre à utiliser ONNX (Open Neural Network Exchange) pour effectuer des prédictions sur des modèles de vision par ordinateur générées à partir de ML automatisé (AutoML) dans Azure Machine Learning. 

Pour utiliser ONNX pour les prédictions, vous devez : 
 
1. Télécharger les fichiers de modèle ONNX à partir d’une exécution d’entraînement AutoML.
1. Comprendre les entrées et les sorties d’un modèle ONNX.
1. Prétraiter vos données afin qu’elles se trouvent dans le format requis pour les images d’entrée.
1. Procéder à l’inférence avec le runtime ONNX pour Python.
1. Visualiser les prédictions de la détection d’objets et les tâches de segmentation.

[ONNX](https://onnx.ai/about.html) est une norme ouverte pour les modèles de machine learning et de deep learning. Elle permet l’importation et l’exportation (interopérabilité) des modèles sur les infrastructures d’IA populaires. Pour plus de détails, explorez le [projet ONNX GitHub](https://github.com/onnx/onnx).

Le [Runtime ONNX](https://onnxruntime.ai/index.html) est un projet open source qui prend en charge l’inférence multiplateforme. Le runtime ONNX fournit des API sur des langages de programmation (notamment Python, C++, C#, C, Java et JavaScript). Vous pouvez utiliser ces API pour effectuer une inférence sur les images d’entrée. Une fois que le modèle a été exporté au format ONNX, vous pouvez utiliser ces API sur n’importe quel langage de programmation dont votre projet a besoin. 

Dans ce guide, vous allez apprendre à utiliser [les API Python pour le runtime ONNX](https://onnxruntime.ai/docs/get-started/with-python.html) afin d’effectuer des prédictions sur des images pour des tâches de vision populaires. Vous pouvez utiliser ces modèles exportés par ONNX dans différents langages.

## <a name="prerequisites"></a>Prérequis

* Procurez-vous un modèle de vision par ordinateur AutoML pour toutes les tâches d’images prises en charge : classification, détection d’objets ou segmentation. [En savoir plus sur la prise en charge AutoML pour les tâches de vision par ordinateur](how-to-auto-train-image-models.md).

* Installez le package [onnxruntime](https://onnxruntime.ai/docs/get-started/with-python.html). Les méthodes décrites dans cet article ont été testées avec les versions 1.3.0 à 1.8.0.

## <a name="download-onnx-model-files"></a>Télécharger les fichiers de modèle ONNX

Vous pouvez télécharger les fichiers de modèle ONNX à partir d’une exécution AutoML à l’aide de l’interface utilisateur Azure Machine Learning studio ou du kit de développement logiciel (SDK) Python Azure Machine Learning. Nous vous recommandons de les télécharger via le kit de développement logiciel (SDK) avec le nom de l’expérience et l’ID d’exécution parent. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

Dans Azure Machine Learning studio, accédez à votre expérience en utilisant le lien hypertexte vers l’expérience générée dans le notebook de formation, ou en sélectionnant le nom de l’expérience dans l’onglet **Expériences** sous **Ressources**. Sélectionnez ensuite la meilleure exécution enfant. 

Dans la meilleure exécution enfant, accédez à **Sorties + journaux** > **train_artifacts**. Utilisez le bouton **Télécharger** pour télécharger manuellement les fichiers suivants :

- *labels.json* : fichier qui contient toutes les classes ou étiquettes du jeu de données d’apprentissage.
- *model.onnx* : modèle au format ONNX. 

![Capture d’écran montrant des sélections pour le téléchargement de fichiers de modèle ONNX.](./media/how-to-inference-onnx-automl-image-models/onnx-files-manual-download.png)

Enregistrez les fichiers de modèle téléchargés dans un répertoire. L’exemple de cet article utilise le répertoire *./automl_models*. 

### <a name="azure-machine-learning-python-sdk"></a>SDK Python Azure Machine Learning

Avec le kit de développement logiciel (SDK), vous pouvez sélectionner la meilleure expérience enfant (par métrique principale) avec le nom de l’expérience et l’ID d’exécution parent. Ensuite, vous pouvez télécharger les fichiers *labels.json* et *model.onnx*.

Le code suivant retourne la meilleure expérience enfant en fonction de la métrique principale pertinente.

```python
# Select the best child run
from azureml.train.automl.run import AutoMLRun

run_id = "" # Specify the run ID
automl_image_run = AutoMLRun(experiment=experiment, run_id=run_id)
best_child_run = automl_image_run.get_best_child()
```

Téléchargez le fichier *labels.json* qui contient toutes les classes et étiquettes du jeu de données d’apprentissage.

```python
import json

labels_file = "automl_models/labels.json"
best_child_run.download_file(name="train_artifacts/labels.json", output_file_path=labels_file)

```

Téléchargez le fichier *model.onnx*.

```python
onnx_model_path = "automl_models/model.onnx"
best_child_run.download_file(name="train_artifacts/model.onnx", output_file_path=onnx_model_path)
```

Après l’étape de téléchargement du modèle, utilisez le package Python du runtime ONNX pour effectuer une inférence à l’aide du fichier *model.onnx*. À des fins de démonstration, cet article utilise les jeux de données de la rubrique [Comment préparer les jeux de données d’images](how-to-prepare-datasets-for-automl-images.md) pour chaque tâche de vision. 

Nous avons formé les modèles de toutes les tâches de vision avec leurs jeux de données respectifs pour illustrer l’inférence de modèle ONNX.
 
## <a name="load-the-labels-and-onnx-model-files"></a>Charger les étiquettes et les fichiers de modèle ONNX

L’extrait de code suivant charge *labels.json*, dans lequel les noms de classe sont triés. Autrement dit, si le modèle ONNX prédit un ID d’étiquette de 2, il correspond au nom d’étiquette donné au troisième index du fichier *labels.json*.

```python
import onnxruntime

labels_file = "automl_models/labels.json"
with open(labels_file) as f:
    classes = json.load(f)
print(classes)
try:
    session = onnxruntime.InferenceSession(onnx_model_path)
    print("ONNX model loaded...")
except Exception as e: 
    print("Error loading ONNX file: ",str(e))
```

## <a name="get-expected-input-and-output-details-for-an-onnx-model"></a>Obtenir les détails d’entrée et de sortie attendus d’un modèle ONNX

Une fois que vous disposez du modèle, il est important de connaître certains détails spécifiques aux modèles et aux tâches. Ces détails incluent le nombre d’entrées et le nombre de sorties, la forme d’entrée ou le format attendu pour le prétraitement de l’image, ainsi que la forme de sortie, afin de connaître les sorties spécifiques à un modèle ou à une tâche.

```python
sess_input = session.get_inputs()
sess_output = session.get_outputs()
print(f"No. of inputs : {len(sess_input)}, No. of outputs : {len(sess_output)}")

for idx, input_ in enumerate(range(len(sess_input))):
    input_name = sess_input[input_].name
    input_shape = sess_input[input_].shape
    input_type = sess_input[input_].type
    print(f"{idx} Input name : { input_name }, Input shape : {input_shape}, \
    Input type  : {input_type}")  

for idx, output in enumerate(range(len(sess_output))):
    output_name = sess_output[output].name
    output_shape = sess_output[output].shape
    output_type = sess_output[output].type
    print(f" {idx} Output name : {output_name}, Output shape : {output_shape}, \
    Output type  : {output_type}") 
``` 

### <a name="expected-input-and-output-formats-for-the-onnx-model"></a>Formats d’entrée et de sortie attendus pour le modèle ONNX

Chaque modèle ONNX a un ensemble prédéfini de formats d’entrée et de sortie.

# <a name="multi-class-image-classification"></a>[Classification d’images multiclasse](#tab/multi-class)

Cet exemple applique le modèle formé sur le jeu de données [fridgeObjects](https://cvbp-secondary.z19.web.core.windows.net/datasets/image_classification/fridgeObjects.zip) de 134 images et 4 classes/étiquettes pour expliquer l’inférence de modèle ONNX. Pour plus d’informations sur l’apprentissage d’une tâche de classification d’images, consultez le [notebook d’images multiclasses](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-classification-multiclass).

### <a name="input-format"></a>Format d’entrée
    
L’entrée est une image prétraitée.

| Nom d’entrée  | Forme d’entrée  | Type d’entrée | Description |
| -------- |----------:|-----|--------|
| input1 | `(batch_size, num_channels, height, width)` | ndarray(float) | L’entrée est une image prétraitée, avec la forme `(1, 3, 224, 224)` pour une taille de lot de 1, et une hauteur et une largeur de 224. Ces chiffres correspondent aux valeurs utilisées pour `crop_size` dans l’exemple d’apprentissage. |
    

### <a name="output-format"></a>Format de sortie

La sortie est un tableau de logits pour toutes les classes/étiquettes.
         
| Nom de sortie   | Forme de sortie  | Type de sortie | Description |
| -------- |----------|-----|------|
| output1 | `(batch_size, num_classes)` | ndarray(float) | Le modèle retourne logits (sans `softmax`). Par exemple, pour les classes de taille de lot 1 et 4, il retourne `(1, 4)`. |

# <a name="multi-label-image-classification"></a>[Classification d’images multiétiquette](#tab/multi-label)

Cet exemple utilise le modèle formé sur le [jeu de données fridgeObjects à plusieurs étiquettes](https://cvbp-secondary.z19.web.core.windows.net/datasets/image_classification/multilabelFridgeObjects.zip) avec 128 images et 4 classes/étiquettes pour expliquer l’inférence de modèle ONNX. Pour plus d’informations sur l’apprentissage de modèle pour la classification d’images à plusieurs étiquettes, consultez le [notebook de classification d’images à plusieurs étiquettes](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-classification-multilabel).

### <a name="input-format"></a>Format d’entrée

L’entrée est une image prétraitée.

| Nom d’entrée       | Forme d’entrée  | Type d’entrée | Description |
| -------- |----------|-----|--------|
| input1 | `(batch_size, num_channels, height, width)` | ndarray(float) | L’entrée est une image prétraitée, avec la forme `(1, 3, 224, 224)` pour une taille de lot de 1, et une hauteur et une largeur de 224. Ces chiffres correspondent aux valeurs utilisées pour `crop_size` dans l’exemple d’apprentissage. |
        
### <a name="output-format"></a>Format de sortie

La sortie est un tableau de logits pour toutes les classes/étiquettes.
    
      
| Nom de sortie       | Forme de sortie  | Type de sortie | Description |
| -------- |----------|-----|------
| output1 | `(batch_size, num_classes)` | ndarray(float) | Le modèle retourne logits (sans `sigmoid`). Par exemple, pour les classes de taille de lot 1 et 4, il retourne `(1, 4)`. |


# <a name="object-detection-with-faster-r-cnn"></a>[Détection d’objets avec Faster R-CNN](#tab/object-detect-cnn)

Cet exemple de détection d’objets utilise le modèle formé sur le [jeu de données de détection fridgeObjects](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjects.zip) de 128 images et 4 classes/étiquettes pour expliquer l’inférence de modèle ONNX. Cet exemple entraîne des modèles Faster R-CNN plus rapides pour illustrer les étapes d’inférence. Pour plus d’informations sur l’apprentissage des modèles de détection d’objets, consultez le [notebook de détection d'objet](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-object-detection).

### <a name="input-format"></a>Format d’entrée

L’entrée est une image prétraitée.

| Nom d’entrée       | Forme d’entrée  | Type d’entrée | Description |
| -------- |----------|-----|--------|
| Entrée | `(batch_size, num_channels, height, width)` | ndarray(float) | L’entrée est une image prétraitée, avec la forme `(1, 3, 600, 800)` pour une taille de lot de 1, une hauteur de 600 et une largeur de 800.|
        
    
### <a name="output-format"></a>Format de sortie

La sortie est un tuple de zones, d’étiquettes et de scores.
  
| Nom de sortie       | Forme de sortie  | Type de sortie | Description |
| -------- |----------|-----|------|
| Zones | `(n_boxes, 4)`, où chaque zone a `x_min, y_min, x_max, y_max` | ndarray(float) | Le modèle renvoie *n* zones avec leurs coordonnées en haut à gauche et en bas à droite. |
| Étiquettes | `(n_boxes)`| ndarray(float) | ID d’étiquette ou de classe d’un objet dans chaque zone. |  
| Scores | `(n_boxes)` | ndarray(float) | Score de confiance d’un objet dans chaque zone. |    


# <a name="object-detection-with-yolo"></a>[Détection d’objets avec YOLO](#tab/object-detect-yolo)

Cet exemple de détection d’objets utilise le modèle formé sur le [jeu de données de détection fridgeObjects](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjects.zip) de 128 images et 4 classes/étiquettes pour expliquer l’inférence de modèle ONNX. Cet exemple entraîne des modèles R-CNN plus rapides pour illustrer les étapes d’inférence. Pour plus d’informations sur l’apprentissage des modèles de détection d’objets, consultez le [notebook de détection d'objet](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-object-detection). 

### <a name="input-format"></a>Format d’entrée

L’entrée est une image prétraitée, avec la forme `(1, 3, 640, 640)` pour une taille de lot de 1, et une hauteur et une largeur de 640. Ces chiffres correspondent aux valeurs utilisées dans l’exemple d’apprentissage.        

| Nom d’entrée       | Forme d’entrée  | Type d’entrée | Description |
| -------- |----------|-----|--------|
| Entrée | `(batch_size, num_channels, height, width)` | ndarray(float) | L’entrée est une image prétraitée, avec la forme `(1, 3, 600, 800)` pour une taille de lot de 1, une hauteur de 600 et une largeur de 800.|
        
### <a name="output-format"></a>Format de sortie
La sortie est une liste de zones, d’étiquettes et de scores. Pour YOLO, vous avez besoin de la première sortie pour extraire des zones, des étiquettes et des scores.
    
| Nom de sortie       | Forme de sortie  | Type de sortie | Description |
| -------- |----------|-----|------|
| Sortie | `(n_boxes, 6)`, où chaque zone a `x_min, y_min, x_max, y_max, confidence_score, class_id` | ndarray(float) | Le modèle renvoie *n* zones avec leurs coordonnées en haut à gauche et en bas à droite, ainsi que les scores de confiance, les ID de classe et les ID d’étiquette des objets. |

# <a name="instance-segmentation"></a>[Segmentation d’instance](#tab/instance-segmentation)

Pour cet exemple de segmentation d’instance, utilisez le modèle Mask R-CNN qui a été formé sur le [jeu de données fridgeObjects](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjectsMask.zip) avec 128 images et 4 classes/étiquettes pour expliquer l’inférence de modèle ONNX. Pour plus d’informations sur l’apprentissage du modèle de segmentation d’instance, consultez le [notebook sur la segmentation d’instance](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-instance-segmentation).

### <a name="input-format"></a>Format d’entrée

L’entrée est une image prétraitée. Le modèle ONNX pour Mask R-CNN a été exporté pour fonctionner avec des images de différentes formes. Pour de meilleures performances, nous vous recommandons de les redimensionner sur une taille fixe cohérente avec des tailles d’images de formation.
    
| Nom d’entrée       | Forme d’entrée  | Type d’entrée | Description |
| -------- |----------|-----|--------|
| Entrée | `(batch_size, num_channels, height, width)` | ndarray(float) | L’entrée est une image prétraitée, avec la forme `(1, 3, input_image_height, input_image_width)` pour une taille de lot de 1, et une hauteur et une largeur similaires à celles d’une image d’entrée. |
        
    
### <a name="output-format"></a>Format de sortie

La sortie est un tuple de zones (instances), d’étiquettes et de scores.
    
| Nom de sortie       | Forme de sortie  | Type de sortie | Description |
| -------- |----------|-----|------|
| Zones | `(n_boxes, 4)`, où chaque zone a `x_min, y_min, x_max, y_max` | ndarray(float) | Le modèle renvoie *n* zones avec leurs coordonnées en haut à gauche et en bas à droite. |
| Étiquettes | `(n_boxes)`| ndarray(float) | ID d’étiquette ou de classe d’un objet dans chaque zone. |  
| Scores | `(n_boxes)` | ndarray(float) | Score de confiance d’un objet dans chaque zone. |    
| Masques | `(n_boxes, 1, height, width)` | ndarray(float) | Masques (polygones) des objets détectés avec la hauteur et la largeur de la forme d’une image d’entrée. |    

---

## <a name="preprocessing"></a>Prétraitement

# <a name="multi-class-image-classification"></a>[Classification d’images multiclasse](#tab/multi-class)

Effectuez les étapes de prétraitement suivantes pour l’inférence de modèle ONNX :

1. Convertissez l’image en RVB.
2. Redimensionnez l’image avec les valeurs `valid_resize_size` et `valid_resize_size` qui correspondent aux valeurs utilisées dans la transformation du jeu de données de validation au cours de l’apprentissage. La valeur par défaut pour `valid_resize_size` est 256.
3. Rognez et centrez l’image sur `height_onnx_crop_size` et `width_onnx_crop_size`. Cela correspond à `valid_crop_size` avec une valeur par défaut de 224.
4. Remplacez `HxWxC` par `CxHxW`.
5. Convertissez au type float.
6. Normalisez avec `mean` = `[0.485, 0.456, 0.406]` et `std` = `[0.229, 0.224, 0.225]` d’ImageNet.

Si vous avez choisi des valeurs différentes pour les [hyperparamètres](how-to-auto-train-image-models.md#configure-model-algorithms-and-hyperparameters) `valid_resize_size` et `valid_crop_size` pendant l’apprentissage, ces valeurs doivent être utilisées.

Obtenez la forme d’entrée nécessaire pour le modèle ONNX.

```python
batch, channel, height_onnx_crop_size, width_onnx_crop_size = session.get_inputs()[0].shape
batch, channel, height_onnx_crop_size, width_onnx_crop_size
```

### <a name="without-pytorch"></a>Sans PyTorch

```python
def preprocess(image, resize_size, crop_size_onnx):
    """perform pre-processing on raw input image
  
    :param image: raw input image
    :type image: PIL image
    :param resize_size: value to resize the image
    :type image: Int
    :param crop_size_onnx: expected crop size of an input image in onnx model
    :type crop_size_onnx: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    # resize
    image = image.resize((resize_size, resize_size))

    # center crop
    left = (resize_size - crop_size_onnx)/2
    top = (resize_size - crop_size_onnx)/2
    right = (resize_size + crop_size_onnx)/2
    bottom = (resize_size + crop_size_onnx)/2
    image = image.crop((left, top, right, bottom))
    np_image = np.array(image)

    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image

# you can modify resize_size based on your trained model
resize_size = 256 

# height and width will be the same for classification
crop_size_onnx = height_onnx_crop_size 
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)

```

### <a name="with-pytorch"></a>Avec PyTorch

```python
import torch
from torchvision import transforms
# tested with torch version: '1.7.1', torchvision version: '0.8.2')

def _make_3d_tensor(x) -> torch.Tensor:
    """This function is for images that have less channels.

    :param x: input tensor
    :type x: torch.Tensor
    :return: return a tensor with the correct number of channels
    :rtype: torch.Tensor
    """
    return x if x.shape[0] == 3 else x.expand((3, x.shape[1], x.shape[2]))

def preprocess(image, resize_size, crop_size_onnx):
    transform = transforms.Compose([
        transforms.Resize(resize_size),
        transforms.CenterCrop(crop_size_onnx),
        transforms.ToTensor(),
        transforms.Lambda(_make_3d_tensor),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])])
    
    img_data = transform(image)
    img_data = img_data.numpy()
    img_data = np.expand_dims(img_data, axis=0)
    return img_data

# you can modify resize_size based on your trained model
resize_size = 256

#height and width will be the same for classification
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)
```

# <a name="multi-label-image-classification"></a>[Classification d’images multiétiquette](#tab/multi-label)

Effectuez les étapes de prétraitement suivantes pour l’inférence de modèle ONNX. Ces étapes sont les mêmes pour la classification d’images multi-classe.

1. Convertissez l’image en RVB.
2. Redimensionnez l’image avec les valeurs `valid_resize_size` et `valid_resize_size` qui correspondent aux valeurs utilisées dans la transformation du jeu de données de validation au cours de l’apprentissage. La valeur par défaut pour `valid_resize_size` est 256.
3. Rognez et centrez l’image sur `height_onnx_crop_size` et `width_onnx_crop_size`. Cela correspond à `valid_crop_size` avec une valeur par défaut de 224.
4. Remplacez `HxWxC` par `CxHxW`.
5. Convertissez au type float.
6. Normalisez avec `mean` = `[0.485, 0.456, 0.406]` et `std` = `[0.229, 0.224, 0.225]` d’ImageNet.

Si vous avez choisi des valeurs différentes pour les [hyperparamètres](how-to-auto-train-image-models.md#configure-model-algorithms-and-hyperparameters) `valid_resize_size` et `valid_crop_size` pendant l’apprentissage, ces valeurs doivent être utilisées.

Obtenez la forme d’entrée nécessaire pour le modèle ONNX.

```python
batch, channel, height_onnx_crop_size, width_onnx_crop_size = session.get_inputs()[0].shape
batch, channel, height_onnx_crop_size, width_onnx_crop_size
```

### <a name="without-pytorch"></a>Sans PyTorch

```python
def preprocess(image, resize_size, crop_size_onnx):
    """perform pre-processing on raw input image
  
    :param image: raw input image
    :type image: PIL image
    :param resize_size: value to resize the image
    :type image: Int
    :param crop_size_onnx: expected crop size of an input image in onnx model
    :type crop_size_onnx: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    # resize
    image = image.resize((resize_size, resize_size))

    # center crop
    left = (resize_size - crop_size_onnx)/2
    top = (resize_size - crop_size_onnx)/2
    right = (resize_size + crop_size_onnx)/2
    bottom = (resize_size + crop_size_onnx)/2
    image = image.crop((left, top, right, bottom))
    np_image = np.array(image)

    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image

# you can modify resize_size based on your trained model
resize_size = 256 

# height and width will be the same for classification
crop_size_onnx = height_onnx_crop_size 
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)

```

### <a name="with-pytorch"></a>Avec PyTorch

```python
import torch
from torchvision import transforms
# tested with torch version: '1.7.1', torchvision version: '0.8.2')

def _make_3d_tensor(x) -> torch.Tensor:
    """This function is for images that have less channels.

    :param x: input tensor
    :type x: torch.Tensor
    :return: return a tensor with the correct number of channels
    :rtype: torch.Tensor
    """
    return x if x.shape[0] == 3 else x.expand((3, x.shape[1], x.shape[2]))

def preprocess(image, resize_size, crop_size_onnx):
    transform = transforms.Compose([
        transforms.Resize(resize_size),
        transforms.CenterCrop(crop_size_onnx),
        transforms.ToTensor(),
        transforms.Lambda(_make_3d_tensor),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])])
    
    img_data = transform(image)
    img_data = img_data.numpy()
    img_data = np.expand_dims(img_data, axis=0)
    return img_data

# you can modify resize_size based on your trained model
resize_size = 256

# height and width will be the same for classification
crop_size_onnx = height_onnx_crop_size 
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)
```


# <a name="object-detection-with-faster-r-cnn"></a>[Détection d’objets avec Faster R-CNN](#tab/object-detect-cnn)

Pour la détection d’objets avec l’algorithme Faster R-CNN, suivez les mêmes étapes de prétraitement que pour la classification d’images, à l’exception du rognage d’image. Vous pouvez redimensionner l’image avec la hauteur `600` et la largeur `800` et récupérer la hauteur et la largeur d’entrée attendues avec le code suivant.

```python
batch, channel, height_onnx, width_onnx = session.get_inputs()[0].shape
batch, channel, height_onnx, width_onnx
```

Ensuite, effectuez les étapes de prétraitement.

```python
def preprocess(image, height_onnx, width_onnx):
    """perform pre-processing on raw input image
    
    :param image: raw input image
    :type image: PIL image
    :param height_onnx: expected height of an input image in onnx model
    :type height_onnx: Int
    :param width_onnx: expected width of an input image in onnx model
    :type width_onnx: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    image = image.resize((width_onnx, height_onnx))
    np_image = np.array(image)
    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # Normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
        
        
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image

test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, height_onnx, width_onnx)
```

# <a name="object-detection-with-yolo"></a>[Détection d’objets avec YOLO](#tab/object-detect-yolo)

Pour la détection d’objets avec l’algorithme YOLO, suivez les mêmes étapes de prétraitement que pour la classification d’images, à l’exception du rognage d’image. Vous pouvez redimensionner l’image avec la hauteur `600` et la largeur `800` et récupérer la hauteur et la largeur d’entrée attendues avec le code suivant.

```python
batch, channel, height_onnx, width_onnx = session.get_inputs()[0].shape
batch, channel, height_onnx, width_onnx
```

En ce qui concerne le prétraitement requis pour YOLO, reportez-vous à [yolo_onnx_preprocessing_utils.py](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml/image-object-detection).

```python
from yolo_onnx_preprocessing_utils import preprocess

test_image_path = "automl_models_od_yolo/test_image.jpg"
img_data, pad = preprocess(test_image_path)
```

# <a name="instance-segmentation"></a>[Segmentation d’instance](#tab/instance-segmentation)

Effectuez les étapes de prétraitement suivantes pour l’inférence de modèle ONNX :

1. Convertissez l’image en RVB.
2. Redimensionnez l’image. 
3. Remplacez `HxWxC` par `CxHxW`.
4. Convertissez au type float.
5. Normalisez avec `mean` = `[0.485, 0.456, 0.406]` et `std` = `[0.229, 0.224, 0.225]` d’ImageNet.

Pour `resize_height` et `resize_width`, vous pouvez également utiliser les valeurs que vous avez utilisées lors de l’apprentissage, limitées par les [hyperparamètres](how-to-auto-train-image-models.md#configure-model-algorithms-and-hyperparameters) `min_size` et `max_size` pour Mask R-CNN.

```python
def preprocess(image, resize_height, resize_width):
    """perform pre-processing on raw input image
        
    :param image: raw input image
    :type image: PIL image
    :param resize_height: resize height of an input image
    :type resize_height: Int
    :param resize_width: resize width of an input image
    :type resize_width: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    image = image.resize((resize_width,resize_height))
    np_image = np.array(image)

    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image
# use height and width based on the trained model
resize_height, resize_width = 600, 800 
test_image_path = 'automl_models/test_image.jpg'
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_height, resize_width)

```

---

## <a name="inference-with-onnx-runtime"></a>Inférence avec le runtime ONNX

L’inférence avec le runtime ONNX diffère pour chaque tâche de vision par ordinateur.

# <a name="multi-class-image-classification"></a>[Classification d’images multiclasse](#tab/multi-class)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx inference session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: scores with shapes
            (1, No. of classes in training dataset) 
    :rtype: ndarray
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    print(f"No. of inputs : {len(sess_input)}, No. of outputs : {len(sess_output)}")    
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    scores = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return scores[0]

scores = get_predictions_from_ONNX(session, img_data)
```

# <a name="multi-label-image-classification"></a>[Classification d’images multiétiquette](#tab/multi-label)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx inference session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: scores with shapes
            (1, No. of classes in training dataset) 
    :rtype: ndarray
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    print(f"No. of inputs : {len(sess_input)}, No. of outputs : {len(sess_output)}")    
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    scores = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return scores[0]

scores = get_predictions_from_ONNX(session, img_data)
```

# <a name="object-detection-with-faster-r-cnn"></a>[Détection d’objets avec Faster R-CNN](#tab/object-detect-cnn)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx model session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: boxes, labels , scores 
            (No. of boxes, 4) (No. of boxes,) (No. of boxes,)
    :rtype: tuple
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    boxes, labels, scores = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return boxes, labels, scores

boxes, labels, scores = get_predictions_from_ONNX(session, img_data)
```

# <a name="object-detection-with-yolo"></a>[Détection d’objets avec YOLO](#tab/object-detect-yolo)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx model session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: boxes, labels , scores 
    :rtype: list
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    pred = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return pred[0]

result = get_predictions_from_ONNX(session, img_data)

```

# <a name="instance-segmentation"></a>[Segmentation d’instance](#tab/instance-segmentation)

Le modèle de segmentation d’instance prédit les zones, les étiquettes, les scores et les masques. ONNX génère un masque prédit par instance, ainsi que des zones englobantes et un score de confiance de classe correspondants. Vous devrez peut-être convertir le masque binaire en polygone si nécessaire.

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx model session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: boxes, labels , scores , masks with shapes
            (No. of instances, 4) (No. of instances,) (No. of instances,)
            (No. of instances, 1, HEIGHT, WIDTH))  
    :rtype: tuple
    """
    
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    boxes, labels, scores, masks = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return boxes, labels, scores, masks


boxes, labels, scores, masks = get_predictions_from_ONNX(session, img_data)
```

---

## <a name="postprocessing"></a>Post-traitement

# <a name="multi-class-image-classification"></a>[Classification d’images multiclasse](#tab/multi-class)

Appliquez `softmax()` à des valeurs prédites pour obtenir des scores de confiance de classification (probabilités) pour chaque classe. La prédiction sera la classe ayant la probabilité la plus élevée. 

### <a name="without-pytorch"></a>Sans PyTorch

```python

def softmax(x):
    x = x.reshape(-1)
    e_x = np.exp(x - np.max(x))
    return e_x / e_x.sum(axis=0)
conf_scores = softmax(scores).tolist()
class_idx = np.argmax(conf_scores)
print("predicted class:",(class_idx, classes[class_idx]))

```

### <a name="with-pytorch"></a>Avec PyTorch

```python

conf_scores = torch.nn.functional.softmax(torch.from_numpy(scores), dim=1).tolist()[0]
class_idx = np.argmax(conf_scores)
print("predicted class:", (class_idx, classes[class_idx]))
```

# <a name="multi-label-image-classification"></a>[Classification d’images multiétiquette](#tab/multi-label)

Cette étape diffère de la classification multiclasse. Vous devez appliquer `sigmoid` à logits (sortie ONNX) pour obtenir des scores de confiance pour la classification d’images à plusieurs étiquettes.

### <a name="without-pytorch"></a>Sans PyTorch

```python

import math

def sigmoid(x):
    return 1 / (1 + math.exp(-x))

# we apply a threshold of 0.5 on confidence scores
cutoff_threshold = .5
for class_idx, prob in enumerate([sigmoid(logit) for logit in scores[0]]):
    if prob > cutoff_threshold:
        print("predicted class:", (class_idx, classes[class_idx]))
```

### <a name="with-pytorch"></a>Avec PyTorch

```python

# we apply a threshold of 0.5 on confidence scores
cutoff_threshold = .5
conf_scores = torch.sigmoid(torch.from_numpy(scores[0])).tolist()
for class_idx, prob in enumerate(conf_scores):
    if prob > cutoff_threshold:
        print("predicted class:", (class_idx, classes[class_idx]))
```

Pour une classification multiclasse et à plusieurs étiquettes, vous pouvez suivre les mêmes étapes que celles mentionnées plus haut pour tous les algorithmes pris en charge par AutoML.


# <a name="object-detection-with-faster-r-cnn"></a>[Détection d’objets avec Faster R-CNN](#tab/object-detect-cnn)

```python
def _get_box_dims(image_shape, box):
    box_keys = ['topX', 'topY', 'bottomX', 'bottomY']
    height, width = image_shape[0], image_shape[1]

    box_dims = dict(zip(box_keys, [coordinate.item() for coordinate in box]))

    box_dims['topX'] = box_dims['topX'] * 1.0 / width
    box_dims['bottomX'] = box_dims['bottomX'] * 1.0 / width
    box_dims['topY'] = box_dims['topY'] * 1.0 / height
    box_dims['bottomY'] = box_dims['bottomY'] * 1.0 / height

    return box_dims

def _get_prediction(boxes, labels, scores, image_shape, classes):
    bounding_boxes = []
    for box, label_index, score in zip(boxes, labels, scores):
        box_dims = _get_box_dims(image_shape, box)

        box_record = {'box': box_dims,
                      'label': classes[label_index],
                      'score': score.item()}

        bounding_boxes.append(box_record)

    return bounding_boxes

bounding_boxes = _get_prediction(boxes, labels, scores, (height_onnx,width_onnx), classes)
print(json.dumps(bounding_boxes, indent=1))

# Filter the results with a threshold.
# Replace the threshold for your test scenario.
score_threshold = 0.8
filtered_bounding_boxes = []
for box in bounding_boxes:
    if box['score'] >= score_threshold:
        filtered_bounding_boxes.append(box)
```

### <a name="visualize-boxes"></a>Visualiser les zones

```python

import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
%matplotlib inline

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np = mpimg.imread(test_image_path)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size
print(img.size)

fig,ax = plt.subplots(1)
# Display the image
ax.imshow(img_np)

# Draw a box and label for each detection 
for detect in filtered_bounding_boxes:
    label = detect['label']
    box = detect['box']
    ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
    topleft_x, topleft_y = x * xmin, y * ymin
    width, height = x * (xmax - xmin), y * (ymax - ymin)
    print('{}: {}, {}, {}, {}'.format(detect['label'], topleft_x, topleft_y, width, height))
    rect = patches.Rectangle((topleft_x, topleft_y), width, height, 
                             linewidth=1, edgecolor='green',facecolor='none')

    ax.add_patch(rect)
    color = 'green'
    plt.text(topleft_x, topleft_y, label, color=color)

plt.show()
```

# <a name="object-detection-with-yolo"></a>[Détection d’objets avec YOLO](#tab/object-detect-yolo)

Le code suivant crée des zones, des étiquettes et des scores. Utilisez les détails de la zone limitée pour effectuer les mêmes étapes de post-traitement que pour le modèle Faster R-CNN. 

```python
from yolo_onnx_preprocessing_utils import non_max_suppression, _convert_to_rcnn_output
import torch

result = non_max_suppression(
    torch.from_numpy(result),
    conf_thres=.1,
    iou_thres=.5)
label, image_shape = _convert_to_rcnn_output(result[0], height_onnx, width_onnx, pad)
boxes = np.array(label["boxes"])
labels = np.array(label["labels"])
labels = [label[0] for label in labels]
scores = np.array(label["scores"])
scores = [score[0] for score in scores]
boxes, labels, scores
```

### <a name="visualize-boxes"></a>Visualiser les zones

```python

import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
%matplotlib inline

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np = mpimg.imread(test_image_path)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size
print(img.size)

fig,ax = plt.subplots(1)
# Display the image
ax.imshow(img_np)

# Draw a box and label for each detection 
for detect in filtered_bounding_boxes:
    label = detect['label']
    box = detect['box']
    ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
    topleft_x, topleft_y = x * xmin, y * ymin
    width, height = x * (xmax - xmin), y * (ymax - ymin)
    print('{}: {}, {}, {}, {}'.format(detect['label'], topleft_x, topleft_y, width, height))
    rect = patches.Rectangle((topleft_x, topleft_y), width, height, 
                             linewidth=1, edgecolor='green',facecolor='none')

    ax.add_patch(rect)
    color = 'green'
    plt.text(topleft_x, topleft_y, label, color=color)

plt.show()
```


# <a name="instance-segmentation"></a>[Segmentation d’instance](#tab/instance-segmentation)

#### <a name="visualize-the-masks-and-bounding-boxes"></a>Visualiser les masques et les zones englobantes

```python
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.patches as patches
import cv2
%matplotlib inline

def display_detections(image, boxes, labels, scores, masks, resize_height, 
                       resize_width, classes, score_threshold=0.3):
    """visualize boxes and masks
    
    :param image: raw image
    :type image: PIL image
    :param boxes: box with shape (No. of instances, 4) 
    :type boxes: ndarray 
    :param labels: classes with shape (No. of instances,) 
    :type labels: ndarray
    :param scores: scores with shape (No. of instances,)
    :type scores: ndarray
    :param masks: masks with shape (No. of instances, 1, HEIGHT, WIDTH) 
    :type masks:  ndarray
    :param resize_height: resize height of an input image
    :type resize_height: Int
    :param resize_width: resize width of an input image
    :type resize_width: Int
    :param classes: classes with shape (No. of classes) 
    :type classes:  list
    :param score_threshold: threshold on scores in the range of 0-1
    :type score_threshold: float
    :return: None     
   
    """

    _, ax = plt.subplots(1, figsize=(12,9))

    image = np.array(image)
    original_height = image.shape[0]
    original_width = image.shape[1]

    for mask, box, label, score in zip(masks, boxes, labels, scores):        
        if score <= score_threshold:
            continue
        mask = mask[0, :, :, None]        
        # resize boxes to original raw input size
        box = [box[0]*original_width/resize_width, 
               box[1]*original_height/resize_height, 
               box[2]*original_width/resize_width, 
               box[3]*original_height/resize_height]
        
        mask = cv2.resize(mask, (image.shape[1],image.shape[0]), 0, 0, interpolation = cv2.INTER_NEAREST)
        # mask is a matrix with values in the range of [0,1]
        # higher values indicate the presence of an object and vice versa
        # select the threshold or cutoff value to get objects present               
        mask = mask > 0.5 
        image_masked = image.copy()
        image_masked[mask] = (0, 255, 255)
        alpha = .5 # alpha blending with range 0 to 1
        cv2.addWeighted(image_masked, alpha, image, 1 - alpha,0, image)        
        rect = patches.Rectangle((box[0], box[1]), box[2] - box[0], box[3] - box[1],\
                                 linewidth=1, edgecolor='b', facecolor='none')
        ax.annotate(classes[label] + ':' + str(np.round(score, 2)), (box[0], box[1]),\
                    color='w', fontsize=12)
        ax.add_patch(rect)
        

    ax.imshow(image)
    plt.show()

display_detections(img, boxes.copy(), labels, scores, masks.copy(), 
                   resize_height, resize_width, classes, score_threshold=.5)
```

---


## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les tâches de vision par ordinateur dans AutoML](how-to-auto-train-image-models.md)
* [Résoudre les expériences AutoML](how-to-troubleshoot-auto-ml.md)

