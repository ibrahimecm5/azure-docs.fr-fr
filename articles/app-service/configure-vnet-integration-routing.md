---
title: Configurer l’intégration du réseau virtuel avec un routage des applications.
description: Cet article pratique vous guide tout au long de la configuration du routage d’applications sur une intégration de réseau virtuel régional.
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: b62a8c4bcef5e5556820f6f4e816f01566d44f72
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892037"
---
# <a name="manage-azure-app-service-virtual-network-integration-routing"></a>Gérer le routage d’intégration de réseau virtuel Azure App Service

Lorsque vous configurez le routage d’applications, vous pouvez acheminer tout le trafic ou seulement le trafic privé (également appelé trafic [RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3)) dans votre réseau virtuel Azure. Cet article explique comment configurer le routage d’applications.

## <a name="prerequisites"></a>Prérequis

Votre application est déjà intégrée à l’aide de la fonctionnalité d’intégration de réseau virtuel régional.

## <a name="configure-in-the-azure-portal"></a>Configurer dans le portail Azure

Suivez les étapes ci-dessous pour désactiver **Tout acheminer** dans votre application via le portail.

:::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-enabled.png" alt-text="Capture d’écran montrant l’activation de Tout acheminer":::

1. Accédez à **Mise en réseau** > **Intégration de réseau virtuel** dans votre portail d’application.
1. Définissez **Tout acheminer** sur **Désactivé**.
    
    :::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-disabling.png" alt-text="Capture d’écran montrant la désactivation de Tout acheminer":::

1. Sélectionnez **Oui** pour confirmer.

## <a name="configure-with-the-azure-cli"></a>Configurer avec Azure CLI

Vous pouvez également configurer **Tout acheminer** à l’aide d’Azure CLI. La version 2.27.0 est la version az minimale requise.

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

- [Activer l’intégration du réseau virtuel Azure](./configure-vnet-integration-enable.md)
- [Vue d’ensemble de la mise en réseau générale](./networking-features.md)