---
title: Déployer des modèles à l’aide de points de terminaison de lot avec des API REST (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment déployer des modèles à l’aide de points de terminaison de lot avec des API REST.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: tracych
ms.author: tracych
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devplatv2
ms.openlocfilehash: ad4c69509430fc448e5432237a3d9c6af6fd5c18
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566720"
---
# <a name="deploy-models-with-rest-preview-for-batch-scoring"></a>Déployer des modèles avec REST (préversion) pour le scoring par lot 

Découvrez comment déployer des modèles avec l’API REST d’Azure Machine Learning pour le scoring par lots (préversion).

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

L’API REST utilise des verbes HTTP standard pour créer, récupérer, mettre à jour et supprimer des ressources. L’API REST fonctionne avec tout langage de programmation ou outil pouvant effectuer des requêtes HTTP. La structure simple de REST en fait un bon choix dans les environnements de script et pour l’automatisation MLOps.

Dans cet article, vous allez apprendre à utiliser les nouvelles API REST pour :

> [!div class="checklist"]
> * Créer des ressources de machine learning
> * Créer un point de terminaison de lot et un déploiement par lots
> * Appeler un point de terminaison de lot pour démarrer un travail de scoring par lot

## <a name="prerequisites"></a>Conditions préalables requises

- Un **abonnement Azure** pour lequel vous disposez de droits d’administration. Si vous n’avez pas d’abonnement de ce type, essayez l’[abonnement personnel gratuit ou payant](https://azure.microsoft.com/free/).
- Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md).
- Un principal de service dans votre espace de travail. Les requêtes REST administratives utilisent l’[authentification du principal de service](how-to-setup-authentication.md#use-service-principal-authentication).
- Un jeton d’authentification du principal de service. Suivez les étapes décrite dans [Récupérer un jeton d’authentification de principal de service](./how-to-manage-rest.md#retrieve-a-service-principal-authentication-token) pour récupérer ce jeton. 
- L’utilitaire **curl**. Le programme **curl** est disponible dans le [Sous-système Windows pour Linux](/windows/wsl/install-win10) ou dans toute distribution UNIX. Dans PowerShell, **curl** est un alias pour **Invoke-WebRequest** et `curl -d "key=val" -X POST uri` devient `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`. 
- Le processeur JSON [jq](https://stedolan.github.io/jq/).

> [!IMPORTANT]
> Les extraits de code de cet article supposent que vous utilisez l’interpréteur de commandes Bash.
>
> Les extraits de code sont extraits du fichier `/cli/batch-score-rest.sh` dans le [référentiel d’exemples AzureML](https://github.com/Azure/azureml-examples).

## <a name="set-endpoint-name"></a>Définir le nom du point de terminaison

> [!NOTE]
> Les noms des points de terminaison de lot doivent être uniques au niveau de la région Azure. Par exemple, il ne peut y avoir qu’un seul point de terminaison de lot avec le nom mybatchendpoint dans westus2.

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="set_endpoint_name":::

## <a name="azure-machine-learning-batch-endpoints"></a>Points de terminaison de lot Azure Machine Learning

Les [points de terminaison de lot (préversion)](concept-endpoints.md#what-are-batch-endpoints-preview) simplifient le processus d’hébergement de vos modèles pour le scoring par lots, ce qui vous permet de vous concentrer sur le machine learning, et non sur l’infrastructure. Dans cet article, vous allez créer un point de terminaison de lot et un déploiement, et l’appeler pour démarrer un travail de scoring par lots. Mais tout d’abord, vous devez inscrire les ressources nécessaires au déploiement, notamment le modèle, le code et l’environnement.

Il existe de nombreuses façons de créer des points de terminaison de lot Azure Machine Learning, [y compris Azure CLI](how-to-use-batch-endpoint.md) et visuellement avec [le studio](how-to-use-batch-endpoints-studio.md). L’exemple suivant crée un point de terminaison de lot et un déploiement avec l’API REST.

## <a name="create-machine-learning-assets"></a>Créer des ressources de machine learning

Tout d’abord, définissez vos ressources Azure Machine Learning pour configurer votre travail.

Dans les appels d’API REST suivants, nous utilisons `SUBSCRIPTION_ID`, `RESOURCE_GROUP`, `LOCATION` et `WORKSPACE` comme espaces réservés. Remplacez les espaces réservés par vos valeurs. 

Les requêtes REST administratives utilisent un [jeton d’authentification de principal de service](how-to-manage-rest.md#retrieve-a-service-principal-authentication-token). Remplacez `TOKEN` par votre propre valeur. Vous pouvez récupérer ce jeton à l’aide de la commande suivante :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" range="13":::

Le fournisseur de services utilise l’argument `api-version` pour garantir la compatibilité. L’argument `api-version` varie d’un service à l’autre. Définissez la version de l’API en tant que variable pour prendre en charge les futures versions :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" range="11":::

### <a name="create-compute"></a>Créer une capacité de calcul
Le scoring par lots s’exécute uniquement sur des ressources de cloud computing, pas localement. La ressource de cloud computing est un cluster de machines virtuelles réutilisable dans lequel vous pouvez exécuter des workflows de scoring par lots.

Créer un cluster de calcul :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="create_compute":::

> [!TIP]
> Si vous souhaitez utiliser un calcul existant à la place, vous devez spécifier l’ID Azure Resource Manager complet lors de la [création du déploiement par lots](#create-batch-deployment). L’ID complet utilise le format `/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.MachineLearningServices/workspaces/$WORKSPACE/computes/<your-compute-name>`.

### <a name="get-storage-account-details"></a>Obtenir les détails du compte de stockage

Pour inscrire le modèle et le code, vous devez d’abord les charger dans un compte de stockage. Les détails du compte de stockage sont disponibles dans le magasin de données. Dans cet exemple, vous obtenez le magasin de stockage par défaut et le compte Stockage Azure pour votre espace de travail. Interrogez votre espace de travail avec une requête GET pour obtenir ces informations dans un fichier JSON.

Vous pouvez utiliser l’outil [jq](https://stedolan.github.io/jq/) pour analyser le résultat JSON et obtenir les valeurs nécessaires. Vous pouvez aussi utiliser le portail Azure pour rechercher les mêmes informations :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="get_storage_details":::

### <a name="upload--register-code"></a>Charger et inscrire le code

Maintenant que vous disposez du magasin de données, vous pouvez charger le script de scoring. Utilisez l’interface CLI Stockage Azure pour charger un objet blob dans votre conteneur par défaut :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="upload_code":::

> [!TIP]
> Vous pouvez aussi employer d’autres méthodes de chargement, comme le portail Azure ou l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).

Une fois que vous avez chargé votre code, vous pouvez spécifier votre code avec une requête PUT :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="create_code":::

### <a name="upload-and-register-model"></a>Charger et inscrire le modèle

Comme pour le code, chargez les fichiers de modèle :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="upload_model":::

Maintenant, inscrivez le modèle :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="create_model":::

### <a name="create-environment"></a>Créer un environnement
Le déploiement doit s’exécuter dans un environnement qui a les dépendances requises. Créez l’environnement avec une demande PUT. Utilisez une image Docker à partir de Microsoft Container Registry. Vous pouvez configurer l’image Docker avec `image` et ajouter des dépendances conda avec `condaFile`.

Exécutez le code suivant pour lire le `condaFile` défini dans le JSON. Le fichier source se trouve dans le référentiel d’exemples `/cli/endpoints/batch/mnist/environment/conda.json` :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="read_condafile":::

À présent, exécutez l’extrait de code suivant pour créer un environnement :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="create_environment":::

## <a name="deploy-with-batch-endpoints"></a>Déployer avec des points de terminaison de lot

Ensuite, créez le point de terminaison de lot, un déploiement et définissez le déploiement par défaut.

### <a name="create-batch-endpoint"></a>Créer un point de terminaison de traitement de lots

Créez le point de terminaison de lot :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="create_endpoint":::

### <a name="create-batch-deployment"></a>Créer un déploiement par lots

Créez un déploiement de lot sous le point de terminaison en ligne :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="create_deployment":::

### <a name="set-the-default-batch-deployment-under-the-endpoint"></a>Définir le déploiement par lots par défaut sous le point de terminaison

Il n’existe qu’un seul déploiement par lots par défaut sous un point de terminaison, qui sera utilisé lors de l’appel pour exécuter le travail de scoring par lots.

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="set_endpoint_defaults":::

## <a name="run-batch-scoring"></a>Exécuter le scoring par lots

L’appel d’un point de terminaison de lot déclenche un travail de scoring par lots. Un travail `id` est retourné dans la réponse et peut être utilisé pour suivre la progression du scoring par lots. Dans les extraits de code suivants, `jq` est utilisé pour récupérer le travail `id`.

### <a name="invoke-the-batch-endpoint-to-start-a-batch-scoring-job"></a>Appeler le point de terminaison de lot pour démarrer un travail de scoring par lot

Obtenez l’URI de scoring et le jeton d’accès pour appeler le point de terminaison de lot. Tout d’abord, récupérez l’URI de scoring :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="get_endpoint":::

Récupérez le jeton d’accès du point de terminaison de lot :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="get_access_token":::

À présent, appelez le point de terminaison de lot pour démarrer un travail de scoring par lot. L’exemple suivant note des données publiques disponibles dans le cloud :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="score_endpoint_with_data_in_cloud":::

Si vos données sont stockées dans un magasin de données Azure Machine Learning inscrit, vous pouvez appeler le point de terminaison de lot avec un jeu de données. Le code suivant crée un nouveau jeu de données :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="create_dataset":::

Ensuite, référencez le jeu de données lors de l’appel du point de terminaison de lot :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="score_endpoint_with_dataset":::

Dans l’extrait de code précédent, un emplacement de sortie personnalisé est fourni à l’aide de `datastoreId`, `path` et `outputFileName`. Ces paramètres vous permettent de configurer l’emplacement de stockage des résultats du scoring par lots.

> [!IMPORTANT]
> Vous devez fournir un emplacement de sortie unique. Si le fichier de sortie existe déjà, le travail de scoring par lots échouera.

Pour cet exemple, la sortie est stockée dans le stockage d’objets Blob par défaut de l’espace de travail. Le nom du dossier est le même que le nom du point de terminaison, et le nom du fichier est généré de manière aléatoire par le code suivant :

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" ID="unique_output" :::

### <a name="check-the-batch-scoring-job"></a>Vérifier le travail de scoring par lot

Les travaux de scoring par lots prennent généralement un certain temps pour traiter l’ensemble des entrées. Surveillez l’état du travail et vérifiez les résultats une fois l’opération terminée :

> [!TIP]
> L’exemple appelle le déploiement par défaut du point de terminaison de lot. Pour appeler un déploiement autre que celui par défaut, utilisez l’en-tête HTTP `azureml-model-deployment` et définissez la valeur sur le nom du déploiement. Par exemple, à l’aide d’un paramètre `--header "azureml-model-deployment: $DEPLOYMENT_NAME"` avec curl.

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="check_job":::

### <a name="check-batch-scoring-results"></a>Vérifier les résultats du scoring par lots

Pour plus d’informations sur la vérification des résultats, consultez [Vérifier les résultats du scoring par lots](how-to-use-batch-endpoint.md#check-batch-scoring-results).

## <a name="delete-the-batch-endpoint"></a>Supprimer le point de terminaison de lot

Si vous n’envisagez pas d’utiliser le point de terminaison de lot, vous devez le supprimer avec la commande ci-dessous (cela supprime le point de terminaison et tous les déploiements de lot sous-jacents) :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/batch-score-rest.sh" id="delete_endpoint":::

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment déployer votre modèle pour le scoring par lots [à l’aide d’Azure CLI](how-to-use-batch-endpoint.md).
* Découvrez comment déployer votre modèle pour le scoring par lots [à l’aide de Studio](how-to-use-batch-endpoints-studio.md).
* Apprenez à [Résoudre les problèmes liés aux points de terminaison de traitement de lots](how-to-troubleshoot-batch-endpoints.md)
