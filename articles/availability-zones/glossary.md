---
title: Terminologie de la résilience Azure
description: Présentation des termes
author: mamccrea
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: mamccrea
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: ade8db49435064c31ec3c1262f359069641a11dd
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057002"
---
# <a name="terminology"></a>Terminologie

Pour mieux comprendre les régions et les zones de disponibilité dans Azure, il est utile de comprendre les principaux termes ou concepts.

| Terme ou concept | Description |
| --- | --- |
| region | Un ensemble de centres de données déployés dans un périmètre avec une latence définie et connectés via un réseau régional dédié à faible latence. |
| Geography | Une zone du monde contenant au moins une région Azure. Les zones géographiques définissent un marché discret qui préserve la résidence des données et les limites de conformité. Les zones géographiques permettent aux clients ayant des besoins spécifiques en conformité et résidence des données de conserver leurs données et leurs applications à proximité. Les zones géographiques sont tolérantes aux pannes et peuvent résister à une défaillance complète de la région, car elles sont connectées à notre infrastructure réseau haute capacité dédiée. |
| zone de disponibilité | Emplacements physiques uniques dans une région. Chaque zone de disponibilité est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. |
| région recommandée | Une région qui fournit le plus grand nombre de fonctionnalités de service et est conçue pour prendre en charge les zones de disponibilité maintenant, ou à l’avenir. Ces régions sont désignées dans le portail Azure avec le libellé **Recommandé**. |
| région alternative (ou autre région) | Une région qui étend l’empreinte d’Azure au sein d’une limite de résidence des données où une région recommandée existe également. Les autres régions vous aident à optimiser la latence et à fournir une deuxième région pour les besoins de récupération d’urgence. Elles ne sont pas conçues pour prendre en charge les zones de disponibilité, bien qu’Azure effectue une évaluation régulière de ces régions pour déterminer si elles doivent devenir des régions recommandées. Ces régions sont désignées dans le portail Azure avec le libellé **Autre**. |
| réplication inter-région (anciennement région jumelée) | Une stratégie de fiabilité et son implémentation qui associent la haute disponibilité des zones de disponibilité à la protection contre les incidents à l’échelle d’une région pour répondre aux besoins de continuité d’activité et de reprise d’activité. |
| Services fondamentaux | Un service Azure principal disponible dans toutes les régions lorsque la région est en disponibilité générale. |
| Service standard | Un service Azure disponible dans toutes les régions recommandées dans un délai de 90 jours suivant la disponibilité générale de la région ou la disponibilité basée sur la demande dans d’autres régions. |
| service stratégique | Un service Azure offrant une disponibilité basée sur la demande dans les régions soutenues par du matériel personnalisé/spécialisé. |
| Service régional | Un service Azure qui est déployé de manière régionale et permet au client de spécifier la région dans laquelle le service sera déployé. Pour avoir une liste complète, voir [Régions disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| Service non régional | Un service Azure pour lequel il n’existe aucune dépendance vis-à-vis d’une région Azure spécifique. Les services non régionaux sont déployés dans plusieurs régions. En cas de défaillance au niveau d’une région, l’instance du service dans une autre région continue de servir les clients. Pour avoir une liste complète, voir [Régions disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| service zonal | Un service Azure qui prend en charge les zones de disponibilité et permet le déploiement d’une ressource sur une zone de disponibilité spécifique, autosélectionnée pour atteindre des exigences de latence ou de performance plus strictes. |
| service redondant interzone | Un service Azure qui prend en charge les zones de disponibilité et qui permet aux ressources d’être répliquées ou distribuées automatiquement entre les zones. |
| service toujours disponible | Un service Azure qui prend en charge les zones de disponibilité et qui permet aux ressources d’être toujours disponibles dans toutes les zones géographiques Azure, et résilientes aux pannes à l’échelle de la zone et de la région. |
