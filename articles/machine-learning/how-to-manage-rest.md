---
title: Utiliser REST pour gérer des ressources ML
titleSuffix: Azure Machine Learning
description: Comment utiliser des API REST pour créer, exécuter et supprimer des ressources Azure Machine Learning, telles qu’un espace de travail, ou enregistrer des modèles.
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 08/10/2020
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 9465a8c45dd44eca4fcd67a8603e60a2061f2609
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518182"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Créer, exécuter et supprimer des ressources Azure ML à l’aide de REST



Il existe plusieurs façons de gérer vos ressources Azure ML. Vous pouvez utiliser le [portail](https://portal.azure.com/), l’[interface de ligne de commande](/cli/azure) ou le [Kit de développement logiciel (SDK) Python](/python/api/overview/azure/ml/intro). Ou vous pouvez choisir l’API REST. L’API REST utilise des verbes HTTP de manière standard pour créer, récupérer, mettre à jour et supprimer des ressources. L’API REST fonctionne avec tout langage de programmation ou outil pouvant effectuer des requêtes HTTP. La structure simple de REST en fait souvent un bon choix dans les environnements de script et pour l’automatisation MLOps. 

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Récupérer un jeton d’autorisation
> * Créer une requête REST correctement formatée à l’aide de l’authentification du principal de service
> * Utiliser des requêtes GET pour récupérer des informations sur les ressources hiérarchiques d’Azure ML
> * Utiliser des requêtes PUT et POST pour créer et modifier des ressources
> * utiliser les demandes PUT pour créer des espaces de travail Azure ML
> * Utiliser des requêtes DELETE pour nettoyer les ressources 

## <a name="prerequisites"></a>Conditions préalables requises

- Un **abonnement Azure** pour lequel vous disposez de droits d’administration. Si vous n’avez pas d’abonnement de ce type, essayez l’[abonnement personnel gratuit ou payant](https://azure.microsoft.com/free/)
- Un [espace de travail Azure Machine Learning](./how-to-manage-workspace.md)
- Les requêtes REST administratives utilisent l’authentification du principal de service. Suivez les étapes décrites dans [Configurer l’authentification pour des ressources et workflows Azure Machine Learning](./how-to-setup-authentication.md#service-principal-authentication) pour créer un principal de service dans votre espace de travail.
- L’utilitaire **curl**. Le programme **curl** est disponible dans le [Sous-système Windows pour Linux](/windows/wsl/install-win10) ou dans toute distribution UNIX. Dans PowerShell, **curl** est un alias pour **Invoke-WebRequest** et `curl -d "key=val" -X POST uri` devient `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`. 

## <a name="retrieve-a-service-principal-authentication-token"></a>Récupérer un jeton d’authentification du principal de service

Les requêtes REST administratives sont authentifiées à l’aide d’un flux implicite OAuth2. Ce flux d’authentification utilise un jeton fourni par le principal de service de votre abonnement. Pour récupérer ce jeton, vous aurez besoin des éléments suivants :

- Votre ID de locataire (identifie l’organisation à laquelle votre abonnement appartient)
- Votre ID client (qui sera associé au jeton créé)
- Votre clé secrète client (que vous devez protéger)

Vous devez avoir obtenu ces valeurs dans la réponse à la création de votre principal de service. L’obtention de ces valeurs est documentée dans [Configurer l’authentification pour des ressources et workflows Azure Machine Learning](./how-to-setup-authentication.md#service-principal-authentication). Si vous utilisez l’abonnement de votre entreprise, vous n’êtes peut-être pas autorisé à créer un principal de service. Dans ce cas, vous devez utiliser une [abonnement personnel gratuit ou payant](https://azure.microsoft.com/free/).

Pour récupérer un jeton :

1. Ouvrez une fenêtre de terminal.
1. Entrez le code suivant sur la ligne de commande
1. Remplacez `<YOUR-TENANT-ID>`, `<YOUR-CLIENT-ID>` et `<YOUR-CLIENT-SECRET>` par vos propres valeurs. Tout au long de cet article, les chaînes placées entre parenthèses sont des variables que vous devrez remplacer par vos propres valeurs appropriées.
1. Exécutez la commande.

```bash
curl -X POST https://login.microsoftonline.com/<YOUR-TENANT-ID>/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=<YOUR-CLIENT-ID>&client_secret=<YOUR-CLIENT-SECRET>" \
```

La réponse doit fournir un jeton d’accès valable pendant une heure :

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "YOUR-ACCESS-TOKEN"
}
```

Notez le jeton, car vous l’utiliserez pour authentifier toutes les requêtes administratives supplémentaires ultérieures. Pour ce faire, vous devez définir un en-tête d’autorisation dans toutes les requêtes :

```bash
curl -h "Authorization:Bearer <YOUR-ACCESS-TOKEN>" ...more args...
```

> [!NOTE]
> Notez que la valeur commence par la chaîne « Porteur », comprenant une espace unique avant que vous n’ajoutiez le jeton.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Obtenir la liste des groupes de ressources associés à votre abonnement

Pour récupérer la liste des groupes de ressources associés à votre abonnement, exécutez :

```bash
curl https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups?api-version=2021-03-01-preview -H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

Dans Azure, de nombreuses API REST sont publiées. Chaque fournisseur de services met à jour son API à son propre rythme, mais le fait sans arrêter les programmes existants. Le fournisseur de services utilise l’argument `api-version` pour garantir la compatibilité. L’argument `api-version` varie d’un service à l’autre. Pour le service Machine Learning, par exemple, la version actuelle de l’API est `2021-03-01-preview`. Pour les comptes de stockage, il s’agit de `2019-08-01`. Pour les coffres de clés, il s’agit de `2019-09-01`. Tous les appels REST doivent définir l’argument `api-version` sur la valeur attendue. Vous pouvez vous appuyer sur la syntaxe et la sémantique de la version spécifiée, même si l’API continue d’évoluer. Si vous envoyez une requête à un fournisseur sans l’argument `api-version`, la réponse contiendra une liste lisible par un humain des valeurs prises en charge. 

L’appel ci-dessus génère une réponse JSON compactée au format suivant : 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Explorer au niveau du détail des espaces de travail et leurs ressources

Pour récupérer l’ensemble des espaces de travail dans un groupe de ressources, exécutez la commande suivante, en remplaçant `<YOUR-SUBSCRIPTION-ID>`, `<YOUR-RESOURCE-GROUP>` et `<YOUR-ACCESS-TOKEN>` : 

```
curl https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

Là encore, vous recevrez une liste JSON, contenant cette fois-ci une liste dont chaque élément détaille un espace de travail :

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Pour travailler avec des ressources au sein d’un espace de travail, vous basculerez du serveur général **management.azure.com** vers un serveur d’API REST spécifique à l’emplacement de l’espace de travail. Notez la valeur de la clé de `discoveryUrl` dans la réponse JSON ci-dessus. Si vous envoyez une requête GET à cette URL, vous recevrez une réponse semblable à la suivante :

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

La valeur de la réponse `api` est l’URL du serveur que vous utiliserez pour des requêtes supplémentaires. Par exemple, pour répertorier les expériences, envoyez la commande suivante. Remplacez `REGIONAL-API-SERVER` par la valeur de la réponse `api` (par exemple : `centralus.api.azureml.ms`). Remplacez également `YOUR-SUBSCRIPTION-ID`, `YOUR-RESOURCE-GROUP`, `YOUR-WORKSPACE-NAME` et `YOUR-ACCESS-TOKEN` comme d’habitude :

```bash
curl https://<REGIONAL-API-SERVER>/history/v1.0/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/experiments?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

De même, pour récupérer les modèles inscrits dans votre espace de travail, envoyez :

```bash
curl https://<REGIONAL-API-SERVER>/modelmanagement/v1.0/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/models?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

Notez que pour répertorier les expériences, le chemin d’accès commence par `history/v1.0`, tandis que pour répertorier les modèles, le chemin d’accès commence par `modelmanagement/v1.0`. L’API REST est divisée en plusieurs groupes opérationnels, chacun doté d’un chemin d’accès distinct. 

|Domaine|Path|
|-|-|
|Artefacts|/rest/api/azureml|
|Magasins de données|/azure/machine-learning/how-to-access-data|
|Optimisation des hyperparamètres|hyperdrive/v1.0/|
|Modèles|modelmanagement/v1.0/|
|Historique d’exécution|execution/v1.0/ et history/v1.0/|

Vous pouvez explorer l’API REST à l’aide du modèle général de :

|Composant URL|Exemple|
|-|-|
| https://| |
| REGIONAL-API-SERVER/ | centralus.api.azureml.ms/ |
| operations-path/ | history/v1.0/ |
| subscriptions/YOUR-SUBSCRIPTION-ID/ | subscriptions/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/YOUR-RESOURCE-GROUP/ | resourceGroups/MyResourceGroup/ |
| providers/operation-provider/ | providers/Microsoft.MachineLearningServices/ |
| provider-resource-path/ | workspaces/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| operations-endpoint/ | artifacts/metadata/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Créer et modifier des ressources à l’aide de requêtes PUT et POST

En plus de la récupération des ressources à l’aide du verbe GET, l’API REST prend en charge la création de toutes les ressources nécessaires pour l’apprentissage, le déploiement et la surveillance des solutions ML. 

La formation et l’exécution de modèles ML nécessitent des ressources de calcul. Vous pouvez répertorier les ressources de calcul d’un espace de travail avec : 

```bash
curl https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/computes?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

Pour créer ou remplacer une ressource de calcul nommée, vous utiliserez une requête PUT. Dans ce qui suit, outre les substitutions désormais familières de `YOUR-SUBSCRIPTION-ID`, `YOUR-RESOURCE-GROUP`, `YOUR-WORKSPACE-NAME` et `YOUR-ACCESS-TOKEN`, remplacez `YOUR-COMPUTE-NAME` ainsi que les valeurs pour `location`, `vmSize`, `vmPriority`, `scaleSettings`, `adminUserName` et `adminUserPassword`. Comme indiqué dans la référence à [Capacité de calcul Machine Learning : créer ou mettre à jour la référence du Kit de développement logiciel (SDK)](/rest/api/azureml/workspaces/createorupdate), la commande suivante crée une ressource Standard_D1 dédiée à nœud unique (ressource de calcul de base de l’UC) qui descendra en puissance après 30 minutes :

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/computes/<YOUR-COMPUTE-NAME>?api-version=2021-03-01-preview' \
  -H 'Authorization:Bearer <YOUR-ACCESS-TOKEN>' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "eastus",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "<ADMIN_USERNAME>",
        "adminUserPassword": "<ADMIN_PASSWORD>"
    }
}'
```

> [!Note]
> Dans les terminaux Windows, vous devrez peut-être faire précéder les guillemets doubles d’un caractère d’échappement lors de l’envoi de données JSON. Autrement dit, un texte comme `"location"` devient `\"location\"`. 

Une requête réussie recevra une réponse `201 Created`, mais notez que cette réponse signifie simplement que le processus d’approvisionnement a commencé. Vous devrez procéder à un sondage (ou utiliser le portail) pour confirmer son bon déroulement.

## <a name="create-a-workspace-using-rest"></a>Créer un espace de travail à l’aide de REST 

Chaque espace de travail Azure ML dépend de quatre autres ressources azure : une ressource Azure Container Registry, Azure Key Vault, Azure Application Informations et un compte stockage Azure. Vous ne pouvez pas créer un espace de travail tant que ces ressources n’existent pas. Pour plus d’informations sur la création de ces ressources, consultez les informations de référence sur l’API REST.

Pour créer un espace de travail, faites une requête PUT auprès de `management.azure.com` selon un appel similaire à ce qui suit. Bien que cet appel vous oblige à définir un grand nombre de variables, il est structurellement identique aux autres appels dont il a été question dans cet article. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-NEW-WORKSPACE-NAME>?api-version=2021-03-01-preview' \
  -H 'Authorization: Bearer <YOUR-ACCESS-TOKEN>' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "AZURE-LOCATION>",
    "identity" : {
        "type" : "systemAssigned"
    },
    "properties": {
        "friendlyName" : "<YOUR-WORKSPACE-FRIENDLY-NAME>",
        "description" : "<YOUR-WORKSPACE-DESCRIPTION>",
        "containerRegistry" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.ContainerRegistry/registries/<YOUR-REGISTRY-NAME>",
        keyVault" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.Keyvault/vaults/<YOUR-KEYVAULT-NAME>",
        "applicationInsights" : "subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.insights/components/<YOUR-APPLICATION-INSIGHTS-NAME>",
        "storageAccount" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.Storage/storageAccounts/<YOUR-STORAGE-ACCOUNT-NAME>"
    }
}'
```

Vous devez recevoir une réponse `202 Accepted` et, dans les en-têtes retournés, un URI `Location`. Vous pouvez OBTENIR cet URI pour plus d’informations sur le déploiement, y compris des informations utiles pour le débogage en cas de problème avec l’une de vos ressources dépendantes (par exemple, si vous avez oublié d’activer l’accès administrateur sur votre registre de conteneurs). 

## <a name="create-a-workspace-using-a-user-assigned-managed-identity"></a>Créer un espace de travail à l’aide d’une identité gérée affectée par l’utilisateur 

Lorsque vous créez un espace de travail, vous pouvez spécifier une identité managée affectée par l’utilisateur qui sera utilisée pour accéder aux ressources associées : ACR, Key Vault, Stockage et App Insights. Pour créer un espace de travail avec une identité gérée affectée par l’utilisateur, utilisez le corps de la requête ci-dessous. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-NEW-WORKSPACE-NAME>?api-version=2021-03-01-preview' \
  -H 'Authorization: Bearer <YOUR-ACCESS-TOKEN>' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "AZURE-LOCATION>",
    "identity": {
      "type": "SystemAssigned,UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.ManagedIdentity/userAssignedIdentities/<YOUR-MANAGED-IDENTITY>": {}
      }
    },
    "properties": {
        "friendlyName" : "<YOUR-WORKSPACE-FRIENDLY-NAME>",
        "description" : "<YOUR-WORKSPACE-DESCRIPTION>",
        "containerRegistry" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.ContainerRegistry/registries/<YOUR-REGISTRY-NAME>",
        keyVault" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.Keyvault/vaults/<YOUR-KEYVAULT-NAME>",
        "applicationInsights" : "subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.insights/components/<YOUR-APPLICATION-INSIGHTS-NAME>",
        "storageAccount" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.Storage/storageAccounts/<YOUR-STORAGE-ACCOUNT-NAME>"
    }
}'
```

## <a name="create-a-workspace-using-customer-managed-encryption-keys"></a>Créer un espace de travail à l’aide de clés de chiffrement gérées par le client

Par défaut, les métadonnées de l’espace de travail sont stockées dans une instance d’Azure Cosmos DB gérée par Microsoft. Les données sont chiffrées avec des clés managées par Microsoft. Au lieu d’utiliser la clé gérée par Microsoft, vous pouvez fournir la clé de votre choix. Cela a pour effet de créer un [ensemble supplémentaire de ressources](/azure/machine-learning/concept-data-encryption#azure-cosmos-db) dans votre abonnement Azure pour stocker vos données.

Pour créer un espace de travail qui utilise vos clés pour le chiffrement, vous devez respecter les conditions préalables suivantes :

* le principal du service Azure Machine Learning doit avoir un accès contributeur à votre abonnement Azure.
* Vous devez disposer d’un Azure Key Vault contenant une clé de chiffrement.
* L’Azure Key Vault doit se exister dans la même région Azure que celle où vous allez créer l’espace de travail Azure Machine Learning.
* La Azure Key Vault doit avoir la suppression réversible et la protection de purge activée pour protéger contre la perte de données en cas de suppression accidentelle.
* Vous devez disposer dans l’Azure Key Vault d’une stratégie d’accès accordant les accès get, wrap et unwrap à l’application Azure Cosmos DB.

Pour créer un espace de travail qui utilise une identité gérée affectée par l’utilisateur et des clés gérées par le client pour le chiffrement, utilisez le corps de la requête ci-dessous. Lors de l’utilisation d’une identité gérée affectée par l’utilisateur pour l’espace de travail, affectez également `userAssignedIdentity` à la propriété l’ID de ressource de l’identité gérée.

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-NEW-WORKSPACE-NAME>?api-version=2021-03-01-preview' \
  -H 'Authorization: Bearer <YOUR-ACCESS-TOKEN>' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "eastus2euap",
    "identity": {
      "type": "SystemAssigned"
    },
    "properties": {
      "friendlyName": "<YOUR-WORKSPACE-FRIENDLY-NAME>",
      "description": "<YOUR-WORKSPACE-DESCRIPTION>",
      "containerRegistry" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.ContainerRegistry/registries/<YOUR-REGISTRY-NAME>",
      "keyVault" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.Keyvault/vaults/<YOUR-KEYVAULT-NAME>",
      "applicationInsights" : "subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.insights/components/<YOUR-APPLICATION-INSIGHTS-NAME>",
      "storageAccount" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.Storage/storageAccounts/<YOUR-STORAGE-ACCOUNT-NAME>",
      "encryption": {
        "status": "Enabled",
        "identity": {
          "userAssignedIdentity": null
        },      
        "keyVaultProperties": {
           "keyVaultArmId": "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.KeyVault/vaults/<YOUR-VAULT>",
           "keyIdentifier": "https://<YOUR-VAULT>.vault.azure.net/keys/<YOUR-KEY>/<YOUR-KEY-VERSION>",
           "identityClientId": ""
        }
      },
      "hbiWorkspace": false
    }
}'
```

### <a name="delete-resources-you-no-longer-need"></a>Supprimer les ressources dont vous n’avez plus besoin

Certaines ressources, mais pas toutes, prennent en charge le verbe DELETE. Vérifiez les [informations de référence sur l’API](/rest/api/azureml/) avant de valider l’API REST pour les cas d’utilisation en suppression. Par exemple, pour supprimer un modèle, vous pouvez utiliser :

```bash
curl
  -X DELETE \
'https://<REGIONAL-API-SERVER>/modelmanagement/v1.0/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/models/<YOUR-MODEL-ID>?api-version=2021-03-01-preview' \
  -H 'Authorization:Bearer <YOUR-ACCESS-TOKEN>' 
```

## <a name="troubleshooting"></a>Dépannage

### <a name="resource-provider-errors"></a>Erreurs du fournisseur de ressources

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Déplacement de l’espace de travail

> [!WARNING]
> Le déplacement de votre espace de travail Azure Machine Learning vers un autre abonnement, ou le déplacement de l’abonnement propriétaire vers un nouveau locataire, n’est pas pris en charge. En effet, cela peut provoquer des erreurs.

### <a name="deleting-the-azure-container-registry"></a>Suppression d'Azure Container Registry

L'espace de travail Azure Machine Learning utilise Azure Container Registry (ACR) pour certaines opérations. Il crée automatiquement une instance ACR dès qu'il en a besoin.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Étapes suivantes

- Explorez la [documentation complète de l’API REST AzureML](/rest/api/azureml/).
- Découvrez comment utiliser le concepteur pour [prédire le prix de voitures avec le concepteur](./tutorial-designer-automobile-price-train-score.md).
- Explorez [Azure Machine Learning avec des notebooks Jupyter](..//machine-learning/samples-notebooks.md).
