---
title: Séries Dav4 et Dasv4
description: Spécifications pour les machines virtuelles des séries Dav4 et Dasv4.
author: mamccrea
ms.author: mamccrea
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.reviewer: jushiman
ms.openlocfilehash: c35f0958b8bfd5aa2695e3387710eb4afb895af0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131463123"
---
# <a name="dav4-and-dasv4-series"></a>Séries Dav4 et Dasv4

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les séries Dav4 et Dasv4 sont de nouvelles tailles qui utilisent le processeur EPYC<sup>TM</sup> 7452 2,35 GHz d’AMD dans une configuration multithread, avec un cache L3 allant jusqu’à 256 Mo, dont 8 Mo sont dédiés aux 8 cœurs, ce qui offre aux clients davantage d’options pour exécuter leurs charges de travail à usage général. Elles ont les mêmes configurations de mémoire et de disque que les séries D et Dsv3.

## <a name="dav4-series"></a>Série Dav4

Les tailles de la série Dav4 sont basées sur le processeur AMD EPYC<sup>TM</sup> 7452 2,35 GHz, qui peut atteindre une fréquence maximale renforcée de 3,35 GHz. Elles offrent une combinaison de processeur virtuel, de mémoire et de stockage temporaire adaptée à la plupart des charges de travail de production. Le stockage sur disque de données est facturé séparément des machines virtuelles. Pour utiliser un SSD Premium, optez pour les tailles Dasv4. Les tarifs et les compteurs de facturation de ces tailles sont identiques à ceux de la série Dav4.

[ACU](acu.md) : 230-260<br>
[Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Génération 1<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge<br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire max. : IOPS / MBps en lecture / MBps en écriture | Nombre max de cartes réseau | Bande passante réseau attendue (Mbits/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4<sup>1</sup> |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 | 2000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 | 4000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 | 8000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 | 10000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 | 16000 |
| Standard_D48a_v4| 48 | 192| 1200 | 32 | 96000 / 1000 / 500 | 8 | 24 000 |
| Standard_D64a_v4| 64 | 256 | 1 600 | 32 | 96000 / 1000 / 500 | 8 | 32000 |
| Standard_D96a_v4| 96 | 384 | 2 400 | 32 | 96000 / 1000 / 500 | 8 | 40000 |

<sup>1</sup>La mise en réseau accélérée ne peut être appliquée qu’à une seule carte réseau. 

## <a name="dasv4-series"></a>Série Dasv4

Les tailles de la série Dasv4 sont basées sur le processeur AMD EPYC<sup>TM</sup> 7452 2,35 GHz, qui peut atteindre une fréquence maximale renforcée de 3,35 GHz et utiliser un SSD Premium. Elles offrent une combinaison de processeur virtuel, de mémoire et de stockage temporaire adaptée à la plupart des charges de travail de production.

[ACU](acu.md) : 230-260<br>
[Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Prise en charge de la génération de machine virtuelle](generation-2.md) : Générations 1 et 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge<br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit de stockage temporaire et débit maximal de rafale mis en cache : IOPS / Mbits/s<sup>1</sup> | Débit du disque non mis en cache max. : IOPS / MBps | Débit du disque maximal de rafale non mis en cache : IOPS/Mo/s<sup>1</sup> | Nombre max de cartes réseau | Bande passante réseau attendue (Mbits/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|----|
| Standard_D2as_v4<sup>2</sup>| 2  | 8   | 16  | 4  | 4000 / 32 (50)       | 4 000/100    | 3200 / 48    | 4 000/200   | 2 | 2000  |
| Standard_D4as_v4            | 4  | 16  | 32  | 8  | 8000 / 64 (100)      | 8 000/200    | 6400 / 96    | 8 000/200   | 2 | 4000  |
| Standard_D8as_v4            | 8  | 32  | 64  | 16 | 16000 / 128 (200)    | 16 000/400   | 12800 / 192  | 16 000/400  | 4 | 8000  |
| Standard_D16as_v4           | 16 | 64  | 128 | 32 | 32 000 / 255 (400)    | 32 000/800   | 25600 / 384  | 32 000/800  | 8 | 10000 |
| Standard_D32as_v4           | 32 | 128 | 256 | 32 | 64 000 / 510 (800)    | 64 000/1 600  | 51200 / 768  | 64 000/1 600 | 8 | 16000 |
| Standard_D48as_v4           | 48 | 192 | 384 | 32 | 96000 / 1020 (1200)  | 96 000/2 000  | 76800 / 1148 | 80 000/2 000 | 8 | 24 000 |
| Standard_D64as_v4           | 64 | 256 | 512 | 32 | 128000 / 1020 (1600) | 128 000/2 000 | 80000 / 1200 | 80 000/2 000 | 8 | 32000 | 
| Standard_D96as_v4           | 96 | 384 | 768 | 32 | 192000 / 1020 (2400) | 192 000/2 000 | 80000 / 1200 | 80 000/2 000 | 8 | 40000 |

<sup>1</sup> Les machines virtuelles de la série Dasv4 peuvent [augmenter via le mode rafale](disk-bursting.md) leurs performances de disque et atteindre le maximum du mode rafale pendant au plus 30 minutes à la fois.<br>
<sup>2</sup> Les performances réseau accélérées ne peuvent être appliquées qu’à une seule carte réseau.



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
