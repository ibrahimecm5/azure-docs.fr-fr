---
title: 'Démarrage rapide : Créer un compte Purview avec PowerShell/Azure CLI'
description: Ce guide de démarrage rapide détaille la création d’un compte Azure Purview avec Azure PowerShell ou Azure CLI.
author: hophanms
ms.author: hophan
ms.date: 10/28/2021
ms.topic: quickstart
ms.service: purview
ms.custom: mode-api, devx-track-azurepowershell
ms.openlocfilehash: ac06f6641f60ec1775e161ec1e949522321fd5af
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990194"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Démarrage rapide : Créer un compte Azure Purview en utilisant Azure PowerShell ou Azure CLI

Dans ce guide de démarrage rapide, vous allez créer un compte Azure Purview en utilisant Azure PowerShell ou l’interface Azure CLI. La [référence PowerShell pour Purview](/powershell/module/az.purview/) est disponible, mais cet article vous guide dans les étapes à suivre pour créer un compte avec PowerShell.

Azure Purview est un service de gouvernance de données qui vous aide à gérer et régir votre paysage de données. En vous connectant aux données de vos sources locales, multiclouds et SaaS (software-as-a-service), Purview crée un mappage à jour de vos informations. Il identifie et classe les données sensibles, et fournit un lignage de bout en bout. Les consommateurs de données peuvent découvrir des données au sein de votre organisation, et les administrateurs de données sont en mesure d’auditer, de sécuriser et de garantir l’utilisation appropriée de vos données.

Pour plus d’informations sur Purview, [consultez notre page de présentation](overview.md). Pour plus d’informations sur le déploiement de Purview dans votre organisation, [consultez nos bonnes pratiques de déploiement](deployment-best-practices.md).

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

### <a name="install-powershell"></a>Installer PowerShell

 Installez Azure PowerShell ou Azure CLI dans votre machine cliente pour déployer le modèle : [Déploiement en ligne de commande](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="create-an-azure-purview-account"></a>Créer un compte Azure Purview

1. Se connecter avec des informations d’identification Azure

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    Connect-AzAccount
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az login
    ```

    ---

1. Si vous avez plusieurs abonnements Azure, sélectionnez celui que vous souhaitez utiliser :

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```

    ---

1. Créez un groupe de ressources pour votre compte Purview. Vous pouvez ignorer cette étape si vous en avez déjà un :

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name myResourceGroup -Location 'East US'
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```

    ---

1. Créer ou déployer le compte Purview

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    Utilisez l’applet de commande [New-AzPurviewAccount](/powershell/module/az.purview/new-azpurviewaccount) pour créer le compte Purview :

    ```azurepowershell
    New-AzPurviewAccount -Name yourPurviewAccountName -ResourceGroupName myResourceGroup -Location eastus -IdentityType SystemAssigned -SkuCapacity 4 -SkuName Standard -PublicNetworkAccess
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    1. Créez un fichier de modèle Purview, tel que `purviewtemplate.json`. Vous pouvez mettre à jour `name`, `location` et `capacity` (`4` ou `16`) :

        ```json
        {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "name": "<yourPurviewAccountName>",
              "type": "Microsoft.Purview/accounts",
              "apiVersion": "2020-12-01-preview",
              "location": "EastUs",
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "networkAcls": {
                  "defaultAction": "Allow"
                }
              },
              "dependsOn": [],
              "sku": {
                "name": "Standard",
                "capacity": "4"
              },
              "tags": {}
            }
          ],
          "outputs": {}
        }
        ```

    1. Déployer le modèle Purview

        Pour exécuter cette commande de déploiement, vous devez disposer de la [dernière version](/cli/azure/install-azure-cli) d’Azure CLI.

        ```azurecli
        az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
        ```

    ---

1. La commande de déploiement retourne des résultats. Recherchez `ProvisioningState` pour voir si le déploiement s’est déroulé correctement.

1. Si vous avez déployé le compte Azure Purview avec un principal de service au lieu d’un compte d’utilisateur, vous devez également exécuter la commande ci-dessous dans Azure CLI :

    ```azurecli
    az purview account add-root-collection-admin --account-name --resource-group [--object-id]
    ```

    Cette commande accorde au compte d’utilisateur des autorisations d’[administrateur de collection](catalog-permissions.md#roles) sur la collection racine de votre compte Azure Purview. L’utilisateur peut alors accéder à Purview Studio et ajouter des autorisations pour d’autres utilisateurs. Pour plus d’informations sur les autorisations dans Azure Purview, consultez notre [guide des autorisations](catalog-permissions.md). Pour plus d’informations sur les collections, consultez notre [article sur la gestion des collections](how-to-create-and-manage-collections.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez vu comment créer un compte Azure Purview.

Lisez les articles suivants pour apprendre à naviguer dans Purview Studio, à créer une collection et à accorder l’accès à Purview.

* [Comment utiliser Purview Studio](use-purview-studio.md)
* [Ajouter des utilisateurs à votre compte Azure Purview](catalog-permissions.md)
* [Création d'une collection](quickstart-create-collection.md)
