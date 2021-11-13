---
title: Activez l’intégration au réseau virtuel Azure.
description: Cet article pratique vous guide tout au long de l’activation de l’intégration d’un réseau virtuel sur une application web App Service
keywords: Intégration au réseau virtuel
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: 549c92b932e0d589aec2490512fc18713fa71546
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270756"
---
# <a name="enable-virtual-network-integration-in-azure-app-service"></a>Activer l’intégration au réseau virtuel dans Azure App Service

En intégrant votre [application App Service](./overview.md) à un réseau virtuel (VNet) Azure, vous pouvez accéder à des ressources privées à partir de votre application dans le réseau virtuel. La fonctionnalité d’intégration au réseau virtuel présente deux variantes :

* Intégration au réseau virtuel régional : se connecter aux réseaux virtuels Azure dans la même région. Vous devez avoir un sous-réseau dédié dans le VNet que vous intégrez.
* Intégration au réseau virtuel avec passerelle : quand vous vous connectez directement à un VNet dans d’autres régions ou à un réseau virtuel classique dans la même région, vous devez utiliser l’intégration au réseau virtuel avec passerelle.

Cet article pratique décrit la configuration de l’intégration au réseau virtuel régional.

## <a name="prerequisites"></a>Prérequis

L’intégration au réseau virtuel nécessite :
- Un niveau tarifaire App Service [prenant en charge l’intégration au réseau virtuel](./overview-vnet-integration.md).
- Un réseau virtuel dans la même région avec un sous-réseau vide.

Le sous-réseau doit être délégué à Microsoft.Web/serverFarms. Si la délégation n’est pas effectuée avant l’intégration, le processus de provisionnement configure cette délégation. Le sous-réseau doit avoir un bloc IPv4 `/28` (16 adresses). Nous vous recommandons d’avoir un minimum de 64 adresses (bloc IPv4 `/26`) pour permettre une échelle horizontale maximale.

## <a name="configure-in-the-azure-portal"></a>Configurer dans le portail Azure

1. Accédez à l’interface utilisateur **Réseau** dans le portail App Service. Sous **Trafic sortant**, sélectionnez **Intégration au réseau virtuel**.

1. Sélectionnez **Ajouter un réseau virtuel**.

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-app.png" alt-text="Sélectionner l’intégration au réseau virtuel":::

1. La liste déroulante contient tous les réseaux virtuels de votre abonnement dans la même région.

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-add-vnet.png" alt-text="Sélectionner le réseau virtuel":::

    * Sélectionnez un sous-réseau vide existant ou créez en un.

Lors de l’intégration, votre application est redémarrée. Une fois l’intégration terminée, vous verrez des informations sur le réseau virtuel auquel vous êtes intégré.

## <a name="configure-with-azure-cli"></a>Configurer avec Azure CLI

Vous pouvez également configurer l’intégration au réseau virtuel en utilisant Azure CLI :

```azurecli-interactive
az webapp vnet-integration add --resource-group <group-name> --name <app-name> --vnet <vnet-name> --subnet <subnet-name>
```

> [!NOTE]
> La commande vérifie que le sous-réseau est délégué à Microsoft.Web/serverFarms et applique la délégation nécessaire si cela n’est pas configuré. Si cela a déjà été configuré et que vous n’avez pas les autorisations nécessaires pour le vérifier, ou si le réseau virtuel est dans un autre abonnement, vous pouvez utiliser le paramètre `--skip-delegation-check` pour contourner la validation.

## <a name="configure-with-azure-powershell"></a>Configurer avec Azure PowerShell

```azurepowershell
# Parameters
$siteName = '<app-name>'
$resourceGroupName = '<group-name>'
$vNetName = '<vnet-name>'
$integrationSubnetName = '<subnet-name>'
$subscriptionId = '<subscription-guid>'

# Configure VNet Integration
$subnetResourceId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Network/virtualNetworks/$vNetName/subnets/$integrationSubnetName"
$webApp = Get-AzResource -ResourceType Microsoft.Web/sites -ResourceGroupName $resourceGroupName -ResourceName $siteName
$webApp.Properties.virtualNetworkSubnetId = $subnetResourceId
$webApp | Set-AzResource -Force
```

## <a name="next-steps"></a>Étapes suivantes

- [Configurer le routage d’intégration au réseau virtuel](./configure-vnet-integration-routing.md)
- [Vue d’ensemble du réseau général](./networking-features.md)