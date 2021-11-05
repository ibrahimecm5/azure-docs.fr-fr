---
title: Types de ressources et attributions de rôles pris en charge pour Chaos Studio
description: Comprenez la liste des types de ressources pris en charge, et l’attribution de rôle nécessaire pour permettre à une expérience d’exécuter une erreur sur ce type de ressource.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 10bb9b826d5c749b283dd38caa77ae03b7695b8e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096919"
---
# <a name="supported-resource-types-and-role-assignments-for-chaos-studio"></a>Types de ressources et attributions de rôles pris en charge pour Chaos Studio

Voici les types de ressources pris en charge pour les erreurs, les types de cibles et les rôles suggérés à utiliser lors de l’octroi d’une autorisation d’expérience à une ressource de ce type.

| Type de ressource | Nom de la cible | Attribution de rôle suggéré |
| - | - | - |
| Microsoft.Compute/virtualMachines (basé sur agent) | Microsoft-Agent | Lecteur |
| Microsoft.Compute/virtualMachineScaleSets (basé sur agent) | Microsoft-Agent | Lecteur |
| Microsoft.Compute/virtualMachines (service direct) | Microsoft-VirtualMachine | Contributeur de machine virtuelle |
| Microsoft.Compute/virtualMachineScaleSets (service direct) | Microsoft-VirtualMachineScaleSet | Contributeur de machine virtuelle |
| Microsoft.DocumentDb/databaseAccounts (CosmosDB, service direct) | Microsoft-CosmosDB | Opérateur Cosmos DB |
| Microsoft.ContainerService/managedClusters (service direct) | Microsoft-AzureKubernetesServiceChaosMesh | Rôle d’utilisateur de cluster Azure Kubernetes Service |
| Microsoft.Network/networkSecurityGroups (service direct) | Microsoft-NetworkSecurityGroup | Contributeur de réseau |
| Microsoft.Cache/Redis (service direct) | Microsoft-AzureCacheForRedis | Contributeur Cache Redis |
