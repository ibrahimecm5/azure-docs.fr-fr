---
title: Environnements organisés
titleSuffix: Azure Machine Learning
description: En savoir plus sur les environnements organisés Azure Machine Learning, un ensemble d’environnements préconfigurés qui permettent de réduire les temps de préparation du déploiement et des expérimentations.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 10/21/2021
ms.openlocfilehash: 432b6ad140c194e757ab34822f13fa607019b87d
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577305"
---
# <a name="azure-machine-learning-curated-environments"></a>Environnements organisés Azure Machine Learning

Cet article dresse la liste des environnements organisés dans Azure Machine Learning avec les dernières versions de framework. Les environnements organisés sont fournis par Azure Machine Learning et sont disponibles dans votre espace de travail par défaut. Ils sont sauvegardés par des images Docker mises en cache, qui utilisent la version la plus récente du kit SDK Azure Machine Learning, ce qui réduit le coût de préparation de l’exécution et permet un déploiement plus rapide. Utilisez ces environnements pour prendre rapidement en main les différents infrastructures d’apprentissage automatique.

> [!NOTE]
> Utilisez le [SDK Python](how-to-use-environments.md), la [CLI](/cli/azure/ml/environment?view=azure-cli-latest&preserve-view=true#az_ml_environment_list) ou Azure Machine Learning [Studio](how-to-manage-environments-in-studio.md) pour obtenir la liste complète des environnements et de leurs dépendances. Pour plus d’informations, consultez l’[article sur les environnements](how-to-use-environments.md#use-a-curated-environment). 

## <a name="training-curated-environments"></a>Formation d’environnements organisés

### <a name="pytorch"></a>PyTorch

**Nom** : AzureML-pytorch-1.10-ubuntu18.04-py38-cuda11-gpu (**préversion**)  
**Description** : Environnement de Deep Learning avec PyTorch contenant le kit SDK Python AzureML et des packages Python supplémentaires.  

Le Dockerfile suivant peut être personnalisé en fonction de vos workflows personnels.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.1-cudnn8-ubuntu18.04:20211029.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/pytorch-1.10

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.8 \
    pip=20.2.4 \
    pytorch=1.10.0 \
    torchvision=0.11.1 \
    torchaudio=0.10.0 \
    cudatoolkit=11.1.1 \
    nvidia-apex=0.1.0 \
    gxx_linux-64 \
    -c anaconda -c pytorch -c conda-forge

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.63' \
                'pandas>=1.3,<1.4' \
                'scipy>=1.5,<1.8' \
                'numpy>=1.10,<1.22' \
                'ipykernel~=6.0' \
                'azureml-core==1.35.0.post1' \
                'azureml-defaults==1.35.0' \
                'azureml-mlflow==1.35.0' \
                'azureml-telemetry==1.35.0' \
                'tensorboard==2.6.0' \
                'tensorflow-gpu==2.6.0' \
                'onnxruntime-gpu>=1.7,<1.10' \
                'horovod==0.23' \
                'future==0.18.2' \
                'torch-tb-profiler==0.3.1'


# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

Autres environnements PyTorch disponibles :
* AzureML-pytorch-1.9-ubuntu18.04-py37-cuda11-gpu  
* AzureML-pytorch-1.8-ubuntu18.04-py37-cuda11-gpu
* AzureML-pytorch-1.7-ubuntu18.04-py37-cuda11-gpu

### <a name="lightgbm"></a>LightGBM

**Nom** : AzureML-lightgbm-3.2-ubuntu18.04-py37-cpu  
**Description** : Environnement de Machine Learning avec Scikit-learn, LightGBM, XGBoost, Dask contenant le kit SDK Python AzureML et des packages supplémentaires.  

Le Dockerfile suivant peut être personnalisé en fonction de vos workflows personnels.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20211029.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/lightgbm

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_TENSORFLOW=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'numpy>=1.10,<1.20' \
                'scipy~=1.5.0' \
                'scikit-learn~=0.24.1' \
                'xgboost~=1.4.0' \
                'lightgbm~=3.2.0' \
                'dask~=2021.6.0' \
                'distributed~=2021.6.0' \
                'dask-ml~=1.9.0' \
                'adlfs~=0.7.0' \
                'ipykernel~=6.0' \
                'azureml-core==1.35.0.post1' \
                'azureml-defaults==1.35.0' \
                'azureml-mlflow==1.35.0' \
                'azureml-telemetry==1.35.0'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

### <a name="sklearn"></a>SKLearn
**Nom** : AzureML-sklearn-0.24-ubuntu18.04-py37-cuda11-gpu  
**Description** : Environnement pour des tâches telles que la régression, le clustering et la classification avec Scikit-learn. Contient le kit SDK Python AzureML et des packages Python supplémentaires.  

Le Dockerfile suivant peut être personnalisé en fonction de vos workflows personnels.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20211029.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/sklearn-0.24.1

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'ipykernel~=6.0' \
                'azureml-core==1.35.0.post1' \
                'azureml-defaults==1.35.0' \
                'azureml-mlflow==1.35.0' \
                'azureml-telemetry==1.35.0' \
                'scikit-learn==0.24.1'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

### <a name="tensorflow"></a>TensorFlow

**Nom** : AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11-gpu  
**Description** : Environnement de Deep Learning avec Tensorflow contenant le kit SDK Python AzureML et des packages Python supplémentaires.  

Le Dockerfile suivant peut être personnalisé en fonction de vos workflows personnels.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20211029.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/tensorflow-2.4

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_TENSORFLOW=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'ipykernel~=6.0' \
                'azureml-core==1.35.0.post1' \
                'azureml-defaults==1.35.0' \
                'azureml-mlflow==1.35.0' \
                'azureml-telemetry==1.35.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
                'tensorflow-datasets==4.3.0' \
                'onnxruntime-gpu>=1.7,<1.8' \
                'horovod[tensorflow-gpu]==0.21.3'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="automated-ml-automl"></a>ML automatisé (AutoML)

Les workflows de formation du pipeline Azure ML qui utilisent AutoML sélectionnent automatiquement un environnement organisé en fonction du type de calcul et de l’activation de DNN. AutoML fournit les environnements organisés suivants :

| Nom | Type de calcul | DNN activé |
| --- | --- | --- |
|AzureML-AutoML | UC | Non |
|AzureML-AutoML-DNN | UC | Oui |
| AzureML-AutoML-GPU | GPU | No |
| AzureML-AutoML-DNN-GPU | GPU | Yes |

Pour plus d’informations sur AutoML et sur les pipelines Azure ML, consultez [Utiliser le ML automatisé dans un pipeline Azure Machine Learning dans Python](how-to-use-automlstep-in-pipelines.md).

## <a name="inference-curated-environments-and-prebuilt-docker-images"></a>Environnements organisés d’inférence et images Docker prédéfinies

[!INCLUDE [list-of-inference-prebuilt-docker-images](../../includes/aml-inference-list-prebuilt-docker-images.md)]

## <a name="security"></a>Sécurité
Des mises à jour de version pour les environnements pris en charge sont publiées toutes les deux semaines pour répondre aux vulnérabilités de moins de 30 jours. 

