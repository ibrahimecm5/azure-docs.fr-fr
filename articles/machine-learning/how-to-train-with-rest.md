---
title: Entraîner des modèles avec REST (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment entraîner des modèles et créer des travaux avec des API REST.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: wenxwei
ms.author: wenxwei
ms.date: 10/21/2021
ms.reviewer: peterlu
ms.custom: devplatv2
ms.openlocfilehash: f411d33baea7863b9139069f1eefd1282cf7b730
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562083"
---
# <a name="train-models-with-rest-preview"></a>Entraîner des modèles avec REST (préversion)

Découvrez comment utiliser l’API REST d’Azure Machine Learning pour créer et gérer des travaux d’entraînement (préversion).

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

L’API REST utilise des verbes HTTP standard pour créer, récupérer, mettre à jour et supprimer des ressources. L’API REST fonctionne avec tout langage de programmation ou outil pouvant effectuer des requêtes HTTP. La structure simple de REST en fait un bon choix dans les environnements de script et pour l’automatisation MLOps.

Dans cet article, vous allez apprendre à utiliser les nouvelles API REST pour :

> [!div class="checklist"]
> * Créer des ressources de machine learning
> * Créer un travail d’entraînement de base 
> * Créer un travail de balayage de réglage hyperparamétrique

## <a name="prerequisites"></a>Conditions préalables requises

- Un **abonnement Azure** pour lequel vous disposez de droits d’administration. Si vous n’avez pas d’abonnement de ce type, essayez l’[abonnement personnel gratuit ou payant](https://azure.microsoft.com/free/).
- Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md).
- Un principal de service dans votre espace de travail. Les requêtes REST administratives utilisent l’[authentification du principal de service](how-to-setup-authentication.md#use-service-principal-authentication).
- Un jeton d’authentification du principal de service. Suivez les étapes décrite dans [Récupérer un jeton d’authentification de principal de service](./how-to-manage-rest.md#retrieve-a-service-principal-authentication-token) pour récupérer ce jeton. 
- L’utilitaire **curl**. Le programme **curl** est disponible dans le [Sous-système Windows pour Linux](/windows/wsl/install-win10) ou dans toute distribution UNIX. Dans PowerShell, **curl** est un alias pour **Invoke-WebRequest** et `curl -d "key=val" -X POST uri` devient `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`. 

## <a name="azure-machine-learning-jobs"></a>Travaux Azure Machine Learning
Un travail est une ressource qui spécifie tous les aspects d’un travail de calcul. Il englobe trois choses :

- Quoi exécuter ?
- Comment l’exécuter ?
- Où l’exécuter ?

Il existe de nombreuses façons d’envoyer une tâche Azure Machine Learning, notamment le kit SDK, Azure CLI et visuellement avec le studio. L’exemple suivant envoie un travail d’entraînement LightGBM avec l’API REST.

## <a name="create-machine-learning-assets"></a>Créer des ressources de machine learning

Tout d’abord, définissez vos ressources Azure Machine Learning pour configurer votre travail.

Dans les appels d’API REST suivants, nous utilisons `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP`, `$LOCATION` et `$WORKSPACE` comme espaces réservés. Remplacez les espaces réservés par vos valeurs. 

Les requêtes REST administratives utilisent un [jeton d’authentification de principal de service](how-to-manage-rest.md#retrieve-a-service-principal-authentication-token). Remplacez `$TOKEN` par votre propre valeur. Vous pouvez récupérer ce jeton à l’aide de la commande suivante :

```bash
TOKEN=$(az account get-access-token --query accessToken -o tsv)
```

Le fournisseur de services utilise l’argument `api-version` pour garantir la compatibilité. L’argument `api-version` varie d’un service à l’autre. La version actuelle de l’API Azure Machine Learning est `2021-03-01-preview`. Définissez la version de l’API en tant que variable pour prendre en charge les futures versions :

```bash
API_VERSION="2021-03-01-preview"
```

### <a name="compute"></a>Compute

L’exécution des travaux de machine learning nécessite des ressources de calcul. Vous pouvez lister les ressources de calcul de votre espace de travail :

```bash
curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.MachineLearningServices/workspaces/$WORKSPACE/computes?api-version=$API_VERSION" \
--header "Authorization: Bearer $TOKEN"
```

Pour cet exemple, nous utilisons un cluster de calcul existant nommé `cpu-cluster`. Nous définissons le nom de la capacité de calcul en tant que variable pour l’encapsulation :

```bash
COMPUTE_NAME="cpu-cluster"
```

> [!TIP]
> Vous pouvez [créer ou remplacer une ressource de calcul nommée avec une requête PUT](./how-to-manage-rest.md#create-and-modify-resources-using-put-and-post-requests). 

### <a name="environment"></a>Environnement 

L’exemple LightGBM doit s’exécuter dans un environnement LightGBM. Créez l’environnement avec une demande PUT. Utilisez une image Docker à partir de Microsoft Container Registry.

Vous pouvez configurer l’image Docker avec `Docker` et ajouter des dépendances conda avec `condaFile` : 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_environment":::

### <a name="datastore"></a>Magasin de données

Sachant que le travail d’entraînement doit s’exécuter sur des données, vous devez spécifier un magasin de données. Dans cet exemple, vous obtenez le magasin de stockage par défaut et le compte Stockage Azure pour votre espace de travail. Interrogez votre espace de travail avec une requête GET pour obtenir ces informations dans un fichier JSON.

Vous pouvez utiliser l’outil [jq](https://stedolan.github.io/jq/) pour analyser le résultat JSON et obtenir les valeurs nécessaires. Vous pouvez aussi utiliser le portail Azure pour rechercher les mêmes informations.

```bash
response=$(curl --location --request GET "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.MachineLearningServices/workspaces/$WORKSPACE/datastores?api-version=$API_VERSION&isDefault=true" \
--header "Authorization: Bearer $TOKEN")

AZURE_STORAGE_ACCOUNT=$(echo $response | jq '.value[0].properties.contents.accountName')
AZUREML_DEFAULT_DATASTORE=$(echo $response | jq '.value[0].name')
AZUREML_DEFAULT_CONTAINER=$(echo $response | jq '.value[0].properties.contents.containerName')
AZURE_STORAGE_KEY=$(az storage account keys list --account-name $AZURE_STORAGE_ACCOUNT | jq '.[0].value')
```

### <a name="data"></a>Données

Maintenant que le magasin de données est à votre disposition, vous pouvez créer un jeu de données. Pour cet exemple, utilisez le jeu de données commun `iris.csv` et pointez dessus dans `path`. 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_data":::

### <a name="code"></a>Code

Maintenant que vous disposez du jeu de données et du magasin de données, vous pouvez charger le script d’entraînement qui va exécuter le travail. Utilisez l’interface CLI Stockage Azure pour charger un objet blob dans votre conteneur par défaut. Vous pouvez aussi employer d’autres méthodes de chargement, comme le portail Azure ou Explorateur Stockage Azure.


```bash
az storage blob upload-batch -d $AZUREML_DEFAULT_CONTAINER/src \
 -s jobs/train/lightgbm/iris/src --account-name $AZURE_STORAGE_ACCOUNT --account-key $AZURE_STORAGE_KEY
```

Une fois le code chargé, vous pouvez le spécifier avec une requête PUT et faire référence au magasin de données avec `datastoreId`. 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_code":::

## <a name="submit-a-training-job"></a>Soumettre une tâche d’apprentissage

Maintenant que vos ressources sont en place, vous pouvez exécuter le travail LightGBM, qui génère un modèle entraîné et des métadonnées. Pour configurer le travail d’entraînement, vous avez besoin des informations suivantes : 

- **run_id** : [Facultatif] Nom du travail, qui doit être unique parmi tous les travaux. Si aucun nom n’est spécifié dans le fichier YAML via le champ `name` ou dans la ligne de commande via `--name/-n`, un GUID/UUID est automatiquement généré et utilisé pour le nom.
- **jobType** : type du travail. Pour un travail d’entraînement de base, utilisez `Command`.
- **codeId** : chemin de votre script d’entraînement.
- **command** : commande à exécuter. Les données d’entrée peuvent être écrites dans la commande et il est possible d’y faire référence avec la liaison de données. 
- **environmentId** : chemin de votre environnement.
- **inputDataBindings** : la liaison de données peut vous aider à référencer des données d’entrée. Créez une variable d’environnement et le nom de la liaison sera ajouté à AZURE_ML_INPUT_, auquel vous pouvez vous faire référence dans `command`. Pour créer une liaison de données, vous devez ajouter le chemin des données que vous avez créées en tant que `dataId`. 
- **experimentName** : [Facultatif] étiquette le travail pour faciliter l’organisation des travaux dans le studio Azure Machine Learning. Chaque enregistrement d’exécution du travail est organisé au titre de l’expérience correspondante dans l’onglet « Experiment » (Expérience) du studio. En cas d’omission, les étiquettes prennent par défaut le nom du répertoire de travail au moment où le travail est créé.
- **compute** : `target` spécifie la cible de calcul, qui peut être le chemin de votre capacité de calcul. `instanceCount` spécifie le nombre d’instances dont vous avez besoin pour le travail.

Utilisez les commandes suivantes pour envoyer le travail d’entraînement :

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_job":::

## <a name="submit-a-hyperparameter-sweep-job"></a>Envoyer un travail de balayage hyperparamétrique

Azure Machine Learning vous permet également de régler efficacement les hyperparamètres d’entraînement. Vous pouvez créer une suite de réglage hyperparamétrique avec les API REST. Pour plus d’informations sur les options de réglage hyperparamétrique d’Azure Machine Learning, consultez [Réglage hyperparamétrique d’un modèle](how-to-tune-hyperparameters.md). Spécifiez les paramètres de réglage hyperparamétrique pour configurer le balayage :

- **jobType** : type du travail. Pour un travail de nettoyage, il s’agit de `Sweep`. 
- **algorithm** : l’algorithme d’échantillonnage - "random" est souvent un bon point de départ. Reportez-vous au [schéma](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json) du travail de balayage pour voir l’énumération des options. 
- **trial** : configuration du travail de commande pour chaque essai à exécuter. 
- **objective** : `primaryMetric` est la métrique d’optimisation, qui doit correspondre au nom d’une métrique journalisée à partir du code d’entraînement. `goal` spécifie la direction (« minimize » ou « maximize »). Reportez-vous au [schéma](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json) pour voir l’énumération complète des options. 
- **searchSpace** : dictionnaire des hyperparamètres à balayer. La clé est un nom pour l’hyperparamètre, par exemple `learning_rate`. La valeur est la distribution hyperparamétrique. Reportez-vous au [schéma](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json) pour voir l’énumération des options.
- **maxTotalTrials** : nombre maximal d’essais individuels à exécuter.
- **maxConcurrentTrials** : [Facultatif] nombre maximal d’essais à exécuter simultanément sur votre cluster de calcul.
- **timeout** : [Facultatif] nombre maximal de minutes durant lesquelles le travail de balayage doit être exécuté.

Pour créer un travail de balayage avec le même exemple LightGBM, utilisez les commandes suivantes : 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_a_sweep_job":::

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un modèle entraîné, découvrez [comment déployer votre modèle](how-to-deploy-and-where.md).
