---
title: Série ND – Machines virtuelles Microsoft Azure
description: Spécifications pour les machines virtuelles de la série ND.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 5dcbe4e7c1c72f444dfe6b3c9e4dae5c3f538ed5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003387"
---
# <a name="nd-series"></a>Série ND

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les machines virtuelles de la série ND sont nouvelles dans la famille de GPU et sont conçues pour les charges de travail d’intelligence artificielle et d’apprentissage profond. Elles offrent d’excellentes performances pour l’apprentissage et l’inférence. Les instances ND sont pilotées par des GPU [Tesla P40 de NVIDIA](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) et des processeurs Intel Xeon E5-2690 v4 (Broadwell). Ces instances offrent d’excellentes performances pour les opérations à virgule flottante simple précision, et pour les charges de travail d’intelligence artificielle utilisant Microsoft Cognitive Toolkit, TensorFlow, Caffe et d’autres infrastructures. La série ND offre également une taille de mémoire GPU beaucoup plus importante (24 Go), ce qui permet d’adapter des modèles de réseaux neuronaux beaucoup plus volumineux. À l’instar de la série NC, la série ND offre une configuration avec un réseau à faible latence secondaire et à haut débit grâce à l’accès direct à la mémoire à distance (RDMA), ainsi que la connectivité InfiniBand, de sorte que vous pouvez exécuter des travaux de formation à grande échelle s’étendant sur de nombreux GPU.

[Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Disques Ultra](disks-types.md#ultra-disks) : pris en charge ([En savoir plus](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) sur la disponibilité, l’utilisation et les performances) <br>
[Migration dynamique](maintenance-and-updates.md) : Non pris en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Non pris en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Génération 1 et 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Non pris en charge<br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge<br>
Interconnexion Nvidia/NVLink : Non pris en charge<br>

> [!IMPORTANT]
> Pour cette série de machines virtuelles, le quota de processeurs virtuels (cœurs) par région dans votre abonnement est initialement défini sur 0. [Demandez une augmentation du quota de processeurs virtuels](../azure-portal/supportability/regional-quota-requests.md) pour cette série dans une [région disponible](https://azure.microsoft.com/regions/services/).
>
| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Débit du disque non mis en cache max. : IOPS/Mbits/s | Nombre max de cartes réseau |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20 000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40 000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 96 | 32 | 80 000/800 | 8 |
| Standard_ND24rs* | 24 | 448 | 2948 | 4 | 96 | 32 | 80 000/800 | 8 |

1 GPU = une carte P40.

*Prenant en charge RDMA


## <a name="supported-operating-systems-and-drivers"></a>Systèmes d’exploitation et pilotes pris en charge

Pour tirer parti des fonctionnalités GPU de machines virtuelles de la série N Azure, installez des pilotes GPU NVIDIA.

[L’extension du pilote GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) installe les pilotes CUDA ou GRID NVIDIA appropriés sur une machine virtuelle de série N. Installez ou gérez l’extension à l’aide du portail Azure ou d’outils tels qu’Azure PowerShell ou les modèles Azure Resource Manager. Consultez la [documentation sur l’extension du pilote GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) pour connaître les systèmes d’exploitation pris en charge et les étapes de déploiement. Pour des informations générales sur les extensions de machine virtuelle, consultez [Extensions et fonctionnalités des machines virtuelles Azure](./extensions/overview.md).

Si vous choisissez d’installer manuellement les pilotes GPU NVIDIA, voir [Configuration des pilotes GPU de série N pour Windows](./windows/n-series-driver-setup.md) ou [Configuration des pilotes GPU de série N pour Linux](./linux/n-series-driver-setup.md) pour connaître les systèmes d’exploitation et pilotes pris en charge, ainsi que les étapes d’installation et de vérification.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Autres tailles et informations

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

Calculatrice de prix : [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)

Pour plus d’informations sur les types de disques, consultez [Quels sont les types de disque disponibles dans Azure ?](disks-types.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.