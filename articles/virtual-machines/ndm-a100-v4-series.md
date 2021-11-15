---
title: NDm A100 v4-series
description: Spécifications pour les machines virtuelles de la série NDm A100 v4.
author: sherrywangms
ms.author: sherrywang
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 10/26/2021
ms.openlocfilehash: 58070e351bd52be6ab1486a3f68bd544a86eaf4e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479440"
---
# <a name="ndm-a100-v4-series"></a>NDm A100 v4-series

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

La série de machines virtuelles NDm A100 v4 est un nouvel ajout à la famille de GPU Azure, conçue pour un apprentissage Deep Learning de pointe et des de charges de travail HPC de montée en puissance parallèle et montée en charge. 

La série NDm A100 v4 commence avec une machine virtuelle unique et huit GPU NVIDIA Ampere A100 80GB Tensor Core. Les déploiements basé sur NDm A100 v4 peuvent monter en puissance jusqu’à des milliers de GPU avec une bande passante d’interconnexion de 1,6 Tbit/s par machine virtuelle. Chaque GPU au sein de la machine virtuelle est fourni avec sa propre connexion indépendante de la topologie NVIDIA Mellanox HDR InfiniBand 200 Gbit/s. Ces connexions sont configurées automatiquement entre les machines virtuelles occupant le même groupe de machines virtuelles identiques, et prennent en charge GPUDirect RDMA.

Chaque GPU est doté de la connectivité NVLINK 3.0 pour la communication au sein de la machine virtuelle, et l’instance est également sauvegardée par 96 GPU physiques Epyc™ de 2e génération.

Ces instances offrent d’excellentes performances pour de nombreux outils d’IA, de ML et d’analyse qui prennent en charge l’accélération GPU « prêts à l’emploi », tels que TensorFlow, Pytorch, Caffe, RAPIDS et d’autres frameworks. En outre, l’interconnexion InfiniBand avec montée en puissance parallèle est prise en charge par un grand nombre d’outils IA et HPC existants basés sur les bibliothèques de communication NCCL2 de NVIDIA pour le clustering transparent des GPU.

> [!IMPORTANT]
> Pour vous familiariser avec les machines virtuelles NDm A100 v4, reportez-vous à [Configuration et optimisation des charges de travail HPC](./workloads/hpc/configure.md) pour connaître les étapes, notamment la configuration du pilote et du réseau.
> En raison de l’augmentation de l’empreinte E/S de la mémoire GPU, la NDm A100 v4 nécessite l’utilisation de [machines virtuelles de génération 2](./generation-2.md) et d’images de la place de marché. Les [images Azure HPC](./workloads/hpc/configure.md) sont vivement recommandées. Les images Azure HPC Ubuntu 18.04, 20.04 et Azure HPC CentOS 7.9 sont prises en charge.
> 

<br>

[Stockage Premium](premium-storage-performance.md) : Pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Disques Ultra](disks-types.md#ultra-disks) : pris en charge ([En savoir plus](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) sur la disponibilité, l’utilisation et les performances) <br>
[Migration dynamique](maintenance-and-updates.md) : Non pris en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Non pris en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Génération 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Non pris en charge<br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge<br>
InfiniBand : pris en charge, GPUDirect RDMA, 8 x 200 Gigabit HDR<br>
Interconnexion Nvidia/NVLink : Pris en charge<br>
<br>
La série NDm A100 v4 prend en charge les versions de noyau suivantes : <br>
CentOS 7.9 HPC : 3.10.0-1160.24.1.el7.x86_64 <br>
Ubuntu 18.04 : 5.4.0-1043-azure <br>
Ubuntu 20.04 : 5.4.0-1046-azure <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) : Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Débit du disque non mis en cache max. : IOPS / MBps | Bande passante réseau maximale | Nombre max de cartes réseau |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND96amsr_v4 | 96 | 1900 | 6 400 | 8 GPU 80 Go A100 (NVLink 3.0) | 80 | 32 | 80 000 / 800 | 24 000 Mbits/s | 8 |

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