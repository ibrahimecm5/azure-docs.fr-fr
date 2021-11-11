---
title: Afficher l’inventaire de vos instances dans le portail Azure
description: Afficher l’inventaire de vos instances dans le portail Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 5a5f13c74799e172d7ce9aefb170626cf61eb98e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558779"
---
# <a name="inventory-of-arc-enabled-data-services"></a>Inventaire des services de données avec Arc


## <a name="view-resources-in-azure-portal"></a>Afficher les ressources dans le portail Azure

Après avoir chargé vos [métriques, vos journaux](upload-metrics-and-logs-to-azure-monitor.md) ou votre [utilisation](view-billing-data-in-azure.md), vous pouvez afficher vos instances managées SQL ou groupes de serveurs PostgreSQL Hyperscale avec Azure Arc dans le portail Azure. Pour afficher votre ressource dans le [portail](https://portal.azure.com), procédez comme suit :

1. Accédez à **Tous les services**.
1. Recherchez votre type d’instance de base de données.
1. Ajoutez le type à vos favoris.
1. Dans le menu de gauche, sélectionnez le type d’instance.
1. Afficher vos instances dans la même vue que vos autres ressources Azure SQL ou Azure PostgreSQL hyperscale (utilisez des filtres pour obtenir une vue granulaire).

## <a name="view-resources-in-your-kubernetes-cluster"></a>Afficher les ressources de votre cluster Kubernetes

Si le contrôleur de données Azure Arc est déployé en mode de connectivité **indirecte**, vous pouvez exécuter la commande ci-dessous pour obtenir la liste de toutes les instances managées SQL Azure Arc :
```
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
#Example
az sql mi-arc list --k8s-namespace arc --use-k8s
```

Si le contrôleur de données Azure Arc est déployé en mode de connectivité **indirecte**, vous pouvez exécuter la commande ci-dessous pour obtenir la liste de toutes les instances managées SQL Azure Arc :
```
az sql mi-arc list --resource-group <resourcegroup>
#Example
az sql mi-arc list --resource-group myResourceGroup
```
