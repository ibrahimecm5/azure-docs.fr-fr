---
title: Limitations de PostgreSQL Hyperscale avec Azure Arc
description: Limitations de PostgreSQL Hyperscale avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: a2a63e62598b291388375e0eb520d390dbdc4278
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562824"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Limitations de PostgreSQL Hyperscale avec Azure Arc

Cet article décrit les limitations de PostgreSQL Hyperscale avec Azure Arc. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>Sauvegarde et restauration
Les fonctionnalités de sauvegarde/restauration ont été temporairement supprimées le temps de finaliser les conceptions et les expériences.

## <a name="high-availability"></a>Haute disponibilité
La configuration de la haute disponibilité et la vérification du basculement en cas de défaillance de l’infrastructure ne sont pas encore disponible.

## <a name="databases"></a>Bases de données
L’hébergement de plusieurs bases de données dans un groupe de serveurs n’est pas pris en charge si vous avez effectué un scale-out du déploiement sur plusieurs nœuds Worker.

## <a name="roles-and-responsibilities"></a>Les rôles et responsabilités

Les rôles et les responsabilités entre Microsoft et ses clients diffèrent entre les services gérés par Azure (Platform as a Service ou PaaS) et Azure Hybrid (comme PostgreSQL Hyperscale avec Azure Arc). 

### <a name="frequently-asked-questions"></a>Forum aux questions
Le tableau ci-dessous récapitule les réponses aux questions fréquemment posées sur les rôles et responsabilités de support.

| Question                      | Azure Platform As A Service (PaaS) | Services Azure Arc hybrides |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| Qui fournit l’infrastructure ?  | Microsoft                          | Customer                  |
| Qui fournit le logiciel ?*       | Microsoft                          | Microsoft                 |
| Qui effectue les opérations ? | Microsoft                          | Customer                  |
| Est-ce que Microsoft fournit des contrats SLA ?      | Oui                                | Non                        |
| Qui est responsable des contrats SLA ? | Microsoft                          | Customer                  |

\* Services Azure

__Pourquoi Microsoft ne fournit pas de contrats SLA sur les services Azure Arc hybrides ?__ Microsoft n’est pas propriétaire de l’infrastructure et ne l’exploite pas, contrairement aux clients.

## <a name="next-steps"></a>Étapes suivantes

- **Faites un essai.** Démarrez rapidement avec [Démarrage rapide d’Azure Arc](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) sur Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou sur une machine virtuelle Azure. 

- **Créez votre propre cluster.** Pour créer sur votre propre cluster Kubernetes, procédez comme suit : 
   1. [Installer les outils clients](install-client-tools.md)
   2. [Créer le contrôleur de données Azure Arc](create-data-controller.md)
   3. [Créer un groupe de serveurs Azure Database pour PostgreSQL Hyperscale sur Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [En savoir plus sur les services de données avec Azure Arc](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Découvrir Azure Arc](https://aka.ms/azurearc)
