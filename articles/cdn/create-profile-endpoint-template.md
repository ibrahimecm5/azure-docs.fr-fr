---
title: 'Démarrage rapide : Créer un profil et un point de terminaison - Modèle Resource Manager'
titleSuffix: Azure Content Delivery Network
description: Dans ce guide de démarrage rapide, découvrez comment créer un profil et un point de terminaison Azure Content Delivery Network à l’aide d’un modèle Resource Manager
services: cdn
author: duongau
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 05/10/2021
ms.author: duau
ms.openlocfilehash: d0f160a068e8f9205a9659e34de6033b6690f520
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426351"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>Démarrage rapide : Créer un profil et un point de terminaison Azure CDN - Modèle ARM

Pour commencer avec Azure Content Delivery Network (CDN), utilisez un modèle ARM (Azure Resource Manager). Le modèle déploie un profil et un point de terminaison.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.cdn%2Fcdn-with-custom-origin%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/cdn-with-custom-origin/).

Ce modèle est configuré pour créer les éléments suivants :

* Profil
* Point de terminaison

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.cdn/cdn-with-custom-origin/azuredeploy.json":::

Une seule ressource Azure est définie dans le modèle :

* **[Microsoft.Cdn/profiles](/azure/templates/microsoft.cdn/profiles)**

## <a name="deploy-the-template"></a>Déployer le modèle

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.cdn/cdn-with-custom-origin/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.cdn/cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

### <a name="portal"></a>Portail

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.cdn%2Fcdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Sélectionnez **Groupes de ressources** dans le volet gauche.

3. Sélectionnez le groupe de ressources que vous avez créé dans la section précédente. Le nom du groupe de ressources par défaut est **myResourceGroupCDN**

4. Vérifiez que les ressources suivantes ont été créées dans le groupe de ressources :

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Groupe de ressources Azure CDN" border="true":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

### <a name="azure-cli"></a>Azure CLI

Quand vous n’en avez plus besoin, utilisez la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources et toutes les ressources qu’il contient.

```azurecli-interactive
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources et toutes les ressources qu’il contient.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>Portail

Quand vous n’en avez plus besoin, supprimez le groupe de ressources, le profil CDN, ainsi que toutes les ressources associées. Sélectionnez le groupe de ressources **myResourceGroupCDN** qui contient le point de terminaison et le profil CDN, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce guide de démarrage rapide, vous avez créé les éléments suivants :

* Profil CDN
* Point de terminaison

Pour plus d’informations sur Azure CDN et Azure Resource Manager, passez aux articles ci-dessous.

> [!div class="nextstepaction"]
> [Tutoriel : Utiliser CDN pour délivrer du contenu statique à partir d’une application web](cdn-add-to-web-app.md)
