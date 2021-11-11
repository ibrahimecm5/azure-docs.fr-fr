---
title: Configurer l’intégration du réseau virtuel avec un routage des applications.
description: Cet article de guide pratique montrer comment configurer un routage d’applications sur une intégration de réseau virtuel régional.
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: d286b8c0bb82c1501cdc77e8097ba6b7d5b2008f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270697"
---
# <a name="manage-azure-app-service-virtual-network-integration-routing"></a>Gérer le routage d’intégration de réseau virtuel Azure App Service

Lorsque vous configurez le routage d’applications, vous pouvez acheminer tout le trafic ou seulement le trafic privé (également appelé trafic [RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3)) dans votre réseau virtuel Azure. Cet article explique comment configurer un routage d’applications.

## <a name="prerequisites"></a>Prérequis

Votre application est déjà intégrée à l’aide de la fonctionnalité d’intégration de réseau virtuel régional.

## <a name="configure-in-the-azure-portal"></a>Configurer dans le portail Azure

Vous pouvez suivre les étapes suivantes pour désactiver Tout acheminer dans votre application via le portail : 

:::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-enabled.png" alt-text="Tout acheminer activé":::

1. Accédez à l’interface utilisateur **Mise en réseau** > **Intégration de réseau virtuel** dans votre portail d’application.
1. Définissez **Tout acheminer** sur Désactivé.
    
    :::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-disabling.png" alt-text="Désactiver Tout acheminer":::

1. Sélectionnez **Oui** pour confirmer.

## <a name="configure-with-azure-cli"></a>Configurer avec Azure CLI

Vous pouvez également configurer Tout acheminer à l’aide d’Azure CLI (l’`az version` minimale requise est 2.27.0) :

```azurecli-interactive
az webapp config set --resource-group <group-name> --name <app-name> --vnet-route-all-enabled [true|false]
```

## <a name="configure-with-azure-powershell"></a>Configurer avec Azure PowerShell

```azurepowershell
# Parameters
$siteName = '<app-name>'
$resourceGroupName = '<group-name>'

# Configure VNet Integration
$webApp = Get-AzResource -ResourceType Microsoft.Web/sites -ResourceGroupName $resourceGroupName -ResourceName $siteName
Set-AzResource -ResourceId ($webApp.Id + "/config/web") -Properties @{ vnetRouteAllEnabled = $true } -Force
```

## <a name="next-steps"></a>Étapes suivantes

- [Activer l’intégration au réseau virtuel](./configure-vnet-integration-enable.md)
- [Vue d’ensemble du réseau général](./networking-features.md)