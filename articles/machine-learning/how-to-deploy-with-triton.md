---
title: Modèle de hautes performances avec Triton (préversion)
titleSuffix: Azure Machine Learning
description: Apprenez à déployer votre modèle à l’aide du logiciel NVIDIA Triton Inference Server dans Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 11/03/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.author: ssambare
author: shivanissambare
ms.custom: deploy, devplatv2
ms.openlocfilehash: c62a446aace060bf55606462b04b323cb44bf2cb
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553841"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Haute performance avec un serveur Triton Inference (préversion) 

Découvrez comment utiliser le [serveur NVIDIA Triton Inference](https://aka.ms/nvidia-triton-docs) dans Azure Machine Learning avec les [points de terminaison en ligne gérés](concept-endpoints.md#managed-online-endpoints).

Triton est un logiciel open source multi-infrastructures qui est optimisé pour l’inférence. Il prend en charge les infrastructures de Machine Learning populaires comme TensorFlow, ONNX Runtime, PyTorch, NVIDIA TensorRT, etc. Il peut être utilisé pour vos charges de travail de processeur ou GPU.

Dans cet article, vous allez découvrir comment déployer Triton et un modèle sur un point de terminaison en ligne géré. Vous trouverez des informations sur l’utilisation de l’interface CLI (ligne de commande) et d’Azure Machine Learning studio.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> Le [serveur d’inférence NVIDIA Triton](https://aka.ms/nvidia-triton-docs) est un logiciel tiers Open source intégré à Azure Machine Learning.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [basic prereqs](../../includes/machine-learning-cli-prereqs.md)]

* Environnement Python 3.8 (ou supérieur) opérationnel.

* Accès aux machines virtuelles de la série NCv3 pour votre abonnement Azure.

    > [!IMPORTANT]
    > Vous devez peut-être demander une augmentation du quota pour votre abonnement pour pouvoir utiliser cette série de machines virtuelles. Pour plus d’informations, consultez [Série NCv3](/azure/virtual-machines/ncv3-series).

[!INCLUDE [clone repo & set defaults](../../includes/machine-learning-cli-prepare.md)]

Le serveur NVIDIA Triton Inference nécessite une structure de référentiel de modèles spécifique, avec un répertoire pour chaque modèle et des sous-répertoires pour la version de modèle. Le contenu de chaque sous-répertoire de version de modèle est déterminé par le type du modèle et les spécifications du serveur back-end sur lequel s’appuie le modèle. Pour afficher l’ensemble de la structure du référentiel de modèles [https://github.com/triton-inference-server/server/blob/main/docs/model_repository.md#model-files](https://github.com/triton-inference-server/server/blob/main/docs/model_repository.md#model-files)

Les informations contenues dans ce document sont basées sur l’utilisation d’un modèle stocké au format ONNX, de sorte que la structure de répertoires du référentiel de modèles est `<model-repository>/<model-name>/1/model.onnx`. Plus précisément, ce modèle effectue l’identification de l’image.

## <a name="deploy-using-cli-v2"></a>Déployer à l’aide de l’interface CLI (v2)

Cette section explique comment déployer Triton sur un point de terminaison en ligne géré à l’aide de l’interface Azure CLI avec l’extension Machine Learning (v2).

1. Pour éviter de taper un chemin pour plusieurs commandes, utilisez la commande suivante afin de définir une variable d’environnement `BASE_PATH`. Cette variable pointe vers le répertoire où se trouvent les fichiers de modèle et de configuration YAML associés :

    ```azurecli
    BASE_PATH=endpoints/online/triton/single-model
    ```

1. Utilisez la commande suivante pour définir le nom du point de terminaison qui va être créé. Dans cet exemple, un nom aléatoire est créé pour le point de terminaison :

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="set_endpoint_name":::

1. Installez les spécifications Python à l’aide des commandes suivantes :

    ```azurecli
    pip install numpy
    pip install tritonclient[http]
    pip install pillow
    pip install gevent
    ```

1. Créez un fichier de configuration YAML pour votre point de terminaison. L’exemple suivant configure le nom et le mode d’authentification du point de terminaison. Le point de terminaison utilisé dans les commandes suivantes se trouve à l’emplacement `/cli/endpoints/online/triton/single-model/create-managed-endpoint.yml` dans le référentiel azureml-examples que vous avez cloné auparavant :

    __create-managed-endpoint.yaml__

    :::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/triton/single-model/create-managed-endpoint.yaml":::

1. Pour créer un point de terminaison à l’aide de la configuration YAML, utilisez la commande suivante :

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="create_endpoint":::

1. Créez un fichier de configuration YAML pour le déploiement. L’exemple suivant configure un déploiement du modèle nommé __blue__ sur le point de terminaison créé à l’étape précédente. Le point de terminaison utilisé dans les commandes suivantes se trouve à l’emplacement `/cli/endpoints/online/triton/single-model/create-managed-deployment.yml` dans le référentiel azureml-examples que vous avez cloné auparavant :

    > [!IMPORTANT]
    > Pour que le déploiement MLflow sans code (NCD) fonctionne, **`model_format`** doit être défini sur **`Triton`** . Pour plus d’informations, consultez le [schéma YAML du modèle CLI (v2)](reference-yaml-model.md).
    >
    > Ce déploiement utilise une machine virtuelle Standard_NC6s_v3. Vous devrez peut-être demander une augmentation du quota pour votre abonnement pour utiliser cette machine virtuelle. Pour plus d’informations, consultez [Série NCv3](/azure/virtual-machines/ncv3-series).

    :::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/triton/single-model/create-managed-deployment.yaml":::

1. Pour créer le déploiement à l’aide de la configuration YAML, utilisez la commande suivante :

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="create_deployment":::

### <a name="invoke-your-endpoint"></a>Appeler votre point de terminaison

Une fois votre déploiement terminé, utilisez la commande suivante pour effectuer une requête de scoring sur le point de terminaison déployé. 

> [!TIP]
> Le fichier `/cli/endpoints/online/triton/single-model/triton_densenet_scoring.py` dans le référentiel azureml-examples est utilisé pour le scoring. L’image transmise au point de terminaison doit être traitée en amont pour respecter les exigences de taille, de type et de format, et en aval pour afficher l’étiquette prédite. `triton_densenet_scoring.py` utilise la bibliothèque `tritonclient.http` pour communiquer avec le serveur Triton Inference.

1. Pour récupérer l’URI de scoring du point de terminaison, utilisez la commande suivante :

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="get_scoring_uri":::

1. Pour récupérer un jeton d’authentification, utilisez la commande suivante :

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="get_token":::

1. Pour effectuer le scoring des données avec le point de terminaison, utilisez la commande suivante : Cette opération envoie l’image d’un paon (https://aka.ms/peacock-pic) au point de terminaison :

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="check_scoring_of_model":::

    La réponse du script ressemble au texte suivant :

    ```
    Is server ready - True
    Is model ready - True
    /azureml-examples/cli/endpoints/online/triton/single-model/densenet_labels.txt
    84 : PEACOCK
    ```

### <a name="delete-your-endpoint-and-model"></a>Supprimer votre point de terminaison et votre modèle

Une fois que vous avez terminé d’utiliser le point de terminaison, utilisez la commande suivante pour le supprimer :

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="delete_endpoint":::

Utilisez la commande suivante pour supprimer votre modèle :

```azurecli
az ml model delete --name $MODEL_NAME --version $MODEL_VERSION
```

## <a name="deploy-using-azure-machine-learning-studio"></a>Effectuer un déploiement à l’aide d’Azure Machine Learning studio

Cette section explique comment déployer Triton sur un point de terminaison en ligne géré à l’aide [d’Azure Machine Learning studio](https://ml.azure.com).

1. Enregistrez votre modèle au format Triton à l’aide de la commande YAML et CLI suivante. YAML utilise un modèle densenet-onnx à partir de [https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/triton/single-model](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/triton/single-model)

    __create-triton-model.yaml__

    ```yml
    name: densenet-onnx-model
    version: 1
    local_path: ./models
    model_format: Triton
    description: Registering my Triton format model.
    ```

    ```azurecli
    az ml model create -f create-triton-model.yaml
    ```

    La capture d’écran suivante montre comment votre modèle inscrit s’affiche sur la __page Modèles__ d’Azure Machine Learning studio.

    :::image type="content" source="media/how-to-deploy-with-triton/triton-model-format.png" lightbox="media/how-to-deploy-with-triton/triton-model-format.png" alt-text="Capture d’écran montrant le format du modèle Triton sur la page Modèles.":::


1. Dans [Studio](https://ml.azure.com), sélectionnez votre espace de travail, puis utilisez la page __Points de terminaison__ ou __Modèles__ pour créer le déploiement du point de terminaison :

    # <a name="endpoints-page"></a>[Page des points de terminaison](#tab/endpoint)

    1. Dans la page __Points de terminaison__, sélectionnez **+ Créer (préversion)** .

        :::image type="content" source="media/how-to-deploy-with-triton/create-option-from-endpoints-page.png" lightbox="media/how-to-deploy-with-triton/create-option-from-endpoints-page.png" alt-text="Capture d’écran montrant l’option Créer dans la page de l’interface utilisateur des points de terminaison.":::

    1. Fournissez un nom et un type d’authentification pour le point de terminaison, puis sélectionnez __Suivant__.
    1. Quand vous sélectionnez un modèle, sélectionnez le modèle Triton inscrit précédemment. Sélectionnez __Suivant__ pour continuer.

    1. Quand vous sélectionnez un modèle enregistré au format Triton, dans l’étape Environnement de l’Assistant, vous n’avez pas besoin d’un script de scoring ni d’un environnement.

        :::image type="content" source="media/how-to-deploy-with-triton/ncd-triton.png" lightbox="media/how-to-deploy-with-triton/ncd-triton.png" alt-text="Capture d’écran montrant qu’aucun code ni environnement n’est requis pour les modèles Triton":::

    1. Exécutez l’Assistant pour déployer le modèle sur le point de terminaison.

        :::image type="content" source="media/how-to-deploy-with-triton/review-screen-triton.png" lightbox="media/how-to-deploy-with-triton/review-screen-triton.png" alt-text="Capture d’écran montrant l’écran de révision du déploiement sans code":::

    # <a name="models-page"></a>[Page Modèles](#tab/models)

    1. Sélectionnez le modèle Triton, puis __Déployer__. Quand vous y êtes invité, sélectionnez __Déployer sur le point de terminaison en temps réel (préversion)__ .

        :::image type="content" source="media/how-to-deploy-with-triton/deploy-from-models-page.png" lightbox="media/how-to-deploy-with-triton/deploy-from-models-page.png" alt-text="Capture d’écran montrant comment déployer un modèle à partir de l’interface utilisateur des modèles":::

    1. Exécutez l’Assistant pour déployer le modèle sur le point de terminaison.

    ---

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les articles suivants :

- [Déployer des modèles avec REST (préversion)](how-to-deploy-with-rest.md)
- [Créer et utiliser des points de terminaison en ligne managés (préversion) dans le studio](how-to-use-managed-online-endpoint-studio.md)
- [Déploiement sûr pour les points de terminaison en ligne (préversion)](how-to-safely-rollout-managed-endpoints.md)
- [Comment mettre à l’échelle automatiquement des points de terminaison en ligne gérés](how-to-autoscale-endpoints.md)
- [Afficher les coûts d’un point de terminaison en ligne managé Azure Machine Learning (préversion)](how-to-view-online-endpoints-costs.md)
- [Accéder aux ressources Azure avec un point de terminaison en ligne géré et une identité managée (préversion)](how-to-access-resources-from-endpoints-managed-identities.md)
- [Résolution des problèmes de déploiement de points de terminaison en ligne managés](how-to-troubleshoot-managed-online-endpoints.md)