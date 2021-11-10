---
title: Séries Eav4 et Easv4
description: Spécifications pour les machines virtuelles des séries Eav4 et Easv4.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: ayshak
ms.reviewer: jushiman
ms.openlocfilehash: 5cefbdb7cdb3d44c08c61889d3a6420d31729989
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471741"
---
# <a name="eav4-and-easv4-series"></a>Séries Eav4 et Easv4

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les machines de séries Eav4 et Easv4 utilisent le processeur EPYC<sup>TM</sup> 7452 2,35 Ghz d’AMD dans une configuration multithread avec un cache L3 jusqu’à 256 Mo, ce qui améliore les options d’exécution de la plupart des charges de travail à mémoire optimisée. Les séries Eav4 et Easv4 ont les mêmes configurations de mémoire et de disque que les séries Ev3 et Esv3.

## <a name="eav4-series"></a>Série Eav4

[ACU](acu.md) : 230 - 260<br>
[Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Générations 1 et 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge <br>
<br>

Les tailles de la série Eav4 sont basées sur le processeur AMD EPYC<sup>TM</sup> 7452 2,35 GHz, qui peut atteindre une fréquence maximale renforcée de 3,35 GHz. Les tailles des machines virtuelles de la série Eav4 sont idéales pour les applications d’entreprise nécessitant une mémoire importante. Le stockage sur disque de données est facturé séparément des machines virtuelles. Pour utiliser un SSD Premium, utilisez les tailles de la série Easv4. Les tarifs et les compteurs de facturation pour les tailles Easv4 sont identiques à celles de la série Eav3.

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire max. : IOPS / MBps en lecture / MBps en écriture | Nombre max de cartes réseau | Bande passante réseau attendue (Mbits/s) |
| -----|-----|-----|-----|-----|-----|-----|-----|
| Standard\_E2a\_v4<sup>1</sup>|2|16|50|4|3000 / 46 / 23|2 | 800 |
| Standard\_E4a\_v4|4|32|100|8|6000 / 93 / 46|2 | 1 600 |
| Standard\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4 | 3200 |
| Standard\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 | 6 400 |
| Standard\_E20a\_v4|20|160|500|32|30000 / 468 / 234|8 | 8000 |
| Standard\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 | 12800 |
| Standard\_E48a\_v4|48|384|1200|32|96000 / 1000 (500)|8 | 19 200 |
| Standard\_E64a\_v4|64|512|1 600|32|96000 / 1000 (500)|8 | 25600 |
| Standard\_E96a\_v4|96|672|2 400|32|96000 / 1000 (500)|8 | 32000 |

<sup>1</sup> Les performances réseau accélérées ne peuvent être appliquées qu’à une seule carte réseau. 


## <a name="easv4-series"></a>Série Easv4

[ACU](acu.md) : 230 - 260<br>
[Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Générations 1 et 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge <br>
<br>

Les tailles de la série Easv4 sont basées sur le processeur AMD EPYC<sup>TM</sup> 7452 de 2,35 Ghz, qui peut atteindre une fréquence maximale renforcée de 3,35 GHz et utiliser un SSD Premium. Les tailles des machines virtuelles de la série Easv4 sont idéales pour les applications d’entreprise nécessitant une mémoire importante.

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit de stockage temporaire et débit maximal de rafale mis en cache : IOPS / Mbits/s<sup>1</sup> | Débit du disque non mis en cache max. : IOPS / MBps | Débit du disque maximal de rafale non mis en cache : IOPS/Mo/s<sup>1</sup> | Nombre max de cartes réseau | Bande passante réseau attendue (Mbits/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4<sup>3</sup>|2|16|32|4|4000 / 32 (50)| 4 000/100 |3200 / 48| 4 000/200 |2 | 800 |
| Standard_E4as_v4 <sup>2</sup>|4|32|64|8|8000 / 64 (100)| 8 000/200 |6400 / 96| 8 000/200 |2 | 1 600 |
| Standard_E8as_v4 <sup>2</sup>|8|64|128|16|16000 / 128 (200)| 16 000/400 |12800 / 192| 16 000/400 |4 | 3200 |
| Standard_E16as_v4 <sup>2</sup>|16|128|256|32|32 000 / 255 (400)| 32 000/800 |25600 / 384| 32 000/800 |8 | 6 400 |
| Standard_E20as_v4|20|160|320|32|40000 / 320 (500)| 40 000/1 000 |32000 / 480| 40 000/1 000 |8 | 8000 |
| Standard_E32as_v4<sup>2</sup>|32|256|512|32|64 000 / 510 (800)| 64 000/1 600 |51200 / 768| 64 000/1 600 |8 | 12800 |
| Standard_E48as_v4|48|384|768|32|96000 / 1020 (1200)| 96 000/2 000 |76800 / 1148| 80 000/2 000 |8 | 19 200 |
| Standard_E64as_v4<sup>2</sup>|64|512|1 024|32|128000 / 1020 (1600)| 128 000/2 000 |80000 / 1200| 80 000/2 000 |8 | 25600 |
| Standard_E96as_v4 <sup>2</sup>|96|672|1344|32|192000 / 1020 (2400)| 192 000/2 000 |80000 / 1200| 80 000/2 000 |8 | 32000 |

<sup>1</sup> Les machines virtuelles de la série Easv4 peuvent [augmenter via le mode rafale](./disk-bursting.md) leurs performances de disque et atteindre le maximum du mode rafale pendant au plus 30 minutes à la fois. <br>
<sup>2</sup> [Tailles avec contraintes de cœurs disponibles](./constrained-vcpu.md). <br>
<sup>3</sup> Les performances réseau accélérées ne peuvent être appliquées qu’à une seule carte réseau. 


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
