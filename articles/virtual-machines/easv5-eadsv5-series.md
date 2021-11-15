---
title: Séries Easv5 et Eadsv5 – Machines virtuelles Azure
description: Spécifications pour les machines virtuelles des séries Easv5 et Eadsv5.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/8/2021
ms.openlocfilehash: 4628f6c6d368f5d62a8c7e5a2dd07adab9d0a3b5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479080"
---
# <a name="easv5-and-eadsv5-series"></a>Séries Easv5 et Eadsv5

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les séries Easv5 et Eadsv5 utilisent le processeur EPYC<sup>TM</sup> 7763V de 3e génération d’AMD dans une configuration multithread avec jusqu’à 256 Mo de cache L3, donnant plus options aux clients pour l’exécution de la plupart des charges de travail à mémoire optimisée. Ces machines virtuelles offrent une combinaison de processeurs virtuels et de mémoire pour répondre aux exigences associées à la plupart des applications professionnelles gourmandes en mémoire, telles que les serveurs de base de données relationnelle et les charges de travail d’analytique en mémoire.

## <a name="easv5-series"></a>Série Easv5

Les machines virtuelles de la série Easv5 utilisent des processeurs EPYC<sup>TM</sup> 7763v de 3e génération d’AMD pour obtenir une fréquence maximale renforcée de 3,7 GHz. Les tailles de la série Easv5 offrent une combinaison de processeurs virtuels et de mémoire idéaux pour les applications d’entreprise gourmandes en mémoire. Les nouvelles machines virtuelles sans disque local constituent une meilleure proposition de valeur pour les charges de travail ne nécessitant pas de disque temporaire local.

> [!NOTE]
> Pour accéder à la FAQ, consultez [Tailles de machines virtuelles Azure sans disque temporaire local](azure-vms-no-temp-disk.yml).

Les machines virtuelles de la série Easv5 prennent en charge les types de disques SSD Standard, HDD Standard et SSD Premium. Vous pouvez également attacher un stockage Disque Ultra en fonction de sa disponibilité régionale. Le stockage sur disque de données est facturé séparément des machines virtuelles. [Consultez les prix des disques](https://azure.microsoft.com/pricing/details/managed-disks/).

[Stockage Premium](premium-storage-performance.md) : Pris(e) en charge <br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge <br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge <br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge <br>
[Prise en charge de la génération de machine virtuelle](generation-2.md) : Générations 1 et 2 <br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Non pris en charge <br><br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit du disque non mis en cache max. : IOPS/Mbits/s | Débit du disque maximal de rafale non mis en cache : IOPS/Mo/s<sup>1</sup> | Nombre max de cartes réseau | Bande passante réseau maximale (Mbits/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2as_v5              | 2  | 16  | Stockage distant uniquement | 4  | 3 750/82    | 10 000/600  | 2 | 12 500  |
| Standard_E4as_v5<sup>2</sup>  | 4  | 32  | Stockage distant uniquement | 8  | 6 400/144   | 20 000/600  | 2 | 12 500  |
| Standard_E8as_v5<sup>2</sup>  | 8  | 64  | Stockage distant uniquement | 16 | 12 800/200  | 20 000/600  | 4 | 12 500  |
| Standard_E16as_v5<sup>2</sup> | 16 | 128 | Stockage distant uniquement | 32 | 25 600/384  | 40 000/800  | 8 | 12 500 |
| Standard_E20as_v5             | 20 | 160 | Stockage distant uniquement | 32 | 32 000/480  | 64 000/1 000 | 8 | 12 500 |
| Standard_E32as_v5<sup>2</sup> | 32 | 256 | Stockage distant uniquement | 32 | 51 200/768  | 80 000/1 600 | 8 | 16000 |
| Standard_E48as_v5             | 48 | 384 | Stockage distant uniquement | 32 | 76 800/1152 | 80 000/2 000 | 8 | 24 000 |
| Standard_E64as_v5<sup>2</sup> | 64 | 512 | Stockage distant uniquement | 32 | 80 000/1 200 | 80 000/2 000 | 8 | 32000 |
| Standard_E96as_v5<sup>2</sup> | 96 | 672 | Stockage distant uniquement | 32 | 80 000/1 600 | 80 000/2 000 | 8 | 40000 |

<sup>1</sup> Les machines virtuelles de la série Easv5 peuvent disposer d’un [bursting](disk-bursting.md) des performances de leurs disques et atteindre le niveau maximal de bursting pendant 30 minutes d’affilée.<br>
<sup>2</sup> [Tailles avec contraintes de cœurs disponibles](constrained-vcpu.md)



## <a name="eadsv5-series"></a>Série Eadsv5

Les machines virtuelles de la série Eadsv5 utilisent des processeurs EPYC<sup>TM</sup> 7763v de 3e génération d’AMD pour obtenir une fréquence maximale renforcée de 3,7 GHz. Les tailles de la série Eadsv5 offrent une combinaison de processeurs virtuels, de mémoire et de stockage temporaire idéaux pour les applications d’entreprise gourmandes en mémoire. Ces nouvelles machines virtuelles disposent d’un stockage local 50 % plus volumineux, ainsi que de meilleures IOPS de disque local en lecture et en écriture par rapport aux tailles [Eav4/Easv4](eav4-easv4-series.md) avec des machines virtuelles [Gen2](generation-2.md).

Les machines virtuelles de la série Eadsv5 prennent en charge les types de disques SSD Standard, HDD Standard et SSD Premium. Vous pouvez également attacher un stockage Disque Ultra en fonction de sa disponibilité régionale. Le stockage sur disque de données est facturé séparément des machines virtuelles. [Consultez les prix des disques](https://azure.microsoft.com/pricing/details/managed-disks/).

[Stockage Premium](premium-storage-performance.md) : Pris(e) en charge <br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge <br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge <br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge <br>
[Prise en charge de la génération de machine virtuelle](generation-2.md) : Générations 1 et 2 <br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge <br><br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire maximal : IOPS/MBits/s | Débit du disque non mis en cache max. : IOPS/Mbits/s | Débit du disque maximal de rafale non mis en cache : IOPS/Mo/s<sup>1</sup> | Nombre max de cartes réseau | Bande passante réseau maximale (Mbits/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2ads_v5              | 2  | 16  | 75   | 4  | 9 000 / 125    | 3 750/82      | 10 000/600  | 2 | 12 500  |
| Standard_E4ads_v5<sup>2</sup>  | 4  | 32  | 150  | 8  | 19 000 / 250   | 6 400/144     | 20 000/600  | 2 | 12 500  |
| Standard_E8ads_v5<sup>2</sup>  | 8  | 64  | 300  | 16 | 38 000 / 500   | 12 800/200    | 20 000/600  | 4 | 12 500  |
| Standard_E16ads_v5<sup>2</sup> | 16 | 128 | 600  | 32 | 75 000 / 1 000  | 25 600/384    | 40 000/800  | 8 | 12 500 |
| Standard_E20ads_v5             | 20 | 160 | 750  | 32 | 94 000 / 1 250  | 32 000/480    | 64 000/1 000 | 8 | 12 500 |
| Standard_E32ads_v5<sup>2</sup> | 32 | 256 | 1200 | 32 | 150 000 / 2 000 | 51 200/768    | 80 000/1 600 | 8 | 16000 |
| Standard_E48ads_v5             | 48 | 384 | 1800 | 32 | 225 000 / 3 000 | 76 800/1152   | 80 000/2 000 | 8 | 24 000 |
| Standard_E64ads_v5<sup>2</sup> | 64 | 512 | 2 400 | 32 | 300 000 / 4 000 | 80 000/1 200   | 80 000/2 000 | 8 | 32000 |
| Standard_E96ads_v5<sup>2</sup> | 96 | 672 | 3600 | 32 | 45 0000 / 4 000 | 80 000/1 600   | 80 000/2 000 | 8 | 40000 |

* Ces valeurs IOPS peuvent être atteintes avec des machines virtuelles de deuxième génération.<br>
<sup>1</sup> Les machines virtuelles de la série Eadsv5 peuvent disposer d’un [bursting](disk-bursting.md) des performances de leurs disques et atteindre le niveau maximal de bursting pendant 30 minutes d’affilée.<br>
<sup>2</sup> [Tailles avec contraintes de cœurs disponibles](constrained-vcpu.md).


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