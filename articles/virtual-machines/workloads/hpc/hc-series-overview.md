---
title: Vue d’ensemble des machines virtuelles série HC - Machine virtuelles Azure | Microsoft Docs
description: En savoir plus sur la prise en charge de la préversion de la taille de machine virtuelle série HC dans Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 0db18bf162ee91ddccc070d7250a2ee35b05f5a9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689204"
---
# <a name="hc-series-virtual-machine-overview"></a>Vue d’ensemble des machines virtuelles série HC

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

L’optimisation des performances des applications HPC sur les processeurs scalable Intel Xeon nécessite une approche bien pensée pour traiter le placement dans cette nouvelle architecture. Ici, nous en décrivons l’implémentation sur des machines virtuelles série HC Azure pour les applications HPC. Nous utiliserons le terme « pNUMA » pour faire référence à un domaine NUMA physique et « vNUMA » pour faire référence à un domaine NUMA virtualisé. De même, nous utiliserons le terme « pCore » pour faire référence à des cœurs de processeur physiques et « vCore » pour faire référence à des cœurs de processeur virtualisés.

Physiquement, un serveur [série HC](../../hc-series.md) correspond à 2 UC Intel Xeon Platinum 8168 à 24 cœurs pour un total de 48 cœurs physiques. Chaque UC est un domaine pNUMA unique et a un accès unifié à six canaux de DRAM. Les UC Intel Xeon Platinum disposent d’un cache L2 4 fois supérieur à celui des générations précédentes (256 Ko/cœur -> 1 Mo/cœur), tout en réduisant le cache L3 par rapport aux UC Intel précédentes (2,5 Mo/cœur -> 1,375 Mo/cœur).

La topologie ci-dessus s’applique aussi à la configuration de l’hyperviseur série HC. Pour permettre à l’hyperviseur Azure de fonctionner sans interférer avec la machine virtuelle, nous réservons les pCores 0-1 et 24-25 (autrement dit, les 2 premiers pCores sur chaque socket). Nous affectons ensuite des domaines pNUMA à tous les cœurs restants sur la machine virtuelle. Par conséquent, la machine virtuelle verra :

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` cœurs par machine virtuelle

La machine virtuelle ne sait pas que les pCores 0-1 et 24-25 ne lui ont pas été attribués. Par conséquent, elle expose chaque vNUMA comme si elle avait 22 cœurs en mode natif.

Les UC Intel Xeon Platinum, Gold et Silver inaugurent également un réseau maillé 2D on-die pour la communication interne et externe au socket de l’UC. Nous recommandons vivement l’épinglage de processus (process pinning) à des fins de performances optimales. Le brochage de processus fonctionne sur les machines virtuelles série HC, car le silicium sous-jacent est exposé comme tel à la machine virtuelle invitée.

Le diagramme suivant illustre la répartition des cœurs réservés pour l’hyperviseur Azure et la machine virtuelle série HC.

![Répartition des cœurs réservés pour l’hyperviseur Azure et la machine virtuelle série HC](./media/architecture/hc-segregation-cores.png)

## <a name="hardware-specifications"></a>Spécifications matérielles

| Spécifications matérielles          | Machine virtuelle série HC                     |
|----------------------------------|----------------------------------|
| Cœurs                            | 44 (HT désactivé)                 |
| UC                              | Intel Xeon Platinum 8168         |
| Fréquence de l’UC (non AVX)          | 3,7 GHz (simple cœur) 2,7-3,4 GHz (tous les cœurs) |
| Mémoire                           | 8 Go/cœur (352 total)            |
| Disque local                       | -SSD de 700 Go                       |
| Infiniband                       | 100 Gbits EDR Mellanox ConnectX-5   |
| Réseau                          | 50 Gbits Ethernet (40 Gbits utilisables) SmartNIC Azure 2ème gén.    |

## <a name="software-specifications"></a>Spécifications logicielles

| Spécifications logicielles     |Machine virtuelle série HC           |
|-----------------------------|-----------------------|
| Taille de travail MPI max            | 13 200 cœurs (300 machines virtuelles dans un seul groupe de machines virtuelles identiques avec singlePlacementGroup=true)  |
| Prise en charge MPI                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, Platform MPI  |
| Frameworks supplémentaires       | UCX, libfabric, PGAS |
| Prise en charge de Stockage Azure       | Disques Standard et Premium (maximum 4 disques) |
| Prise en charge du système d’exploitation pour SRIOV RDMA   | CentOS/RHEL 7.6+, Ubuntu 16.04+, SLES 12 SP4+, WinServer 2016+  |
| Prise en charge d’Orchestrator        | CycleCloud, Batch, AKS ; [options de configuration de cluster](../../sizes-hpc.md#cluster-configuration-options)  |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur [Architecture Intel Xeon SP](https://software.intel.com/content/www/us/en/develop/articles/intel-xeon-processor-scalable-family-technical-overview.html).
- Consultez les dernières annonces, des exemples de charge de travail HPC et les résultats des performances sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).
