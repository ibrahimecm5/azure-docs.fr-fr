---
title: Déployer des modèles à l’aide de points de terminaison en ligne managés avec des API REST (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment déployer des modèles à l’aide de points de terminaison en ligne managés avec des API REST.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devplatv2
ms.openlocfilehash: 05622e6300dc19e28efaba114aded16fc1a26788
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557354"
---
# <a name="deploy-models-with-rest-preview"></a>Déployer des modèles avec REST (préversion)

Découvrez comment déployer des modèles avec l’API REST d’Azure Machine Learning (préversion).

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

## <a name="set-endpoint-name"></a>Définir le nom du point de terminaison

> [!NOTE]
> Les noms des points de terminaison doivent être uniques au niveau de la région Azure. Par exemple, il ne peut y avoir qu’un seul point de terminaison avec le nom my-endpoint dans westus2.

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="set_endpoint_name":::

## <a name="azure-machine-learning-managed-online-endpoints"></a>Points de terminaison en ligne managés Azure Machine Learning
Les points de terminaison en ligne managés (préversion) vous permettent de déployer votre modèle sans avoir à créer ni gérer l’infrastructure sous-jacente. Dans cet article, vous allez créer un point de terminaison en ligne et un déploiement, puis le valider en l’appelant. Mais tout d’abord, vous devez inscrire les ressources nécessaires au déploiement, notamment le modèle, le code et l’environnement.

Il existe de nombreuses façons de créer des points de terminaison en ligne Azure Machine Learning, [y compris Azure CLI](how-to-deploy-managed-online-endpoints.md) et visuellement avec [le studio](how-to-use-managed-online-endpoint-studio.md). L’exemple suivant présente un point de terminaison en ligne managé avec l’API REST.

## <a name="create-machine-learning-assets"></a>Créer des ressources de machine learning

Tout d’abord, définissez vos ressources Azure Machine Learning pour configurer votre travail.

Dans les appels d’API REST suivants, nous utilisons `SUBSCRIPTION_ID`, `RESOURCE_GROUP`, `LOCATION` et `WORKSPACE` comme espaces réservés. Remplacez les espaces réservés par vos valeurs. 

Les requêtes REST administratives utilisent un [jeton d’authentification de principal de service](how-to-manage-rest.md#retrieve-a-service-principal-authentication-token). Remplacez `TOKEN` par votre propre valeur. Vous pouvez récupérer ce jeton à l’aide de la commande suivante :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/deploy-rest.sh" id="get_access_token":::

Le fournisseur de services utilise l’argument `api-version` pour garantir la compatibilité. L’argument `api-version` varie d’un service à l’autre. Définissez la version de l’API en tant que variable pour prendre en charge les futures versions :

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/deploy-rest.sh" id="api_version":::

### <a name="get-storage-account-details"></a>Obtenir les détails du compte de stockage

Pour inscrire le modèle et le code, vous devez d’abord les charger dans un compte de stockage. Les détails du compte de stockage sont disponibles dans le magasin de données. Dans cet exemple, vous obtenez le magasin de stockage par défaut et le compte Stockage Azure pour votre espace de travail. Interrogez votre espace de travail avec une requête GET pour obtenir ces informations dans un fichier JSON.

Vous pouvez utiliser l’outil [jq](https://stedolan.github.io/jq/) pour analyser le résultat JSON et obtenir les valeurs nécessaires. Vous pouvez aussi utiliser le portail Azure pour rechercher les mêmes informations :

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_storage_details":::

### <a name="upload--register-code"></a>Charger et inscrire le code

Maintenant que vous disposez du magasin de données, vous pouvez charger le script de scoring. Utilisez l’interface CLI Stockage Azure pour charger un objet blob dans votre conteneur par défaut :

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="upload_code":::

> [!TIP]
> Vous pouvez aussi employer d’autres méthodes de chargement, comme le portail Azure ou l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).

Une fois le code chargé, vous pouvez le spécifier avec une requête PUT et faire référence au magasin de données avec `datastoreId` :

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_code":::

### <a name="upload-and-register-model"></a>Charger et inscrire le modèle

Comme pour le code, chargez les fichiers de modèle :

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="upload_model":::

Maintenant, inscrivez le modèle :

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_model":::

### <a name="create-environment"></a>Créer un environnement
Le déploiement doit s’exécuter dans un environnement qui a les dépendances requises. Créez l’environnement avec une demande PUT. Utilisez une image Docker à partir de Microsoft Container Registry. Vous pouvez configurer l’image Docker avec `Docker` et ajouter des dépendances conda avec `condaFile`.

Dans l’extrait de code suivant, le contenu d’un environnement Conda (fichier YAML) a été lu dans une variable d’environnement :

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_environment":::

### <a name="create-endpoint"></a>Création d’un point de terminaison

Créez le point de terminaison en ligne :

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_endpoint":::

### <a name="create-deployment"></a>Créer un déploiement

Créez un déploiement sous le point de terminaison en ligne :

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_deployment":::

### <a name="invoke-the-endpoint-to-score-data-with-your-model"></a>Appeler le point de terminaison pour effectuer le scoring des données avec votre modèle

Nous avons besoin de l’URI de scoring et du jeton d’accès pour appeler le point de terminaison. Tout d’abord, récupérez l’URI de scoring :

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_endpoint":::

Récupérez le jeton d’accès du point de terminaison :

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_access_token":::

À présent, appelez le point de terminaison à l’aide de curl :

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="score_endpoint":::

### <a name="check-the-logs"></a>Consulter les journaux

Vérifiez les journaux du déploiement :

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_deployment_logs":::

### <a name="delete-the-endpoint"></a>Supprimer le point de terminaison

Si vous n’envisagez pas d’utiliser le déploiement, vous devez le supprimer avec la commande ci-dessous (cela supprime le point de terminaison et tous les déploiements sous-jacents) :

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="delete_endpoint":::

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment déployer votre modèle [à l’aide d’Azure CLI](how-to-deploy-managed-online-endpoints.md).
* Découvrez comment déployer votre modèle [à l’aide du studio](how-to-use-managed-online-endpoint-studio.md).
* Découvrez comment [résoudre les problèmes de déploiement et de scoring de points de terminaison en ligne managés (préversion)](how-to-troubleshoot-managed-online-endpoints.md).
* Découvrir comment [accéder aux ressources Azure avec un point de terminaison en ligne managé et une identité managée (préversion)](how-to-access-resources-from-endpoints-managed-identities.md)
* Découvrez comment [superviser les points de terminaison en ligne](how-to-monitor-online-endpoints.md).
* Découvrez [Déploiement sécurisé pour les points de terminaison en ligne (préversion)](how-to-safely-rollout-managed-endpoints.md).
* [Afficher les coûts d’un point de terminaison en ligne managé Azure Machine Learning (préversion)](how-to-view-online-endpoints-costs.md).
* [Liste des SKU pour les points de terminaison en ligne managés (préversion)](reference-managed-online-endpoints-vm-sku-list.md).
* Découvrez les limites des points de terminaison managés dans [Gérer et augmenter les quotas pour les ressources avec Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).