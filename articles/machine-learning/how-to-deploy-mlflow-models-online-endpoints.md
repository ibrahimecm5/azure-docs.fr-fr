---
title: Déployer des modèles MLflow sur un point de terminaison managé en ligne (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment déployer votre modèle MLflow en tant que service web automatiquement managé par Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 11/03/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, mlflow, devplatv2, no-code-deployment
ms.openlocfilehash: a2328490185032e805a95f85b430d6742c76435c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289885"
---
# <a name="deploy-mlflow-models-to-managed-online-endpoint-preview"></a>Déployer des modèles MLflow sur un point de terminaison managé en ligne (préversion)

Dans cet article, découvrez comment déployer votre modèle [MLflow](https://www.mlflow.org) sur un [point de terminaison en ligne géré](concept-endpoints.md#managed-online-endpoints) (préversion). Lorsque vous déployez votre modèle MLflow sur un point de terminaison en ligne géré, il s’agit d’un déploiement sans code. Il ne nécessite pas de script de scoring et d’environnement. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [basic cli prereqs](../../includes/machine-learning-cli-prereqs.md)]

* Vous devez disposer d’un modèle MLflow. Les exemples de cet article sont basés sur les modèles de [https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/mlflow](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/mlflow).

[!INCLUDE [clone repo & set defaults](../../includes/machine-learning-cli-prepare.md)]

Dans l’extrait de code utilisé dans cet article, la variable d’environnement `ENDPOINT_NAME` contient le nom du point de terminaison à créer et à utiliser. Pour ce faire, utilisez la commande suivante à partir de l’interface CLI. Remplacez `<YOUR_ENDPOINT_NAME>` par le nom du fichier de votre point de terminaison :

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-mlflow.sh" ID="set_endpoint_name":::

## <a name="deploy-using-cli-v2"></a>Déployer à l’aide de l’interface CLI (v2)

Cet exemple montre comment vous pouvez déployer un modèle MLflow sur un point de terminaison géré en ligne à l’aide de l’interface CLI (v2).

> [!IMPORTANT]
> Pour le déploiement sans code MLflow, les **[tests via des points de terminaison locaux](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints)** ne sont pas pris en charge actuellement.

1. Créez un fichier de configuration YAML pour votre point de terminaison. L’exemple suivant configure le nom et le mode d’authentification du point de terminaison :

    __create-endpoint.yaml__

    :::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/mlflow/create-endpoint.yaml":::

1. Pour créer un point de terminaison à l’aide de la configuration YAML, utilisez la commande suivante :

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-mlflow.sh" ID="create_endpoint":::

1. Créez un fichier de configuration YAML pour le déploiement. L’exemple suivant configure un déploiement du modèle `sklearn-diabetes` sur le point de terminaison créé à l’étape précédente :

    > [!IMPORTANT]
    > Pour que le déploiement MLflow sans code (NCD) fonctionne, la définition de **`model_format`** sur **`mlflow`** est obligatoire. Pour plus d’informations, consultez le [schéma YAML du modèle CLI (v2)](reference-yaml-model.md).

    __sklearn-deployment.yaml__

    :::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/mlflow/sklearn-deployment.yaml":::

1. Pour créer le déploiement à l’aide de la configuration YAML, utilisez la commande suivante :

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-mlflow.sh" ID="create_sklearn_deployment":::

### <a name="invoke-the-endpoint"></a>Appeler le point de terminaison

Une fois votre déploiement terminé, utilisez la commande suivante pour effectuer une demande de scoring sur le point de terminaison déployé. Le fichier [sample-request-sklearn.json](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/mlflow/sample-request-sklearn.json) utilisé dans cette commande se trouve dans le répertoire `/cli/endpoints/online/mlflow` du référentiel azure-examples :

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-mlflow.sh" ID="test_sklearn_deployment":::

**sample-request-sklearn.json**

:::code language="json" source="~/azureml-examples-main/cli/endpoints/online/mlflow/sample-request-sklearn.json":::

La réponse ressemblera au texte suivant :

```json
[ 
  11633.100167144921,
  8522.117402884991
]
```

### <a name="delete-endpoint"></a>Supprimer le point de terminaison

Une fois que vous avez terminé avec le point de terminaison, utilisez la commande suivante pour le supprimer :

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-mlflow.sh" ID="delete_endpoint":::

## <a name="deploy-using-azure-machine-learning-studio"></a>Déployer avec Azure Machine Learning Studio

Cet exemple montre comment vous pouvez déployer un modèle MLflow sur un point de terminaison géré en ligne à l’aide d’[Azure Machine Learning Studio](https://ml.azure.com).

1. Enregistrez votre modèle au format MLflow à l’aide de la commande YAML et CLI suivante. YAML utilise un modèle MLflow scikit-learn à partir de [https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/mlflow](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/mlflow).

    __sample-create-mlflow-model.yaml__

    ```yaml
    $schema: https://azuremlschemas.azureedge.net/latest/model.schema.json
    name: sklearn-diabetes-mlflow
    version: 1
    local_path: sklearn-diabetes/model
    model_format: mlflow
    description: Scikit-learn MLflow model.
    ```


    ```azurecli
    az ml model create -f sample-create-mlflow-model.yaml
    ```

2. Dans [Studio](https://ml.azure.com), sélectionnez votre espace de travail, puis utilisez la page __Points de terminaison__ ou __Modèles__ pour créer le déploiement du point de terminaison :

    # <a name="endpoints-page"></a>[Page des points de terminaison](#tab/endpoint)

    1. Dans la page __Points de terminaison__, sélectionnez **+ Créer (préversion)** .

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/create-from-endpoints.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/create-from-endpoints.png" alt-text="Capture d’écran montrant l’option Créer dans la page de l’interface utilisateur des points de terminaison.":::

    1. Fournissez un nom et un type d’authentification pour le point de terminaison, puis sélectionnez __Suivant__.
    1. Lorsque vous sélectionnez un modèle, sélectionnez le modèle MLflow inscrit précédemment. Sélectionnez __Suivant__ pour continuer.

    1. Lorsque vous sélectionnez un modèle enregistré au format MLflow, dans l’étape Environnement de l’assistant, vous n’avez pas besoin d’un script de scoring et d’un environnement.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/ncd-wizard.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/ncd-wizard.png" alt-text="Capture d’écran montrant le code et l’environnement requis pour les modèles MLflow":::

    1. Terminez l’assistant pour déployer le modèle sur le point de terminaison.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/review-screen-ncd.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/review-screen-ncd.png" alt-text="Capture d’écran montrant l’écran de révision du déploiement sans code":::

    # <a name="models-page"></a>[Page Modèles](#tab/models)

    1. Sélectionnez le modèle MLflow, puis __Déployer__. Quand vous y êtes invité, sélectionnez __Déployer sur le point de terminaison en temps réel (préversion)__ .

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/deploy-from-models-ui.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/deploy-from-models-ui.png" alt-text="Capture d’écran montrant comment déployer un modèle à partir de l’interface utilisateur des modèles":::

    1. Terminez l’assistant pour déployer le modèle sur le point de terminaison.

    ---

## <a name="deploy-models-after-a-training-job"></a>Déployer des modèles après une tâche de formation

Cette section vous aide à comprendre comment déployer des modèles sur un point de terminaison en ligne géré une fois que vous avez terminé votre [travail de formation](how-to-train-cli.md).

1. Téléchargez les sorties à partir du travail de formation. Les sorties contiennent le dossier du modèle.

    > [!NOTE]
    > Si vous avez utilisé `mlflow.autolog()` dans votre script de formation, vous verrez des artefacts de modèle dans l’historique des exécutions du travail. Azure Machine Learning s’intègre avec la fonctionnalité de suivi de MLflow. Vous pouvez utiliser `mlflow.autolog()` pour plusieurs infrastructures de ML courantes afin de journaliser les paramètres de modèle, les métriques de performances, les artefacts de modèle et même des graphiques d’importance des caractéristiques.
    >
    > Pour plus d’informations, consultez [Former des modèles avec l’interface CLI](how-to-train-cli.md#model-tracking-with-mlflow). Consultez également les [exemples de travail de formation](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/single-step) dans le référentiel GitHub.

    # <a name="azure-machine-learning-studio"></a>[Azure Machine Learning Studio](#tab/studio)

    :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/download-output-logs.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/download-output-logs.png" alt-text="Capture d’écran montrant comment télécharger les sorties et les journaux à partir de l’exécution de l’expérimentation":::

    # <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

    ```azurecli
    az ml job download -n $run_id --outputs
    ```

2. Pour effectuer un déploiement à l’aide des fichiers téléchargés, vous pouvez utiliser Studio ou l’interface de ligne de commande Azure. Utilisez le dossier de modèle des sorties pour le déploiement :

    * [Déployer avec Azure Machine Learning Studio](how-to-deploy-mlflow-models-online-endpoints.md#deploy-using-azure-machine-learning-studio).
    * [Déployez à l’aide d’Azure Machine Learning CLI (v2)](how-to-deploy-mlflow-models-online-endpoints.md#deploy-using-cli-v2).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les articles suivants :

- [Déployer des modèles avec REST (préversion)](how-to-deploy-with-rest.md)
- [Créer et utiliser des points de terminaison en ligne managés (préversion) dans le studio](how-to-use-managed-online-endpoint-studio.md)
- [Déploiement sûr pour les points de terminaison en ligne (préversion)](how-to-safely-rollout-managed-endpoints.md)
- [Comment mettre à l’échelle automatiquement des points de terminaison en ligne gérés](how-to-autoscale-endpoints.md)
- [Utiliser des points de terminaison de traitement de lots (préversion) pour le scoring par lots](how-to-use-batch-endpoint.md)
- [Afficher les coûts d’un point de terminaison en ligne managé Azure Machine Learning (préversion)](how-to-view-online-endpoints-costs.md)
- [Accéder aux ressources Azure avec un point de terminaison en ligne géré et une identité managée (préversion)](how-to-access-resources-from-endpoints-managed-identities.md)
- [Résolution des problèmes de déploiement de points de terminaison en ligne managés](how-to-troubleshoot-managed-online-endpoints.md)