---
title: Séries Dasv5 et Dadsv5 – Machines virtuelles Azure
description: Spécifications pour les machines virtuelles des séries Dasv5 et Dadsv5.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 10/8/2021
ms.openlocfilehash: c9f2c270f8794effccd8439edd57422db15b71b6
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989923"
---
# <a name="dasv5-and-dadsv5-series"></a>Séries Dasv5 et Dadsv5

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les séries Dasv5 et Dadsv5 utilisent le processeur EPYC<sup>TM</sup> 7763V de 3e génération d’AMD dans une configuration multithread avec jusqu’à 256 Mo de cache L3, donnant plus options aux clients pour l’exécution de leurs charges de travail universelles. Ces machines virtuelles offrent une combinaison de processeurs virtuels et de mémoire permettant de répondre aux exigences associées à la plupart des charges de travail d’entreprise, comme les bases de données de taille petite à moyenne, les serveurs web avec un trafic faible à moyen, les serveurs d’applications, etc.

## <a name="dasv5-series"></a>Série Dasv5

Les machines virtuelles de la série Dasv5 utilisent des processeurs EPYC<sup>TM</sup> 7763v de 3e génération d’AMD pour obtenir une fréquence maximale renforcée de 3,5 GHz. Les tailles de la série Dasv5 offrent une combinaison de processeur virtuel et de mémoire pour la plupart des charges de travail de production. Les nouvelles machines virtuelles sans disque local constituent une meilleure proposition de valeur pour les charges de travail ne nécessitant pas de disque temporaire local.

> [!NOTE]
> Pour accéder à la FAQ, consultez [Tailles de machines virtuelles Azure sans disque temporaire local](azure-vms-no-temp-disk.yml).

Les machines virtuelles de la série Dasv5 prennent en charge les types de disques SSD Standard, HDD Standard et SSD Premium. Vous pouvez également attacher un stockage Disque Ultra en fonction de sa disponibilité régionale. Le stockage sur disque de données est facturé séparément des machines virtuelles. [Consultez les prix des disques](https://azure.microsoft.com/pricing/details/managed-disks/).

[Stockage Premium](premium-storage-performance.md) : Pris en charge <br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge <br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge <br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge <br>
[Prise en charge de la génération de machine virtuelle](generation-2.md) : Générations 1 et 2 <br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Non pris en charge <br><br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit du disque non mis en cache max. : IOPS/Mbits/s | Débit du disque maximal de rafale non mis en cache : IOPS/Mo/s<sup>1</sup> | Nombre max de cartes réseau | Bande passante réseau maximale (Mbits/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2as_v5  | 2  | 8   | Stockage distant uniquement | 4  | 3750/82    | 10000/600   | 2 | 12 500  |
| Standard_D4as_v5  | 4  | 16  | Stockage distant uniquement | 8  | 6400/144   | 20000/600   | 2 | 12 500  |
| Standard_D8as_v5  | 8  | 32  | Stockage distant uniquement | 16 | 12800/200  | 20000/600   | 4 | 12 500  |
| Standard_D16as_v5 | 16 | 64  | Stockage distant uniquement | 32 | 25 600/384  | 40000/800   | 8 | 12 500 |
| Standard_D32as_v5 | 32 | 128 | Stockage distant uniquement | 32 | 51 200/768  | 80000/1600  | 8 | 16000 |
| Standard_D48as_v5 | 48 | 192 | Stockage distant uniquement | 32 | 76 800/1152 | 80 000/2 000  | 8 | 24 000 |
| Standard_D64as_v5 | 64 | 256 | Stockage distant uniquement | 32 | 80 000/1 200 | 80 000/2 000  | 8 | 32000 |
| Standard_D96as_v5 | 96 | 384 | Stockage distant uniquement | 32 | 80000/1600 | 80 000/2 000  | 8 | 40000 |


<sup>1</sup> Les machines virtuelles de la série Dasv5 peuvent disposer d’un [bursting](disk-bursting.md) des performances de leurs disques et atteindre le niveau maximal de bursting pendant 30 minutes d’affilée.


## <a name="dadsv5-series"></a>Série Dadsv5

Les machines virtuelles de la série Dadsv5 utilisent des processeurs EPYC<sup>TM</sup> 7763v de 3e génération d’AMD pour obtenir une fréquence maximale renforcée de 3,5 GHz. Les tailles de la série Dadsv5 offrent une combinaison de processeur virtuel, de mémoire et de stockage temporaire adaptée à la plupart des charges de travail de production. Ces nouvelles machines virtuelles disposent d’un stockage local 50 % plus volumineux, ainsi que de meilleures IOPS de disque local en lecture et en écriture par rapport aux tailles [Dav4/Dasv4](dav4-dasv4-series.md) avec des machines virtuelles [Gen2](generation-2.md).

Les machines virtuelles de la série Dadsv5 prennent en charge les types de disques SSD Standard, HDD Standard et SSD Premium. Vous pouvez également attacher un stockage Disque Ultra en fonction de sa disponibilité régionale. Le stockage sur disque de données est facturé séparément des machines virtuelles. [Consultez les prix des disques](https://azure.microsoft.com/pricing/details/managed-disks/).


[Stockage Premium](premium-storage-performance.md) : Pris en charge <br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge <br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge <br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge <br>
[Prise en charge de la génération de machine virtuelle](generation-2.md) : Générations 1 et 2 <br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge <br><br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire maximal : IOPS/MBits/s | Débit du disque non mis en cache max. : IOPS/Mbits/s | Débit du disque maximal de rafale non mis en cache : IOPS/Mo/s<sup>1</sup> | Nombre max de cartes réseau | Bande passante réseau maximale (Mbits/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2ads_v5  | 2  | 8   | 75   | 4  | 9000 / 125    | 3750/82    | 10000/600  | 2 | 12 500  |
| Standard_D4ads_v5  | 4  | 16  | 150  | 8  | 19000 / 250   | 6400/144   | 20000/600  | 2 | 12 500  |
| Standard_D8ads_v5  | 8  | 32  | 300  | 16 | 38000 / 500   | 12800/200  | 20000/600  | 4 | 12 500  |
| Standard_D16ads_v5 | 16 | 64  | 600  | 32 | 75000 / 1000  | 25 600/384  | 40000/800  | 8 | 12 500 |
| Standard_D32ads_v5 | 32 | 128 | 1200 | 32 | 150000 / 2000 | 51 200/768  | 80000/1000 | 8 | 16000 |
| Standard_D48ads_v5 | 48 | 192 | 1800 | 32 | 225000 / 3000 | 76 800/1152 | 80000/200 | 8 | 24 000 |
| Standard_D64ads_v5 | 64 | 256 | 2 400 | 32 | 300000 / 4000 | 80 000/1 200 | 80 000/2 000 | 8 | 32000 |
| Standard_D96ads_v5 | 96 | 384 | 3600 | 32 | 450000 / 4000 | 80000/1600 | 80 000/2 000 | 8 | 40000 |

* Ces valeurs IOPS peuvent être atteintes avec des machines virtuelles de deuxième génération.<br>
<sup>1</sup> Les machines virtuelles de la série Dadsv5 peuvent disposer d’un [bursting](disk-bursting.md) des performances de leurs disques et atteindre le niveau maximal de bursting pendant 30 minutes d’affilée.


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
