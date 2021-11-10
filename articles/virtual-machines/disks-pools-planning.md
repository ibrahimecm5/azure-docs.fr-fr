---
title: Optimiser les performances de vos pools de disques Azure (préversion)
description: Découvrez comment optimiser les performances d’un pool de disques Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 801930dc58993a81c29969f602da64f201919bd0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428004"
---
# <a name="azure-disk-pools-preview-planning-guide"></a>Guide de planification des pools de disques Azure (préversion)

Avant de déployer un pool de disques Azure (préversion), il est important de comprendre les exigences de performances de votre charge de travail. La détermination de vos exigences à l’avance vous permet d’optimiser les performances de votre pool de disques. Les performances d’un pool de disques sont déterminées par trois facteurs principaux. L’ajustement de l’un d’eux a pour effet de modifier les performances de votre pool de disques. C’est facteurs sont les suivants :

- Cible de scalabilité du pool de disques
- Objectifs de scalabilité des disques individuels contenus dans le pool de disques
- Connexion réseau entre les machines clientes et le pool de disques.

## <a name="optimize-for-low-latency"></a>Optimiser pour une faible latence

Si vous privilégiez une faible latence, ajoutez des disques Ultra à votre pool de disques. Les disques Ultra offrent une latence inférieure à la milliseconde. Pour bénéficier de la latence la plus faible possible, vous devez également évaluer la configuration de votre réseau et vous assurer qu’elle utilise le chemin optimal. Pour minimiser la latence réseau, songez à utiliser [ExpressRoute FastPath](../expressroute/about-fastpath.md).

## <a name="optimize-for-high-throughput"></a>Optimiser pour un débit élevé

Si vous établissez la priorité du débit, commencez par évaluer les objectifs de performance des différentes références SKU du pool de disques, ainsi que le nombre de pools de disques requis pour fournir vos cibles de débit. Si vos besoins en performances dépassent ce qu’un pool de disques premium peut fournir, vous pouvez fractionner votre déploiement sur plusieurs pools de disques. Ensuite, vous pouvez décider comment utiliser au mieux les performances proposées sur un pool de disques entre chaque disque individuel et leurs types. Pour un pool de disques, vous pouvez mélanger et faire correspondre les disques SSD standard et premium, ou utiliser uniquement des disques ultra. Vous ne pouvez pas utiliser des disques ultra avec des disques SSD premium ou standard. Sélectionnez le type de disque qui correspond le mieux à vos besoins. Vérifiez également que la connectivité réseau entre vos clients et le pool de disques ne constitue pas un goulot d’étranglement, en particulier en matière de débit.


## <a name="use-cases"></a>Cas d'utilisation

Le tableau suivant présente des cas d’usage classiques pour des pools de disques avec Azure VMware Solution et une configuration recommandée.


|Cas d’usage d’Azure VMware Solution  |Type de disque suggéré  |Référence SKU du pool de disques suggéré  |Configuration réseau suggérée  |
|---------|---------|---------|---------|
|Stockage par blocs pour les pages de travail actives, comme une extension de vSAN Azure VMware Solution.     |Disques Ultra         |Premium         |Utilisez une passerelle de réseau virtuel ExpressRoute Ultra-performance ou ErGw3AZ (10 Gbits/s) pour connecter le réseau virtuel du pool de disques au cloud Azure VMware Solution et permettre à FastPath de réduire la latence du réseau.         |
|Hiérarchisation : hiérarchiser les données rarement utilisées sur le pool de disques à partir du vSAN Azure VMware Solution.     |SSD premium, SSD standard         |Standard         |Utiliser la passerelle de réseau virtuel ExpressRoute Standard (1 Gbits/s) ou Haute performance (2 Gbits/s) pour connecter le réseau virtuel du pool de disques au cloud Azure VMware Solution.         |
|Stockage de données pour site de récupération d’urgence sur Azure VMware Solution : répliquer des données à partir d’un environnement VMware local ou principal vers le pool de disques en tant que site secondaire.     |SSD premium, SSD standard         |Standard, De base         |Utiliser la passerelle de réseau virtuel ExpressRoute Standard (1 Gbits/s) ou Haute performance (2 Gbits/s) pour connecter le réseau virtuel du pool de disques au cloud Azure VMware Solution.         |


Consultez [Liste de vérification pour la planification réseau pour Azure VMware Solution](../azure-vmware/tutorial-network-checklist.md) pour planifier votre configuration réseau, ainsi que découvrir d’autres considérations relatives à Azure VMware Solution.

## <a name="disk-pool-scalability-and-performance-targets"></a>Objectifs de performance et d’extensibilité de pool de disques

|Ressource  |Pool de disques de base  |Type de disques standards  |Type de disques premium  |
|---------|---------|---------|---------|
|Nombre maximal de disques par pool     |16         |32         |32         |
|Nombre maximal d’IOPS par pool de disques     |12 800         |25 600         |51 200         |
|Nombre maximal de Mbits/s par pool de disques     |192         |384         |768         |

L’exemple suivant doit vous donner une idée de la façon dont les différents facteurs de performance fonctionnent ensemble :

Par exemple, si vous ajoutez 2 disques SSD Premium de 1 Tio (P30, avec cible approvisionnée de 5 000 IOPS et 200 Mbits/s) dans un pool de disques standards, vous obtenez 2 x 5000 = 10 000 IOPS. Toutefois, le débit est plafonné à 384 Mbits/s par pool de disques. Pour aller au-delà de cette limite de 384 Mbits/s, vous pouvez déployer davantage de pools de disques pour effectuer un scale-out afin d’augmenter débit. Le débit de votre réseau limite l’efficacité du scale-out.

Les pools de disques créés sans spécifier la référence (SKU) dans l’API REST sont le pool de disques standard par défaut.

## <a name="availability"></a>Disponibilité

Les pools de disques étant actuellement en préversion, ils ne doivent pas être utilisés pour des charges de travail de production. Par défaut, un pool de disques prend uniquement en charge les disques SSD premium et standards. Vous pouvez activer la prise en charge des disques ultra sur un pool de disques, mais un pool de disques avec disques ultra n’est pas compatible avec les disques SSD premium ou standards.

Les pools de disques avec prise en charge des disques SSD premium et standards sont basés sur une architecture à haute disponibilité, avec des multiples qui hébergent le point de terminaison iSCSI. Les pools de disques avec prise en charge de disques ultra sont hébergés sur un déploiement d’instance unique.

Si votre pool de disques devient inaccessible à votre cloud Azure VMware Solution pour une raison quelconque, les conséquences seront les suivantes :

- Tous les magasins de données associés au pool de disques cesseront d’être accessibles.
- Toutes les machines virtuelles VMware hébergées dans le cloud Azure VMware Solution qui utilisent les magasins de données concernés seront dans un état non sain.
- L’intégrité des clusters dans le cloud Azure VMware Solution ne sera pas affectée, à l’exception d’une opération : vous ne pourrez pas mettre un ordinateur hôte en mode maintenance. Azure VMware Solution gèrera cet échec et tentera une récupération en déconnectant les magasins de données concernés.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer un pool de disques](disks-pools-deploy.md).
- Pour en savoir plus sur la façon dont les solutions VMware Azure intègrent les pools de disques, consultez [Attacher des pools de disques à des hôtes de solution VMware Azure (version préliminaire)](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md).
