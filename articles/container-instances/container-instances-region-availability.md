---
title: Disponibilité des ressources par région
description: Disponibilité des ressources de calcul et de mémoire pour le service Azure Container Instances dans différentes régions Azure.
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: 13cdd53d345ed983fa4954662d3bb5905ccafbe3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843762"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilité des ressources pour Azure Container Instances dans les régions Azure

Cet article décrit en détail la disponibilité des ressources de calcul, de mémoire et de stockage d’Azure Container Instances dans les régions Azure et du système d’exploitation cible. Pour obtenir la liste générale des régions disponibles pour Azure Container Instances, consultez [Régions disponibles](https://azure.microsoft.com/regions/services/).

Les valeurs présentées sont les ressources maximales disponibles par déploiement d’un [groupe de conteneurs](container-instances-container-groups.md). Les valeurs sont à jour au moment de la publication.

> [!NOTE]
> Les groupes de conteneurs créés dans les limites de ces ressources sont soumis à la disponibilité dans la région de déploiement. Quand une région a une charge importante, vous pouvez rencontrer un échec durant le déploiement des instances. Pour atténuer ce type d'échec de déploiement, essayez de déployer des instances comportant des paramètres de ressources inférieurs, ou bien retentez le déploiement ultérieurement ou dans une autre région avec les ressources disponibles.

Pour plus d’informations sur les quotas et autres limites de vos déploiements, voir [Quotas et limites pour Azure Container Instances](container-instances-quotas.md).

## <a name="linux-container-groups"></a>Groupes de conteneurs Linux

Les régions et les ressources maximales suivantes sont disponibles pour les groupes de conteneurs avec des conteneurs Linux dans des déploiements généraux, déploiements de [réseau virtuel Azure](container-instances-vnet.md) et déploiements avec des [ressources GPU](container-instances-gpu.md) (préversion).

> [!IMPORTANT]
> Le nombre maximal de ressources dans une région est différent selon votre déploiement. Par exemple, une région peut avoir une taille de processeur et de mémoire maximale différente dans un déploiement de réseau virtuel Azure par rapport à un déploiement général. Cette même région peut également avoir un ensemble différent de valeurs maximales pour un déploiement avec des ressources GPU. Vérifiez votre type de déploiement avant de consulter les tableaux ci-dessous pour obtenir les valeurs maximales de votre région.

> [!NOTE]
> Certaines régions ne prennent pas en charge les zones de disponibilité (signalé par la mention « N/A » dans le tableau ci-dessous), et certaines régions ont des zones de disponibilité, mais ACI ne tire pas actuellement parti de la capacité (indiqué par un « N » dans le tableau ci-dessous). Pour plus d’informations, consultez [Régions Azure disposant de zones de disponibilité][az-region-support].

| Région | Utilisation maximale du processeur | Mémoire max. (GB) | Utilisation maximale du processeur du réseau virtuel | Mémoire maximale du réseau virtuel (Go) | Stockage (Go) | Références SKU de GPU (préversion) | Prise en charge des zones de disponibilité |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: | :----: |
| Australie Est | 4 | 16 | 4 | 16 | 50 | N/A | O |
| Sud-Australie Est | 4 | 14 | N/A | N/A | 50 | N/A | N |
| Brésil Sud | 4 | 16 | 2 | 8 | 50 | N/A | O |
| Centre du Canada | 4 | 16 | 4 | 16 | 50 | N/A | N |
| Est du Canada | 4 | 16 | 4 | 16 | 50 | N/A | N |
| Inde centrale | 4 | 16 | 4 | 4 | 50 | V100 | N |
| USA Centre | 4 | 16 | 4 | 16 | 50 | N/A | O |
| Asie Est | 4 | 16 | 4 | 16 | 50 | N/A | N |
| USA Est | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 | O |
| USA Est 2 | 4 | 16 | 4 | 16 | 50 | N/A | O |
| France Centre | 4 | 16 | 4 | 16 | 50 | N/A | O|
| Allemagne Centre-Ouest | 4 | 16 | N/A | N/A | 50 | N/A | O |
| Japon Est | 2 | 8 | 4 | 16 | 50 | N/A | O |
| OuJapon Est | 4 | 16 | N/A | N/A | 50 | N/A | N |
| Centre de la Corée | 4 | 16 | N/A | N/A | 50 | N/A | N |
| Centre-Nord des États-Unis | 2 | 3,5 | 4 | 16 | 50 | K80, P100, V100 | N |
| Europe Nord | 4 | 16 | 4 | 16 | 50 | K80 | O |
| Norvège Est | 4 | 16 | N/A | N/A | 50 | N/A | N |
| États-Unis - partie centrale méridionale | 4 | 16 | 4 | 16 | 50 | V100 | O |
| Asie Sud-Est | 4 | 16 | 4 | 16 | 50 | P100, V100 | O |
| Inde Sud | 4 | 16 | N/A | N/A | 50 | K80 | N |
| Suisse Nord | 4 | 16 | N/A | N/A | 50 | N/A | N |
| Sud du Royaume-Uni | 4 | 16 | 4 | 16 | 50 | N/A | O|
| Ouest du Royaume-Uni | 4 | 16 | N/A | N/A | 50 | N/A | N |
| Émirats arabes unis Nord | 4 | 16 | N/A | N/A | 50 | N/A | N |
| Centre-USA Ouest| 4 | 16 | 4 | 16 | 50 | N/A | N |
| Europe Ouest | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 | O |
| USA Ouest | 4 | 16 | 4 | 16 | 50 | N/A | N |
| USA Ouest 2 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 | O |

Les ressources maximales suivantes sont accessibles à un groupe de conteneurs déployé avec [Ressources GPU](container-instances-gpu.md) (préversion).

> [!IMPORTANT]
> À ce stade, les déploiements avec des ressources GPU ne sont pas pris en charge dans le déploiement d’un réseau virtuel Azure et ne sont disponibles que sur les groupes de conteneurs Linux.

| Références SKU de GPU | Nombre de GPU | Utilisation maximale du processeur | Mémoire max. (GB) | Stockage (Go) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100, V100 | 1 | 6 | 112 | 50 |
| P100, V100 | 2 | 12 | 224 | 50 |
| P100, V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Groupes de conteneurs Windows

Les régions et les ressources maximales suivantes sont accessibles aux groupes de conteneurs Windows Server 2019 (préversion) avec les conteneurs Windows Server [pris en charge ou en préversion](./container-instances-faq.yml).

> [!IMPORTANT]
> À ce stade, les déploiements avec des groupes de conteneurs Windows ne sont pas pris en charge dans le déploiement d’un réseau virtuel Azure.

### <a name="windows-server-2016"></a>Windows Server 2016

> [!NOTE]
> Les hôtes 1B et 2B ont été dépréciés pour Windows Server 2016. Pour plus d’informations sur les hôtes 1B, 2B et 3B, consultez [Compatibilité des versions d’hôte et de conteneur](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility).

| Région |Utilisation maximale de l’UC pour 3B | Mémoire maximale (Go) pour 3B | Stockage (Go) |
| -------- | :----: | :-----: | :-------: |
| Australie Est | 2 | 8 | 20 |
| Brésil Sud | 4 | 16 | 20 |
| Centre du Canada  | 2 | 3,5 | 20 |
| Inde centrale | 2 | 3,5 | 20 |
| USA Centre | 2 | 3,5 | 20 |
| Asie Est | 2 | 3,5 | 20 |
| USA Est | 2 | 8 | 20 |
| USA Est 2 | 4 | 16 | 20 |
| Japon Est | 4 | 16 | 20 |
| Centre de la Corée | 4 | 16 | 20 |
| Centre-Nord des États-Unis | 4 | 16 | 20 |
| Europe Nord | 2 | 8 | 20 |
| États-Unis - partie centrale méridionale | 2 | 8 | 20 |
| Asie Sud-Est | 2 | 3,5 | 20 |
| Inde Sud | 2 | 3,5 | 20 |
| Sud du Royaume-Uni | 2 | 3,5 | 20 |
| Centre-USA Ouest | 2 | 8 | 20 |
| Europe Ouest | 4 | 16 | 20 |
| USA Ouest | 2 | 8 | 20 |
| USA Ouest 2 | 2 | 3,5 | 20 |

### <a name="windows-server-2019-ltsc"></a>Windows Server 2019 LTSC

> [!NOTE]
> Les hôtes 1B et 2B ont été dépréciés pour Windows Server 2019 LTSC. Pour plus d’informations sur les hôtes 1B, 2B et 3B, consultez [Compatibilité des versions d’hôte et de conteneur](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility).

| Région | Utilisation maximale de l’UC pour 3B | Mémoire maximale (Go) pour 3B | Stockage (Go) | Prise en charge des zones de disponibilité |
| -------- | :----: | :-----: | :-------: |
| Australie Est | 4 | 16 | 20 | O |
| Brésil Sud | 4 | 16 | 20 | O |
| Centre du Canada | 4 | 16 | 20 | N |
| Inde centrale | 4 | 16 | 20 | N |
| USA Centre | 4 | 16 | 20 | O |
| Asie Est | 4 | 16 | 20 | N |
| USA Est | 4 | 16 | 20 | O |
| USA Est 2 | 2 | 3,5 | 20 | O |
| France Centre | 4 | 16 | 20 | O |
| Japon Est | 4 | 16 | 20 | O |
| Centre de la Corée | 4 | 16 | 20 | N |
| Centre-Nord des États-Unis | 4 | 16 | 20 | N |
| Europe Nord | 4 | 16 | 20 | O |
| États-Unis - partie centrale méridionale | 4 | 16 | 20 | O |
| Asie Sud-Est | 4 | 16 | 20 | O |
| Inde Sud | 4 | 16 | 20 | N |
| Sud du Royaume-Uni | 4 | 16 | 20 | O |
| Centre-USA Ouest | 4 | 16 | 20 | N |
| Europe Ouest | 4 | 16 | 20 | O |
| USA Ouest | 4 | 16 | 20 | N |
| USA Ouest 2 | 4 | 16 | 20 | O |

## <a name="next-steps"></a>Étapes suivantes

Informez l’équipe si vous souhaitez voir des régions supplémentaires ou une disponibilité accrue des ressources sur le site [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Pour plus d’informations sur la résolution des problèmes de déploiement d’instances de conteneur, consultez [Résoudre les problèmes de déploiement avec Azure Container Instances](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[az-region-support]: /availability-zones/az-region.md#azure-regions-with-availability-zones