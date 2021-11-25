---
title: Série Fsv2
description: Spécifications pour les machines virtuelles de la série Fsv2.
author: brbell
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 4c652a9292caf8e7ac198ac6922b11b78a85781e
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401418"
---
# <a name="fsv2-series"></a>Série Fsv2

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

La série Fsv2 s’exécute sur les processeurs Intel® Xeon® Platinum 8272CL (cascade Lake) et les processeurs Intel® Xeon® Platinum 8168 (Skylake). Il est doté d’une fréquence d’horloge turbo tous cœurs prolongée de 3,4 GHz et d’une fréquence turbo monocœur maximale de 3,7 GHz. Les instructions Intel® AVX-512 sont une nouveauté sur les processeurs Intel Scalable. Elles fourniront jusqu’à 2X plus de performances pour les charges de travail de traitement vectoriel pour les opérations en virgule flottante simple ou double précision. En d’autres termes, elles sont très rapides pour les charges de travail de calcul.

Machines virtuelles de la série Fsv2 dotées de la technologie Hyper-Threading d’Intel®.

[ACU](acu.md) : 195 - 210<br>
[Stockage Premium](premium-storage-performance.md) : Pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Prise en charge de la génération de machine virtuelle](generation-2.md) : Générations 1 et 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge <br>
[Virtualisation imbriquée](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) : prise en charge <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS/Mbits/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS/Mbits/s |  Débit du disque maximal de rafale non mis en cache : IOPS/Mo/s<sup>1</sup> |Nombre max de cartes réseau|Bande passante réseau attendue (Mbit/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_F2s_v2<sup>4</sup>  | 2  | 4   | 16  | 4  | 4 000/31 (32)       | 3 200/47    | 4 000/200 | 2| 5 000   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8 000/63 (64)       | 6 400/95    | 8 000/200 | 2|10000  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16 000/127 (128)    | 12 800/190  | 16 000/400 | 4|12 500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32 000/255 (256)    | 25 600/380  | 32 000/800 | 4|12 500  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64 000/512 (512)    | 51 200/750  | 64 000/1 600 | 8|16000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96 000/768 (768)    | 76 800/1 100 | 80 000/2 000 | 8|21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128 000/1 024 (1 024) | 80 000/1 100 | 80 000/2 000 | 8|28000 |
| Standard_F72s_v2<sup>2, 3</sup> | 72 | 144 | 576 | 32 | 144 000/1 152 (1 520) | 80 000/1 100 | 80 000/2 000 | 8|30000 |

<sup>1</sup> Les machines virtuelles de la série Fsv2 peuvent disposer d’un [bursting](./disk-bursting.md) des performances de leurs disques et atteindre le niveau maximal de bursting pendant 30 minutes d’affilée.

<sup>2</sup> L’utilisation de plus de 64 processeurs virtuels nécessite l’un de ces systèmes d’exploitation invités pris en charge :

- Windows Server 2016 ou version ultérieure
- Ubuntu 16.04 LTS or version ultérieure, avec noyau Azure adapté (noyau 4.15 ou version ultérieure)
- SLES 12 SP2 ou version ultérieure
- RHEL ou CentOS version 6.7 à 6.10, avec le package LIS fourni par Microsoft 4.3.1 (ou version ultérieure) installé
- RHEL ou CentOS version 7.3, avec le package LIS fourni par Microsoft 4.2.1 (ou version ultérieure) installé
- RHEL ou CentOS 7.6 ou version ultérieure
- Oracle Linux avec UEK4 ou version ultérieure
- Debian 9 avec le noyau backports, Debian 10 ou version ultérieure
- CoreOS avec un noyau 4.14 ou version ultérieure

<sup>3</sup> L’instance est isolée sur un matériel dédié à un client unique.<br>
<sup>4</sup> L’accélération réseau peut uniquement être appliquée à une seule carte d’interface réseau.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Autres tailles et informations

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

Calculatrice de prix : [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)

Pour plus d’informations sur les types de disques : [Types de disques](./disks-types.md#ultra-disks)


## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
