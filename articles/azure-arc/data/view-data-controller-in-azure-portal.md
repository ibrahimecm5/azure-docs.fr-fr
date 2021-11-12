---
title: Afficher la ressource de contrôleur de données Azure Arc dans le portail Azure
description: Afficher la ressource de contrôleur de données Azure Arc dans le portail Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: fe594e7459f32d0639c39b9f694f31e7816ca118
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558741"
---
# <a name="view-azure-arc-data-controller-resource-in-azure-portal"></a>Afficher la ressource de contrôleur de données Azure Arc dans le portail Azure

Pour afficher le contrôleur de données Azure Arc dans votre portail Azure, vous devez exporter les données d’utilisation, les métriques ou les journaux de votre cluster Kubernetes et les charger sur Azure. 

## <a name="direct-connected-mode"></a>Mode de connexion directe
Si le contrôleur de données Azure Arc est déployé en mode **connexion directe**, les données d’utilisation sont automatiquement chargées dans Azure, et les ressources Kubernetes sont projetées dans Azure.

## <a name="indirect-connected-mode"></a>Mode de connexion indirecte
En mode **connexion indirecte**, vous devez exporter et charger les données d’utilisation, les métriques ou les journaux vers Azure, comme décrit dans [Charger les données d’utilisation, les métriques et les journaux sur Azure](upload-metrics-and-logs-to-azure-monitor.md). Cette action crée les ressources appropriées dans Azure.

## <a name="azure-portal"></a>Portail Azure

Une fois que vous avez terminé votre premier [chargement de métriques ou journaux dans Azure](upload-metrics-and-logs-to-azure-monitor.md) ou que vous avez [chargé les données d’utilisation](view-billing-data-in-azure.md), vous pouvez voir le contrôleur de données Azure Arc et les instances managées SQL avec Azure Arc ou les ressources serveur Postgres Hyperscale dans le portail Azure.

Ouvrez le portail Azure à l’URL : [https://portal.azure.com](https://portal.azure.com).

Recherchez votre contrôleur de données d’après son nom dans la barre de recherche, puis cliquez sur celui-ci.

