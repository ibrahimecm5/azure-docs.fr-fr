---
title: Séries Ddv4 et Ddsv4
description: Spécifications relatives aux machines virtuelles des séries Dv4, Ddv4, Dsv4 et Ddsv4.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 0ac7719d108ab888b5573d03a8c45d545bcb135d
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398665"
---
# <a name="ddv4-and-ddsv4-series"></a>Séries Ddv4 et Ddsv4

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les séries Ddv4 et Ddsv4 s'exécutent sur les processeurs Intel&reg; ​​Xeon&reg; Platinum 8272CL (Cascade Lake) dans une configuration de type « Hyper-Threading » qui apporte davantage de valeur ajoutée à la plupart des charges de travail à usage général. Caractéristiques : vitesse d’horloge Turbo continue de 3,4 GHz, [technologie Intel&reg; Turbo Boost 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [technologie Intel&reg; Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) et [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Elles prennent également en charge la technologie [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Ces nouvelles tailles de machines virtuelles disposeront d'un stockage local 50 % plus volumineux, ainsi que de meilleures IOPS de disque local en lecture et en écriture par rapport aux tailles [Dv3/Dsv3](./dv3-dsv3-series.md) avec des [machines virtuelles Gen2](./generation-2.md).

Les cas d'usage de la série D englobent les applications de classe Entreprise, les bases de données relationnelles, la mise en cache en mémoire et l'analytique.

## <a name="ddv4-series"></a>Série Ddv4

Les tailles de la série Ddv4 s'exécutent sur Intel &reg;​​Xeon&reg; Platinum 8272CL (Cascade Lake). La série Ddv4 offre une combinaison de processeurs virtuels, de mémoire et de disque temporaire adaptée à la plupart des charges de travail de production.

Les nouvelles tailles de machines virtuelles Ddv4 incluent un stockage SSD local rapide et plus volumineux (2 400 Gio maximum) et sont conçues pour les applications qui bénéficient d'un stockage local à faible latence et à haut débit, comme les applications qui nécessitent des lectures/écritures rapides sur un espace de stockage temporaire ou qui requièrent un espace de stockage temporaire pour les caches ou les fichiers temporaires. Vous pouvez associer les stockages SSD Standard et HDD Standard aux machines virtuelles Ddv4. Le stockage sur disque de données à distance est facturé séparément des machines virtuelles.

[ACU](acu.md) : 195-210<br>
[Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Prise en charge de la génération de machine virtuelle](generation-2.md) : Générations 1 et 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge<br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge <br>
[Virtualisation imbriquée](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) : prise en charge <br>
<br> 

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire maximal : IOPS/Mbits/s<sup>*</sup> | Nombre max de cartes réseau|Bande passante réseau attendue (Mbits/s) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v4<sup>1</sup> | 2  | 8   | 75   | 4  | 9000/125    | 2 | 1 000  |
| Standard_D4d_v4             | 4  | 16  | 150  | 8  | 19000/250   | 2 | 2000  |
| Standard_D8d_v4             | 8  | 32  | 300  | 16 | 38000/500   | 4 | 4000  |
| Standard_D16d_v4            | 16 | 64  | 600  | 32 | 75000/1000   | 8 | 8000  |
| Standard_D32d_v4            | 32 | 128 | 1200 | 32 | 150000/2000 | 8 | 16000 |
| Standard_D48d_v4            | 48 | 192 | 1800 | 32 | 225000/3000 | 8 | 24 000 |
| Standard_D64d_v4            | 64 | 256 | 2 400 | 32 | 300000/4000 | 8 | 30000 |




<sup>*</sup> Ces valeurs IOPS peuvent être atteintes avec des [machines virtuelles de deuxième génération](generation-2.md)<br>
<sup>1</sup>La mise en réseau accélérée ne peut être appliquée qu’à une seule carte réseau. 

## <a name="ddsv4-series"></a>Série Ddsv4

La série Ddsv4 s'exécute sur Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). La série Ddsv4 offre une combinaison de processeurs virtuels, de mémoire et de disque temporaire adaptée à la plupart des charges de travail de production.

Les nouvelles tailles de machines virtuelles Ddsv4 incluent un stockage SSD local rapide et plus volumineux (2 400 Gio maximum) et sont conçues pour les applications qui bénéficient d'un stockage local à faible latence et à haut débit, comme les applications qui nécessitent des lectures/écritures rapides sur un espace de stockage temporaire ou qui requièrent un espace de stockage temporaire pour les caches ou les fichiers temporaires. 

 > [!NOTE]
 >Les tarifs et les compteurs de facturation de ces tailles sont identiques à ceux de la série Ddv4.

[ACU](acu.md) : 195-210<br>
[Stockage Premium](premium-storage-performance.md) : Pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Prise en charge de la génération de machine virtuelle](generation-2.md) : Générations 1 et 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge<br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge <br>
[Virtualisation imbriquée](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) : prise en charge <br>
<br> 

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire maximal : IOPS/Mbits/s<sup>*</sup> | Débit du disque non mis en cache max. : IOPS/Mbits/s |  Débit du disque maximal de rafale non mis en cache : IOPS/Mo/s<sup>1</sup> | Nombre max de cartes réseau|Bande passante réseau attendue (Mbits/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4<sup>2</sup> | 2  | 8   | 75   | 4  | 9000/125    | 3 200/48    | 4 000/200   | 2 | 1 000  |
| Standard_D4ds_v4             | 4  | 16  | 150  | 8  | 19000/250   | 6 400/96    | 8 000/200   | 2 | 2000  |
| Standard_D8ds_v4             | 8  | 32  | 300  | 16 | 38000/500   | 12 800/192  | 16 000/400  | 4 | 4000  |
| Standard_D16ds_v4            | 16 | 64  | 600  | 32 | 85 000/1 000   | 25 600/384  | 32 000/800  | 8 | 8000  |
| Standard_D32ds_v4            | 32 | 128 | 1200 | 32 | 150000/2000 | 51 200/768  | 64 000/1 600 | 8 | 16000 |
| Standard_D48ds_v4            | 48 | 192 | 1800 | 32 | 225000/3000 | 76 800/1152 | 80 000/2 000 | 8 | 24 000 |
| Standard_D64ds_v4            | 64 | 256 | 2 400 | 32 | 300000/4000 | 80 000/1 200 | 80 000/2 000 | 8 | 30000 |

<sup>*</sup> Ces valeurs IOPS peuvent être atteintes avec des [machines virtuelles de deuxième génération](generation-2.md)<br>
<sup>1</sup> Les machines virtuelles de la série Ddsv4 peuvent disposer d’un [bursting](./disk-bursting.md) des performances de leurs disques et atteindre le niveau maximal de bursting pendant 30 minutes d’affilée.<br>
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
