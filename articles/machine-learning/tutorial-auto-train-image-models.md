---
title: 'Tutoriel : AutoML - Entraîner un modèle de détection d’objet'
titleSuffix: Azure Machine Learning
description: Entraînez un modèle de détection d’objet pour prédire les tarifs des taxis de New York à l’aide du kit SDK Python Azure Machine Learning avec ML automatisé d’Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: swatig007
ms.author: swatig
ms.reviewer: nibaccam
ms.date: 10/06/2021
ms.custom: devx-track-python, automl
ms.openlocfilehash: e6906988b47ecdb1ba5e1ca7947666e6371ddd8e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468572"
---
# <a name="tutorial-train-an-object-detection-model-preview-with-automl-and-python"></a>Tutoriel : Entraîner un modèle de détection d’objet (préversion) avec AutoML et Python

>[!IMPORTANT]
> Les fonctionnalités présentées dans cet article sont en préversion. Elles doivent être considérées comme des fonctionnalités en préversion [expérimentales](/python/api/overview/azure/ml/#stable-vs-experimental) qui peuvent changer à tout moment.

Dans ce tutoriel, vous allez découvrir comment entraîner un modèle de détection d’objet à l’aide du ML automatisé d’Azure Machine Learning avec le kit SDK Python Azure Machine Learning. Ce modèle de détection d’objet détermine si l’image contient des objets, tels qu’une canette, un carton, une bouteille de lait ou une bouteille d’eau.

Le ML automatisé accepte des données d’entraînement et des paramètres de configuration, et itère automatiquement des combinaisons de différentes méthodes de normalisation/standardisation des fonctionnalités, de modèles et de paramètres hyperparamètres afin d’obtenir le meilleur modèle.


Dans ce tutoriel, vous allez écrire du code à l’aide du kit SDK Python et découvrir comment effectuer les tâches suivantes :

> [!div class="checklist"]
> * Télécharger et transformer des données
> * Entraîner un modèle de détection d’objet Machine Learning automatisé
> * Spécifier des valeurs d’hyperparamètres pour votre modèle
> * Effectuer un balayage hyperparamétrique
> * Déployer votre modèle
> * Visualiser les détections

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante](https://azure.microsoft.com/free/) d’Azure Machine Learning dès aujourd’hui.

* Python 3.6 ou 3.7 sont pris en charge pour cette fonctionnalité

* Suivez le guide [Démarrage rapide : Bien démarrer avec Azure Machine Learning](quickstart-create-resources.md#create-the-workspace) si vous ne disposez pas encore d’un espace de travail Azure Machine Learning.

* Téléchargez le fichier de données [**odFridgeObjects.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)

Vous trouverez également ce tutoriel sur [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) si vous souhaitez l’exécuter dans votre propre [environnement local](how-to-configure-environment.md#local). Pour vous procurer les packages requis :
* Exécutez `pip install azureml`
* [Installez le client `automl` complet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment).

## <a name="compute-target-setup"></a>Configuration de la cible de calcul

Vous devez d’abord configurer une cible de calcul à utiliser pour l’entraînement de votre modèle ML automatisé. Les modèles ML automatisé pour les tâches d’image requièrent des références (SKU) GPU.

Ce tutoriel utilise la série NCsv3 (avec des GPU V100), car ce type de cible de calcul s’appuie sur plusieurs GPU pour accélérer l’entraînement. En outre, vous pouvez configurer plusieurs nœuds afin de tirer parti du parallélisme lors du réglage des hyperparamètres pour votre modèle.

Le code suivant crée un calcul GPU de taille standard _NC24s_v3 avec quatre nœuds attachés à l’espace de travail, `ws`.

> [!WARNING]
> Vérifiez que votre abonnement dispose d’un quota suffisant pour la cible de calcul que vous souhaitez utiliser.

```python
from azureml.core.compute import AmlCompute, ComputeTarget

cluster_name = "gpu-cluster-nc24s_v3"

try:
    compute_target = ws.compute_targets[cluster_name]
    print('Found existing compute target.')
except KeyError:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='Standard_NC24s_v3',
                                                           idle_seconds_before_scaledown=1800,
                                                           min_nodes=0,
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

#If no min_node_count is provided, the scale settings are used for the cluster.
compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="experiment-setup"></a>Configuration de l’expérience
Ensuite, créez un `Experiment` dans votre espace de travail pour suivre vos exécutions d’entraînement de modèle.

```python

from azureml.core import Experiment

experiment_name = 'automl-image-object-detection'
experiment = Experiment(ws, name=experiment_name)
```

## <a name="visualize-input-data"></a>Visualiser les données d’entrée

Une fois les données d’image d’entrée préparées au format [JSONL](https://jsonlines.org/) (JSON Lines), vous pouvez visualiser les cadres englobants de vérité fondamentale pour une image. Pour cela, vérifiez que `matplotlib` est installé.

```
%pip install --upgrade matplotlib
```
```python

%matplotlib inline
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image as pil_image
import numpy as np
import json
import os

def plot_ground_truth_boxes(image_file, ground_truth_boxes):
    # Display the image
    plt.figure()
    img_np = mpimg.imread(image_file)
    img = pil_image.fromarray(img_np.astype("uint8"), "RGB")
    img_w, img_h = img.size

    fig,ax = plt.subplots(figsize=(12, 16))
    ax.imshow(img_np)
    ax.axis("off")

    label_to_color_mapping = {}

    for gt in ground_truth_boxes:
        label = gt["label"]

        xmin, ymin, xmax, ymax =  gt["topX"], gt["topY"], gt["bottomX"], gt["bottomY"]
        topleft_x, topleft_y = img_w * xmin, img_h * ymin
        width, height = img_w * (xmax - xmin), img_h * (ymax - ymin)

        if label in label_to_color_mapping:
            color = label_to_color_mapping[label]
        else:
            # Generate a random color. If you want to use a specific color, you can use something like "red".
            color = np.random.rand(3)
            label_to_color_mapping[label] = color

        # Display bounding box
        rect = patches.Rectangle((topleft_x, topleft_y), width, height,
                                 linewidth=2, edgecolor=color, facecolor="none")
        ax.add_patch(rect)

        # Display label
        ax.text(topleft_x, topleft_y - 10, label, color=color, fontsize=20)

    plt.show()

def plot_ground_truth_boxes_jsonl(image_file, jsonl_file):
    image_base_name = os.path.basename(image_file)
    ground_truth_data_found = False
    with open(jsonl_file) as fp:
        for line in fp.readlines():
            line_json = json.loads(line)
            filename = line_json["image_url"]
            if image_base_name in filename:
                ground_truth_data_found = True
                plot_ground_truth_boxes(image_file, line_json["label"])
                break
    if not ground_truth_data_found:
        print("Unable to find ground truth information for image: {}".format(image_file))

def plot_ground_truth_boxes_dataset(image_file, dataset_pd):
    image_base_name = os.path.basename(image_file)
    image_pd = dataset_pd[dataset_pd['portable_path'].str.contains(image_base_name)]
    if not image_pd.empty:
        ground_truth_boxes = image_pd.iloc[0]["label"]
        plot_ground_truth_boxes(image_file, ground_truth_boxes)
    else:
        print("Unable to find ground truth information for image: {}".format(image_file))
```

À l’aide des fonctions d’assistance ci-dessus, pour une image donnée, vous pouvez exécuter le code suivant pour afficher les cadres englobants.

```python
image_file = "./odFridgeObjects/images/31.jpg"
jsonl_file = "./odFridgeObjects/train_annotations.jsonl"

plot_ground_truth_boxes_jsonl(image_file, jsonl_file)
```

## <a name="upload-data-and-create-dataset"></a>Charger des données et créer un jeu de données

Pour pouvoir utiliser les données pour l’entraînement, chargez-les dans votre espace de travail via un magasin de données. Le magasin de données fournit un mécanisme vous permettant de charger ou de télécharger des données, et d’interagir avec elles à partir de vos cibles de calcul distantes.

```python
ds = ws.get_default_datastore()
ds.upload(src_dir='./odFridgeObjects', target_path='odFridgeObjects')
```

Une fois les données chargées dans le magasin de données, vous pouvez créer un jeu de données Azure Machine Learning à partir des données. Les jeux de données intègrent vos données dans un objet consommable à des fins d’entraînement.

Le code suivant crée un jeu de données pour l’entraînement. Étant donné qu’aucun jeu de données de validation n’est spécifié, par défaut 20 % de vos données d’entraînement sont utilisées pour la validation.

``` python
from azureml.core import Dataset
from azureml.data import DataType

training_dataset_name = 'odFridgeObjectsTrainingDataset'
if training_dataset_name in ws.datasets:
    training_dataset = ws.datasets.get(training_dataset_name)
    print('Found the training dataset', training_dataset_name)
else:
    # create training dataset
        # create training dataset
    training_dataset = Dataset.Tabular.from_json_lines_files(
        path=ds.path('odFridgeObjects/train_annotations.jsonl'),
        set_column_types={"image_url": DataType.to_stream(ds.workspace)},
    )
    training_dataset = training_dataset.register(workspace=ws, name=training_dataset_name)

print("Training dataset name: " + training_dataset.name)
```

### <a name="visualize-dataset"></a>Visualiser le jeu de données

Vous pouvez également visualiser les cadres englobants de vérité fondamentale pour une image à partir de ce jeu de données.

Chargez les données dans un dataframe pandas.

```python
import azureml.dataprep as dprep

from azureml.dataprep.api.functions import get_portable_path

# Get pandas dataframe from the dataset
dflow = training_dataset._dataflow.add_column(get_portable_path(dprep.col("image_url")),
                                              "portable_path", "image_url")
dataset_pd = dflow.to_pandas_dataframe(extended_types=True)
```

Pour toute image donnée, vous pouvez exécuter le code suivant pour afficher les cadres englobants.

```python
image_file = "./odFridgeObjects/images/31.jpg"
plot_ground_truth_boxes_dataset(image_file, dataset_pd)
```

## <a name="configure-your-object-detection-experiment"></a>Configurer votre expérience de détection d’objet

Pour configurer des exécutions de ML automatisé pour les tâches associées à une image, utilisez l’objet `AutoMLImageConfig`. Dans votre `AutoMLImageConfig`, vous pouvez spécifier les algorithmes de modèle avec le paramètre `model_name`, et configurer les paramètres de façon à effectuer un balayage hyperparamétrique sur un espace de paramètres défini afin de trouver le modèle optimal.

Dans cet exemple, nous utilisons `AutoMLImageConfig` pour entraîner un modèle de détection d’objet avec `yolov5` et `fasterrcnn_resnet50_fpn`, tous deux étant pré-entraînés sur COCO, un jeu de données de détection d’objet, de segmentation et d’étiquetage à grande échelle qui contient des milliers d’images étiquetées avec plus de 80 catégories d’étiquettes.

### <a name="hyperparameter-sweeping-for-image-tasks"></a>Balayage hyperparamétrique pour les tâches d’image

Vous pouvez effectuer un balayage hyperparamétrique sur un espace de paramètres défini pour trouver le modèle optimal.

Le code suivant définit l’espace de paramètres en vue du balayage hyperparamétrique pour chaque algorithme défini, `yolov5` et `fasterrcnn_resnet50_fpn`.  Dans l’espace de paramètres, spécifiez la plage de valeurs pour `learning_rate`, `optimizer`, `lr_scheduler`, etc., parmi laquelle AutoML devra choisir lorsqu’il tentera de générer un modèle avec la métrique principale optimale. Si les valeurs d’hyperparamètres ne sont pas spécifiées, les valeurs par défaut sont utilisées pour chaque algorithme.

Pour les paramètres de réglage, utilisez l’échantillonnage aléatoire afin de choisir des échantillons à partir de cet espace de paramètres en important les classes `GridParameterSampling, RandomParameterSampling` et `BayesianParameterSampling`.  Cela indique au ML automatisé qu’il doit d’essayer en tout 20 itérations avec ces différents échantillons, en exécutant quatre itérations à la fois sur notre cible de calcul, qui a été configurée à l’aide de quatre nœuds. Plus l’espace contient de paramètres, plus il faut d’itérations pour trouver des modèles optimaux.

La stratégie d’arrêt anticipé Bandit est également utilisée. Cette stratégie met fin aux configurations dont les performances sont médiocres, autrement dit celles qui ne sont pas dans une fourchette de 20 % par rapport à la configuration la plus performante, ce qui permet d’économiser de manière significative les ressources de calcul.

```python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import BanditPolicy, HyperDriveConfig
from azureml.train.hyperdrive import choice, uniform

parameter_space = {
    'model': choice(
        {
            'model_name': choice('yolov5'),
            'learning_rate': uniform(0.0001, 0.01),
            #'model_size': choice('small', 'medium'), # model-specific
            'img_size': choice(640, 704, 768), # model-specific
        },
        {
            'model_name': choice('fasterrcnn_resnet50_fpn'),
            'learning_rate': uniform(0.0001, 0.001),
            #'warmup_cosine_lr_warmup_epochs': choice(0, 3),
            'optimizer': choice('sgd', 'adam', 'adamw'),
            'min_size': choice(600, 800), # model-specific
        }
    )
}

tuning_settings = {
    'iterations': 20,
    'max_concurrent_iterations': 4,
    'hyperparameter_sampling': RandomParameterSampling(parameter_space),
    'policy': BanditPolicy(evaluation_interval=2, slack_factor=0.2, delay_evaluation=6)
}
```

Une fois l’espace de paramètres et les paramètres de réglage définis, vous pouvez les transmettre à votre objet `AutoMLImageConfig`, puis soumettre l’expérience pour entraîner un modèle d’image à l’aide de votre jeu de données d’entraînement.

```python
from azureml.train.automl import AutoMLImageConfig
automl_image_config = AutoMLImageConfig(task='image-object-detection',
                                        compute_target=compute_target,
                                        training_data=training_dataset,
                                        validation_data=validation_dataset,
                                        primary_metric='mean_average_precision',
                                        **tuning_settings)

automl_image_run = experiment.submit(automl_image_config)
automl_image_run.wait_for_completion(wait_post_processing=True)
```

Lorsque vous procédez à un balayage hyperparamétrique, il peut être utile de visualiser les différentes configurations qui ont été tentées à l’aide de l’interface utilisateur HyperDrive. Vous pouvez accéder à cette interface utilisateur en accédant à l’onglet « Exécutions enfants » dans l’interface utilisateur de l’automl_image_rune principale ci-dessus, qui est l’exécution parente HyperDrive. Vous pouvez ensuite accéder à son onglet « Exécutions enfants ». En guise d’alternative, vous pouvez voir ci-dessous l’exécution parente HyperDrive et accéder à son onglet « Exécutions enfants » :

```python
from azureml.core import Run
hyperdrive_run = Run(experiment=experiment, run_id=automl_image_run.id + '_HD')
hyperdrive_run
```

## <a name="register-the-best-model"></a>Enregistrer le meilleur modèle

Une fois l’exécution terminée, nous pouvons inscrire le modèle qui a été créé à partir de la meilleure exécution.

```python
best_child_run = automl_image_run.get_best_child()
model_name = best_child_run.properties['model_name']
model = best_child_run.register_model(model_name = model_name, model_path='outputs/model.pt')
```

## <a name="deploy-model-as-a-web-service"></a>Déployer le modèle en tant que service web

Une fois que vous avez votre modèle entraîné, vous pouvez le déployer sur Azure. Vous pouvez déployer votre modèle entraîné en tant que service web sur ACI (Azure Container Instances) ou AKS (Azure Kubernetes Service). ACI est l’option idéale pour tester les déploiements, tandis qu’AKS est mieux adapté à une utilisation en production à grande échelle.

Dans ce tutoriel, nous déployons le modèle en tant que service web dans AKS.

1. Créez un cluster de calcul AKS. Dans cet exemple, une référence SKU de machine virtuelle GPU est utilisée pour le cluster de déploiement.

    ```python
    from azureml.core.compute import ComputeTarget, AksCompute
    from azureml.exceptions import ComputeTargetException

    # Choose a name for your cluster
    aks_name = "cluster-aks-gpu"

    # Check to see if the cluster already exists
    try:
        aks_target = ComputeTarget(workspace=ws, name=aks_name)
        print('Found existing compute target')
    except ComputeTargetException:
        print('Creating a new compute target...')
        # Provision AKS cluster with GPU machine
        prov_config = AksCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                            location="eastus2")
        # Create the cluster
        aks_target = ComputeTarget.create(workspace=ws,
                                          name=aks_name,
                                          provisioning_configuration=prov_config)
        aks_target.wait_for_completion(show_output=True)
    ```

1. Définissez une configuration d’inférence qui décrit comment configurer le service web contenant votre modèle. Vous pouvez utiliser le script de scoring et l’environnement de l’exécution d’entraînement dans votre configuration d’inférence.

    > [!NOTE]
    > Pour modifier les paramètres du modèle, ouvrez le script de scoring téléchargé et modifiez la variable model_settings avant de déployer le modèle.

    ```python
    from azureml.core.model import InferenceConfig

    best_child_run.download_file('outputs/scoring_file_v_1_0_0.py', output_file_path='score.py')
    environment = best_child_run.get_environment()
    inference_config = InferenceConfig(entry_script='score.py', environment=environment)
    ```

1. Vous pouvez ensuite déployer le modèle en tant que service web AKS.

    ```python

    from azureml.core.webservice import AksWebservice
    from azureml.core.webservice import Webservice
    from azureml.core.model import Model
    from azureml.core.environment import Environment

    aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,
                                                    cpu_cores=1,
                                                    memory_gb=50,
                                                    enable_app_insights=True)

    aks_service = Model.deploy(ws,
                               models=[model],
                               inference_config=inference_config,
                               deployment_config=aks_config,
                               deployment_target=aks_target,
                               name='automl-image-test',
                               overwrite=True)
    aks_service.wait_for_deployment(show_output=True)
    print(aks_service.state)
    ```

## <a name="test-the-web-service"></a>Test du service web

Vous pouvez tester le service web déployé pour prédire de nouvelles images. Pour ce tutoriel, transmettez une image aléatoire du jeu de données et transmettez-la à l’URI de scoring.

```python
import requests

# URL for the web service
scoring_uri = aks_service.scoring_uri

# If the service is authenticated, set the key or token
key, _ = aks_service.get_keys()

sample_image = './test_image.jpg'

# Load image data
data = open(sample_image, 'rb').read()

# Set the content type
headers = {'Content-Type': 'application/octet-stream'}

# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, data, headers=headers)
print(resp.text)
```
## <a name="visualize-detections"></a>Visualiser les détections
Maintenant que vous avez attribué un score à une image de test, vous pouvez visualiser les cadres englobants pour cette image. Pour ce faire, vérifiez que matplotlib est installé.

```
%pip install --upgrade matplotlib
```

```python
%matplotlib inline
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
import numpy as np
import json

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np=mpimg.imread(sample_image)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size

fig,ax = plt.subplots(1, figsize=(15,15))
# Display the image
ax.imshow(img_np)

# draw box and label for each detection
detections = json.loads(resp.text)
for detect in detections['boxes']:
    label = detect['label']
    box = detect['box']
    conf_score = detect['score']
    if conf_score > 0.6:
        ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
        topleft_x, topleft_y = x * xmin, y * ymin
        width, height = x * (xmax - xmin), y * (ymax - ymin)
        print('{}: [{}, {}, {}, {}], {}'.format(detect['label'], round(topleft_x, 3),
                                                round(topleft_y, 3), round(width, 3),
                                                round(height, 3), round(conf_score, 3)))

        color = np.random.rand(3) #'red'
        rect = patches.Rectangle((topleft_x, topleft_y), width, height,
                                 linewidth=3, edgecolor=color,facecolor='none')

        ax.add_patch(rect)
        plt.text(topleft_x, topleft_y - 10, label, color=color, fontsize=20)

plt.show()
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Sautez cette section si vous prévoyez d’exécuter d’autres tutoriels Azure Machine Learning.

Si vous n’avez pas l’intention d’utiliser les ressources que vous avez créées, supprimez-les pour éviter des frais.

1. Dans le portail Azure, sélectionnez **Groupes de ressources** tout à gauche.
1. À partir de la liste, sélectionnez le groupe de ressources créé.
1. Sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources. Puis sélectionnez **Supprimer**.

Vous pouvez également conserver le groupe de ressources mais supprimer un espace de travail unique. Affichez les propriétés de l’espace de travail, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel sur le machine learning automatisé, vous avez effectué les tâches suivantes :

> [!div class="checklist"]
> * Configuration d’un espace de travail et préparation des données pour une expérience
> * Entraînement d’un modèle de détection d’objet automatisé
> * Spécification des valeurs d’hyperparamètres pour votre modèle
> * Balayage hyperparamétrique
> * Déploiement de votre modèle
> * Visualisation des détections

* [Apprenez-en davantage sur la vision par ordinateur en ML automatisé (préversion)](concept-automated-ml.md#computer-vision-preview).
* [Découvrez comment configurer AutoML pour entraîner des modèles de vision par ordinateur avec Python (préversion)](how-to-auto-train-image-models.md).
* Passez en revue les exemples de code détaillé et les cas d’usage disponibles dans le [dépôt GitHub d’exemples de blocs-notes pour le Machine Learning automatisé](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml). Vérifiez les dossiers avec le préfixe « image- » pour obtenir des exemples propres à la création de modèles de vision par ordinateur.

> [!NOTE]
> L’utilisation du jeu de données des objets réfrigérants est disponible sous la [licence MIT](https://github.com/microsoft/computervision-recipes/blob/master/LICENSE).
