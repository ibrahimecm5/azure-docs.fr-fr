---
title: Séries Edv4 et Edsv4
description: Spécifications relatives aux machines virtuelles des séries Ev4, Edv4, Esv4 et Edsv4.
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: f4138c2b2835ede93e0a33693f49ed75ee6bb499
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449086"
---
# <a name="edv4-and-edsv4-series"></a>Séries Edv4 et Edsv4

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les séries Edv4 et Edsv4 s’exécutent sur les processeurs Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) dans une configuration hyper-thread. Idéales pour les applications d’entreprise gourmandes en mémoire, elles proposent jusqu’à 504 Gio de RAM, la [technologie Intel&reg; Turbo Boost 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), la [technologie Intel&reg; Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) et la [technologie Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Elles prennent également en charge la technologie [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Ces nouvelles tailles de machines virtuelles disposeront d’un stockage local 50 % plus volumineux ainsi que de meilleures IOPS de disque local en lecture et en écriture par rapport aux tailles [Ev3/Esv3](./ev3-esv3-series.md) avec des [machines virtuelles Gen2](./generation-2.md). Elles sont dotées d’une vitesse d’horloge de Turbo cœur de 3,4 GHz. 

## <a name="edv4-series"></a>Série Edv4

Les tailles de la série Edv4 s’exécutent sur les processeurs Intel &reg;​​Xeon&reg; Platinum 8272CL (Cascade Lake). Les tailles de machine virtuelle Edv4 proposent jusqu’à 504 Gio de RAM, en plus du stockage SSD local rapide et volumineux (jusqu’à 2 400 Gio). Ces machines virtuelles sont idéales pour les applications d’entreprise gourmandes en mémoire et les applications qui bénéficient d’un stockage local à faible latence et à haut débit. Vous pouvez associer les stockages de disque SSD Standard et HDD Standard aux machines virtuelles Edv4. 

[ACU](acu.md) : 195 - 210<br>
[Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Prise en charge de la génération de machine virtuelle](generation-2.md) : Générations 1 et 2<br>
[Mise en réseau accélérée](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge<sup>1</sup> <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Non pris en charge <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire maximal : IOPS/Mbits/s<sup>*</sup>  | Nombre max de cartes réseau|Bande passante réseau maximale (Mbits/s) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4<sup>1</sup>  | 2  | 16  | 75   | 4  | 9000/125    | 2 | 1 000  |
| Standard_E4d_v4              | 4  | 32  | 150  | 8  | 19000/250   | 2 | 2000  |
| Standard_E8d_v4              | 8  | 64  | 300  | 16 | 38000/500   | 4 | 4000  |
| Standard_E16d_v4             | 16 | 128 | 600  | 32 | 75000/1000   | 8 | 8000  |
| Standard_E20d_v4             | 20 | 160 | 750  | 32 | 94000/1250  | 8 | 10000 |
| Standard_E32d_v4             | 32 | 256 | 1200 | 32 | 150000/2000 | 8 | 16000 |
| Standard_E48d_v4             | 48 | 384 | 1800 | 32 | 225000/3000 | 8 | 24 000 |
| Standard_E64d_v4             | 64 | 504 | 2 400 | 32 | 300000/4000 | 8 | 30000 |

<sup>*</sup> Ces valeurs IOPS peuvent être atteintes avec des [machines virtuelles Gen2](generation-2.md)
<sup>1</sup> L’accélération réseau peut uniquement être appliquée à une seule carte d’interface réseau. <br>

## <a name="edsv4-series"></a>Série Edsv4

Les tailles de la série Edsv4 s’exécutent sur les processeurs Intel &reg;​​Xeon&reg; Platinum 8272CL (Cascade Lake). Les tailles de machine virtuelle Edsv4 proposent jusqu’à 504 Gio de RAM, en plus du stockage SSD local rapide et volumineux (jusqu’à 2 400 Gio). Ces machines virtuelles sont idéales pour les applications d’entreprise gourmandes en mémoire et les applications qui bénéficient d’un stockage local à faible latence et à haut débit.

[ACU](acu.md) : 195-210<br>
[Stockage Premium](premium-storage-performance.md) : Pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Prise en charge de la génération de machine virtuelle](generation-2.md) : Générations 1 et 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire maximal : IOPS/Mbits/s<sup>*</sup> | Débit du disque non mis en cache max. : IOPS/Mbits/s | Débit du disque maximal de rafale non mis en cache : IOPS/Mo/s<sup>1</sup> | Nombre max de cartes réseau|Bande passante réseau maximale (Mbits/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4<sup>4</sup>    | 2  | 16  | 75   | 4  | 9000/125    | 3 200/48    | 4 000/200   | 2 | 1 000  |
| Standard_E4ds_v4                | 4  | 32  | 150  | 8  | 19000/250   | 6 400/96    | 8 000/200   | 2 | 2000  |
| Standard_E8ds_v4                | 8  | 64  | 300  | 16 | 38000/500   | 12 800/192  | 16 000/400  | 4 | 4000  |
| Standard_E16ds_v4               | 16 | 128 | 600  | 32 | 75000/1000   | 25 600/384  | 32 000/800  | 8 | 8000  |
| Standard_E20ds_v4               | 20 | 160 | 750  | 32 | 94000/1250  | 32 000/480  | 40 000/1 000 | 8 | 10000 |
| Standard_E32ds_v4               | 32 | 256 | 1200 | 32 | 150000/2000 | 51 200/768  | 64 000/1 600 | 8 | 16000 |
| Standard_E48ds_v4               | 48 | 384 | 1800 | 32 | 225000/3000 | 76 800/1152 | 80 000/2 000 | 8 | 24 000 |
| Standard_E64ds_v4 <sup>2</sup>  | 64 | 504 | 2 400 | 32 | 300000/4000 | 80 000/1 200 | 80 000/2 000 | 8 | 30000 |
| Standard_E80ids_v4 <sup>3</sup> | 80 | 504 | 2 400 | 64 | 375000/4000 | 80 000/1 200 | 80 000/2 000 | 8 | 30000 |

<sup>*</sup> Ces valeurs IOPS peuvent être garanties avec des [machines virtuelles de deuxième génération](generation-2.md)<br>
<sup>1</sup> Les machines virtuelles de la série Edsv4 peuvent [augmenter via le mode rafale](./disk-bursting.md) leurs performances de disque et atteindre le maximum du mode rafale pendant au plus 30 minutes à la fois.<br>
<sup>2</sup> [Tailles avec contraintes de cœurs disponibles)](./constrained-vcpu.md).<br>
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
