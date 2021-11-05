---
title: Plateformes prises en charge par Microsoft Defender pour le cloud | Microsoft Docs
description: Ce document fournit la liste des plateformes prises en charge par Microsoft Defender pour le cloud.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 4cf49562119503e7b0c54cb0691cbb520c6a0f9e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428346"
---
# <a name="supported-platforms"></a>Plateformes prises en charge 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cette page liste les plateformes et les environnements pris en charge par Microsoft Defender pour le cloud.

## <a name="combinations-of-environments"></a>Combinaisons d’environnements <a name="vm-server"></a>

Microsoft Defender pour le cloud prend en charge des machines virtuelles et des serveurs sur différents types d’environnements hybrides :

* Azure uniquement
* Azure et en local
* Azure et autres clouds
* Azure, autres clouds et en local

Dans le cas d’un environnement Azure activé dans un abonnement Azure, Microsoft Defender pour le cloud détecte automatiquement les ressources IaaS qui sont déployées dans l’abonnement.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Defender pour le cloud dépend de l’[agent Log Analytics](../azure-monitor/agents/agents-overview.md#log-analytics-agent). Vérifiez que vos machines exécutent l’un des systèmes d’exploitation pris en charge pour cet agent, comme décrit dans les pages suivantes :

* [Agent Log Analytics pour les systèmes d’exploitation pris en charge par Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)
* [Agent Log Analytics pour les systèmes d’exploitation pris en charge par Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

Vérifiez également que votre agent Log Analytics est [correctement configuré pour envoyer des données à Defender pour le cloud](enable-data-collection.md#manual-agent).

Pour en savoir plus sur les fonctionnalités de Defender pour le cloud qui sont disponibles sur Windows et Linux, consultez [Couverture des fonctionnalités pour les machines](supported-machines-endpoint-solutions-clouds.md).

> [!NOTE]
> Même si **Microsoft Defender pour les serveurs** est conçu pour protéger les serveurs, la plupart d’entre eux sont pris en charge pour les ordinateurs Windows 10. La fonctionnalité [Solution EDR intégrée de Defender pour le cloud : Microsoft Defender pour point de terminaison](integration-defender-for-endpoint.md) n’est pas prise en charge actuellement.

## <a name="managed-virtual-machine-services"></a>Services de machine virtuelle managés <a name="virtual-machine"></a>

Des machines virtuelles sont également créées dans un abonnement client dans le cadre de certains services gérés par Azure, comme Azure Kubernetes (AKS) et Azure Databricks, entre autres. Defender pour le cloud découvre également ces machines virtuelles, et l’agent Log Analytics peut être installé et configuré si un système d’exploitation pris en charge est disponible.

## <a name="cloud-services"></a>Services cloud <a name="cloud-services"></a>

Les machines virtuelles s’exécutant dans un service cloud sont également prises en charge. Seuls les rôles de travail et web des services cloud en cours d’exécution dans des emplacements de production sont surveillés. Pour en savoir plus sur les services cloud, consultez [Vue d’ensemble d’Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

La protection des machines virtuelles résidant dans Azure Stack Hub est également prise en charge. Pour plus d’informations sur l’intégration de Defender pour le cloud à Azure Stack Hub, consultez [Intégrer vos machines virtuelles Azure Stack Hub à Defender pour le cloud](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms). 

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Defender pour le cloud collecte des données avec l’agent Log Analytics](enable-data-collection.md).
- Découvrez comment [Defender pour cloud gère et protège les données](data-security.md).
- Découvrez comment [planifier l’adoption de Microsoft Defender pour le cloud en prenant connaissance des considérations relatives à la conception](security-center-planning-and-operations-guide.md).