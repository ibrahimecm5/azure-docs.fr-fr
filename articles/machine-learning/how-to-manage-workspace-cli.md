---
title: Créer des espaces de travail avec Azure CLI
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser l’extension Azure CLI pour Machine Learning afin de créer un espace de travail Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 09/23/2021
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: 029202fa236f5a7be2e3b3cbc650f2e54a4d1015
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085751"
---
# <a name="manage-azure-machine-learning-workspaces-using-azure-cli"></a>Gérer les espaces de travail Azure Machine Learning à l’aide d’Azure CLI

Cet article explique comment créer et gérer un espace de travail Azure Machine Learning à l’aide d’Azure CLI. Azure CLI fournit des commandes pour la gestion des ressources Azure, et est conçu pour vous permettre de commencer rapidement à utiliser Azure en mettant l’accent sur l’automatisation. L’extension Machine Learning de l’interface CLI fournit les commandes pour utiliser les ressources d’Azure Machine Learning.

> [!NOTE]
> Les exemples présentés dans cet article font référence aux versions 1.0 et v2 de l’interface CLI. L’interface CLI Machine Learning (v2) est actuellement en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production.

## <a name="prerequisites"></a>Prérequis

* Un **abonnement Azure**. Si vous n’en avez pas, essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/).

* Pour utiliser les commandes CLI dans ce document à partir de votre **environnement local**, vous avez besoin [d’Azure CLI](/cli/azure/install-azure-cli).

    Si vous utilisez [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), l’interface CLI est accessible via le navigateur et réside dans le cloud.

## <a name="limitations"></a>Limites

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>Connexion de la CLI à votre abonnement Azure

> [!IMPORTANT]
> Si vous utilisez Azure Cloud Shell, vous pouvez ignorer cette section. Le shell cloud vous authentifie automatiquement à l’aide du compte avec lequel vous vous connectez à votre abonnement Azure.

Il existe plusieurs façons de vous authentifier auprès de votre abonnement Azure à partir de l’interface CLI. La plus simple consiste à s’authentifier de manière interactive à l’aide d’un navigateur. Pour vous authentifier de manière interactive, ouvrez une ligne de commande ou un terminal et utilisez la commande suivante :

```azurecli-interactive
az login
```

Si l’interface CLI peut ouvrir votre navigateur par défaut, elle le fera et chargera une page de connexion par la même occasion. Dans le cas contraire, vous devez ouvrir un navigateur et suivre les instructions de la ligne de commande. Les instructions impliquent de naviguer vers [https://aka.ms/devicelogin](https://aka.ms/devicelogin) et d’entrer un code d’autorisation.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Pour les autres méthodes d’authentification, consultez [Se connecter avec Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

L’espace de travail Azure Machine Learning doit être créé à l’intérieur d’un groupe de ressources. Vous pouvez utiliser un groupe de ressources existant ou en créer un. Pour __créer un groupe de ressources__, utilisez la commande suivante. Remplacez `<resource-group-name>` par le nom à utiliser pour ce groupe de ressources. Remplacez `<location>` par la région Azure à utiliser pour ce groupe de ressources :

> [!NOTE]
> Vous devez sélectionner une région où Azure Machine Learning est disponible. Pour plus d’informations, consultez [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

La réponse de cette commande ressemble au JSON suivant. Vous pouvez utiliser les valeurs de sortie pour localiser les ressources créées, ou les analyser comme entrée pour les étapes suivantes d’utilisation de l’interface CLI à des fins d’automatisation.

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Pour plus d’informations sur l’utilisation des groupes de ressources, consultez [az group](/cli/azure/group).

## <a name="create-a-workspace"></a>Créer un espace de travail

Lorsque vous déployez un espace de travail Azure Machine Learning, divers autres services sont [requis en tant que ressources associées dépendantes](./concept-workspace.md#resources). Lorsque vous utilisez l’interface CLI pour créer l’espace de travail, celle-ci peut soit créer pour vous des ressources associées, soit attacher des ressources existantes.

> [!IMPORTANT]
> Lorsque vous attachez votre propre compte de stockage, vérifiez qu’il répond aux critères suivants :
>
> * Le compte de stockage n’est _pas_ un compte Premium (Premium_LRS ou Premium_GRS).
> * Les fonctionnalités Azure Blob et Azure File sont activées.
> * L’espace de noms hiérarchique (ADLS Gen 2) est désactivé. Ces exigences concernent uniquement le compte de stockage _par défaut_ que l’espace de travail utilise.
>
> Lors de l’attachement du registre de conteneurs Azure, le [compte administrateur](../container-registry/container-registry-authentication.md#admin-account) doit être activé être utilisable avec un espace de travail Azure Machine Learning.

# <a name="create-with-new-resources"></a>[Créer avec de nouvelles ressources](#tab/createnewresources)

Pour créer un nouvel espace de travail dans lequel les __services sont créés automatiquement__, utilisez la commande suivante :

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

# <a name="bring-existing-resources-10-cli"></a>[Importer des ressources existantes (CLI 1.0)](#tab/bringexistingresources1)
Pour créer un espace de travail qui utilise des ressources existantes, vous devez fournir l’ID de chaque ressource. Vous pouvez obtenir cet ID via l’onglet « Propriétés » de chaque ressource, via le portail Azure ou en exécutant les commandes suivantes à l’aide d’Azure CLI.

  * **Compte de stockage Azure** :     `az storage account show --name <storage-account-name> --query "id"`
  * **Azure Application Insights** :     `az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"`
  * **Azure Key Vault** :     `az keyvault show --name <key-vault-name> --query "ID"`
  * **Azure Container Registry** :     `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

  Le format de l’ID de ressource renvoyé est le suivant : `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/<provider>/<subresource>/<resource-name>"`.

Une fois que vous disposez des ID des ressources que vous souhaitez utiliser avec l’espace de travail, utilisez la commande `az workspace create -w <workspace-name> -g <resource-group-name>` de base et ajoutez le(s) paramètre(s) et l’ID ou les ID pour les ressources existantes. Par exemple, la commande suivante crée un espace de travail qui utilise un registre de conteneurs existant :

```azurecli-interactive
az ml workspace create -w <workspace-name>
                       -g <resource-group-name>
                       --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

# <a name="bring-existing-resources-cli-v2---preview"></a>[Importer des ressources existantes (CLI (v2) – préversion)](#tab/bringexistingresources2)

Pour créer un nouvel espace de travail tout en important des ressources existantes associées à l’aide de l’interface CLI, vous devez d’abord définir la manière dont votre espace de travail doit être configuré dans un fichier de configuration.

```yaml workspace.yml
name: azureml888
location: EastUS
description: Description of my workspace
storage_account: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>
container_registry: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.ContainerRegistry/registries/<registry-name>
key_vault: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.KeyVault/vaults/<vault-name>
application_insights: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/components/<application-insights-name>
```

Ensuite, vous pouvez référencer ce fichier de configuration dans la commande CLI de création d’espace de travail.

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --file workspace.yml
```

Si vous attachez des ressources existantes, vous devez fournir leur ID. Vous pouvez obtenir cet ID via l’onglet « Propriétés » de chaque ressource dans le portail Azure ou en exécutant les commandes suivantes à l’aide d’Azure CLI.

* **Compte de stockage Azure** :     `az storage account show --name <storage-account-name> --query "id"`
* **Azure Application Insights** :     `az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"`
* **Azure Key Vault** :     `az keyvault show --name <key-vault-name> --query "ID"`
* **Azure Container Registry** :     `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

La valeur d’ID de ressource ressemble à ceci : `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/<provider>/<subresource>/<resource-name>"`.

---

> [!IMPORTANT]
> Lorsque vous attachez des ressources existantes, vous n’avez pas besoin de les spécifier toutes. Vous pouvez en spécifier une ou plusieurs. Par exemple, vous pouvez spécifier un compte de stockage existant et l’espace de travail créera les autres ressources.

La sortie de la commande de création d’espace de travail ressemble au JSON suivant. Vous pouvez utiliser les valeurs de sortie pour localiser les ressources créées ou les analyser comme entrée pour les étapes suivantes d’utilisation de l’interface CLI.

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}

```

## <a name="advanced-configurations"></a>Configurations avancées
### <a name="configure-workspace-for-private-network-connectivity"></a>Configurer l’espace de travail pour la connectivité de réseau privé

Selon le cas d’utilisation et les exigences de votre organisation, vous pouvez choisir de configurer Azure Machine Learning en utilisant une connectivité de réseau privé. Vous pouvez utiliser Azure CLI pour déployer un espace de travail et un point de terminaison de liaison privée pour la ressource d’espace de travail. Pour plus d’informations sur l’utilisation d’un point de terminaison privé et d’un réseau virtuel avec votre espace de travail, consultez [Vue d’ensemble de l’isolement et la confidentialité des réseaux virtuels](how-to-network-security-overview.md). Pour les configurations de ressources complexes, examinez également les options de déploiement basées sur un modèle, dont [Azure Resource Manager](how-to-create-workspace-template.md).

# <a name="10-cli"></a>[CLI 1.0](#tab/vnetpleconfigurationsv1cli)

Si vous souhaitez restreindre l’accès à votre espace de travail à un réseau virtuel, vous pouvez utiliser les paramètres suivants dans la commande `az ml workspace create`, ou utiliser les commandes `az ml workspace private-endpoint`.

```azurecli-interactive
az ml workspace create -w <workspace-name>
                       -g <resource-group-name>
                       --pe-name "<pe name>"
                       --pe-auto-approval "<pe-autoapproval>"
                       --pe-resource-group "<pe name>"
                       --pe-vnet-name "<pe name>"
                       --pe-subnet-name "<pe name>"
```

* `--pe-name`: Nom du point de terminaison privé qui est créé.
* `--pe-auto-approval`: Si les connexions de point de terminaison privé à l’espace de travail doivent être approuvées automatiquement.
* `--pe-resource-group`: Groupe de ressources dans lequel créer le point de terminaison privé. Doit être le même groupe qui contient le réseau virtuel.
* `--pe-vnet-name`: Réseau virtuel existant dans lequel créer le point de terminaison privé.
* `--pe-subnet-name`: Nom du sous-réseau dans lequel créer le point de terminaison privé. La valeur par défaut est `default`.

Pour plus d’informations sur l’utilisation de ces commandes, consultez les [pages de référence de l’interface CLI](/cli/azure/ml(v1)/workspace).

# <a name="cli-v2---preview"></a>[CLI (v2) – préversion](#tab/vnetpleconfigurationsv2cli)

Pour configurer la connectivité de réseau privé pour votre espace de travail à l’aide de l’interface CLI (v2), étendez le fichier de configuration de l’espace de travail de manière à inclure les détails des ressources de point de terminaison de liaison privée.

```yaml workspace.yml
name: azureml888
location: EastUS
description: Description of my workspace
storage_account: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>
container_registry: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.ContainerRegistry/registries/<registry-name>
key_vault: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.KeyVault/vaults/<vault-name>
application_insights: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/components/<application-insights-name>

private_endpoints:
  approval_type: AutoApproval
  connections:
    my-endpt1:
      subscription_id: <subscription-id>
      resource_group: <resourcegroup>
      location: <location>
      vnet_name: <vnet-name>
      subnet_name: <subnet-name>
```

Ensuite, vous pouvez référencer ce fichier de configuration dans la commande CLI de création d’espace de travail.

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --file workspace.yml
```

---

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Clé gérée par le client et espace de travail High Business Impact

Par défaut, les métadonnées de l’espace de travail sont stockées dans une instance d’Azure Cosmos DB gérée par Microsoft. Les données sont chiffrées avec des clés managées par Microsoft. Au lieu d’utiliser la clé gérée par Microsoft, vous pouvez fournir la clé de votre choix. Cela a pour effet de créer un ensemble supplémentaire de ressources dans votre abonnement Azure pour stocker vos données.

Pour en savoir plus sur les ressources créées lorsque vous apportez votre propre clé de chiffrement, consultez [Chiffrement des données avec Azure Machine Learning](./concept-data-encryption.md#azure-cosmos-db).

Les commandes CLI ci-dessous fournissent des exemples pour créer un espace de travail qui utilise des clés gérées par le client pour le chiffrement à l’aide des versions CLI 1.0 et CLI (v2).

# <a name="10-cli"></a>[CLI 1.0](#tab/vnetpleconfigurationsv1cli)

Utilisez le paramètre `--cmk-keyvault` pour spécifier quel Azure Key Vault contient la clé et `--resource-cmk-uri` pour spécifier l’ID de ressource et l’URI de la clé dans le coffre.

Pour [limiter les données que Microsoft collecte](./concept-data-encryption.md#encryption-at-rest) sur votre espace de travail, vous pouvez en outre spécifier le paramètre `--hbi-workspace`. 

```azurecli-interactive
az ml workspace create -w <workspace-name>
                       -g <resource-group-name>
                       --cmk-keyvault "<cmk keyvault name>"
                       --resource-cmk-uri "<resource cmk uri>"
                       --hbi-workspace
```

# <a name="cli-v2---preview"></a>[CLI (v2) – préversion](#tab/vnetpleconfigurationsv2cli)

Utilisez le paramètre `customer_managed_key` contenant les paramètres `key_vault` et `key_uri` pour spécifier l’ID de ressource et l’URI de la clé à l’intérieur du coffre.

Pour [limiter les données que Microsoft collecte](./concept-data-encryption.md#encryption-at-rest) sur votre espace de travail, vous pouvez en outre spécifier la propriété `hbi_workspace`. 

```yaml workspace.yml
name: azureml888
location: EastUS
description: Description of my workspace
storage_account: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>
container_registry: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.ContainerRegistry/registries/<registry-name>
key_vault: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.KeyVault/vaults/<vault-name>
application_insights: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/components/<application-insights-name>

hbi_workspace: true
customer_managed_key:
  key_vault: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers//Microsoft.KeyVault/<vaulttype>/<vaultname>
  key_uri: https://<keyvaultid>.vault.azure.net/keys/<keyname>/<keyversion>

```

Ensuite, vous pouvez référencer ce fichier de configuration dans la commande CLI de création d’espace de travail.

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --file workspace.yml
```
---

> [!NOTE]
> Avec des autorisations de contributeur sur votre abonnement, autorisez l’__application Azure Machine Learning__ (dans Gestion des identités et des accès) à gérer les ressources supplémentaires de chiffrement de données.

> [!NOTE]
> Azure Cosmos DB ne permet __pas__ de stocker des informations, telles que les performances du modèle, les informations consignées par les expériences ou les informations enregistrées à partir de vos modèles de déploiement. Pour plus d’informations sur la surveillance de ces éléments, consultez la section [Surveillance et journalisation](concept-azure-machine-learning-architecture.md) de l’article sur l’architecture et les concepts.

> [!IMPORTANT]
> Sélectionner High Business Impact ne peut être effectué que lors de la création d’un espace de travail. Vous ne pouvez pas modifier ce paramètre une fois l’espace de travail créé.

Pour plus d’informations sur les clés gérées par le client et sur l’espace de travail High Business Impact, consultez [Sécurité Enterprise pour Azure Machine Learning](concept-data-encryption.md#encryption-at-rest).

## <a name="using-the-cli-to-manage-workspaces"></a>Utilisation de l’interface CLI pour gérer les espaces de travail

### <a name="list-workspaces"></a>Répertorier les espaces de travail

Pour afficher la liste de tous les espaces de travail pour votre abonnement Azure, utilisez la commande suivante :

```azurecli-interactive
az ml workspace list
```

Pour plus d’informations, consultez la documentation [az ml workspace list](/cli/azure/ml/workspace#az_ml_workspace_list).

### <a name="get-workspace-information"></a>Obtenir des informations sur l’espace de travail

Pour obtenir des informations sur un espace de travail, utilisez la commande suivante :

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Pour plus d’informations, consultez la documentation [az ml workspace show](/cli/azure/ml/workspace#az_ml_workspace_show).

### <a name="update-a-workspace"></a>Mettre à jour un espace de travail

Utilisez la commande suivante pour mettre à jour un espace de travail :

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Pour plus d’informations, consultez la documentation [az ml workspace update](/cli/azure/ml/workspace#az_ml_workspace_update).

### <a name="sync-keys-for-dependent-resources"></a>Synchroniser les clés pour les ressources dépendantes

Si vous modifiez les clés d’accès pour l’une des ressources utilisées par votre espace de travail, cela prend environ une heure de synchroniser l’espace de travail avec la nouvelle clé. Pour forcer l’espace de travail à synchroniser les nouvelles clés immédiatement, utilisez la commande suivante :

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Pour plus d’informations sur la modification des clés, consultez [Regénérer les clés d’accès au stockage](how-to-change-storage-access-key.md).

Pour plus d’informations sur la commande sync-keys, consultez [az ml workspace sync-keys](/cli/azure/ml/workspace#az_ml_workspace_sync-keys).

### <a name="delete-a-workspace"></a>Supprimer un espace de travail

[!INCLUDE [machine-learning-delete-workspace](../../includes/machine-learning-delete-workspace.md)]

Pour supprimer un espace de travail une fois qu’il n’est plus nécessaire, utilisez la commande suivante :

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> La suppression d’un espace de travail ne supprime pas le service Application Insight, le compte de stockage, le coffre de clés ou le registre de conteneurs utilisé par l’espace de travail.

Vous pouvez également supprimer le groupe de ressources, ce qui supprime l’espace de travail et toutes les autres ressources Azure dans le groupe de ressources. Pour supprimer le groupe de ressources, utilisez la commande suivante :

```azurecli-interactive
az group delete -g <resource-group-name>
```

Pour plus d’informations, consultez la documentation [az ml workspace delete](/cli/azure/ml/workspace#az_ml_workspace_delete).

Si vous avez supprimé accidentellement votre espace de travail, vous pouvez toujours récupérer vos blocs-notes. Reportez-vous à [cette documentation](/azure/machine-learning/how-to-high-availability-machine-learning#workspace-deletion).

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

Pour plus d’informations sur l’extension Azure CLI pour Machine Learning, consultez la documentation [az ml](/cli/azure/ml).
