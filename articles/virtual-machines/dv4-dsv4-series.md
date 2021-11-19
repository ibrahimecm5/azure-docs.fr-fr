---
title: Séries Dv4 et Dsv4 - Machines virtuelles Microsoft Azure
description: Spécifications pour les machines virtuelles des séries Dv4 et Dsv4.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: b86da0ddee576e654433b70cbb902daa6916c776
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401437"
---
# <a name="dv4-and-dsv4-series"></a>Séries Dv4 et Dsv4

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les séries Dv4 et Dsv4 s’exécutent sur les processeurs Intel&reg; ​​Xeon&reg; Platinum 8272CL (Cascade Lake) dans une configuration de type « Hyper-Threading » qui apporte davantage de valeur ajoutée à la plupart des charges de travail universelles. Caractéristiques : vitesse d’horloge Turbo continue de 3,4 GHz, [technologie Intel&reg; Turbo Boost 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [technologie Intel&reg; Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) et [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Elles prennent également en charge la technologie [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). 

> [!NOTE]
> Pour accéder à la FAQ, consultez [Tailles de machines virtuelles Azure sans disque temporaire local](azure-vms-no-temp-disk.yml).

## <a name="dv4-series"></a>Série Dv4

Les tailles de la série Dv4 s’exécutent sur Intel &reg;​​Xeon&reg; Platinum 8272CL (Cascade Lake). Elles offrent une combinaison de processeur virtuel, de mémoire et d’options de stockage distant adaptée à la plupart des charges de travail de production. Les machines virtuelles de série Dv4 sont dotées de la technologie Hyper-Threading d’[Intel&reg;](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).


Le stockage sur disque de données à distance est facturé séparément des machines virtuelles. Pour utiliser les disques de stockage Premium, utilisez des machines virtuelles au format Dsv4. Les tarifs et les compteurs de facturation de ces tailles sont identiques à ceux de la série Dv4.

> [!NOTE]
> Après un redémarrage, un fichier nommé *Data_loss_warning.txt* peut apparaître à côté du lecteur C (le premier disque de données attaché à partir du Portail Azure). Dans ce scénario, malgré le nom de fichier, aucune perte de données ne s’est produite sur le disque. En général, le fichier *Data_loss_warning.txt* est copié sur le lecteur temporaire. Si vous utilisez une machine virtuelle qui n’a pas de lecteur Temp, WindowsAzureGuestAgent copie incorrectement le fichier sur la première lettre de lecteur. Dans les machines virtuelles v4, la première lettre de lecteur est un disque de données.
>
> Une résolution de ce problème a été appliquée dans la version la plus récente (version 2.7.41491.999) de l’agent de machine virtuelle.

[ACU](acu.md) : 195-210<br>
[Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Prise en charge de la génération de machine virtuelle](generation-2.md) : Générations 1 et 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Non pris en charge <br>
[Virtualisation imbriquée](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) : prise en charge <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Nombre max de cartes réseau|Bande passante réseau attendue (Mbit/s) |
|---|---|---|---|---|---|---|
| Standard_D2_v4<sup>1</sup> | 2 | 8 | Stockage distant uniquement | 4 | 2|5 000 |
| Standard_D4_v4 | 4 | 16  | Stockage distant uniquement | 8 | 2|10000 |
| Standard_D8_v4 | 8 | 32 | Stockage distant uniquement | 16 | 4|12 500 |
| Standard_D16_v4 | 16 | 64 | Stockage distant uniquement | 32 | 8|12 500 |
| Standard_D32_v4 | 32 | 128 | Stockage distant uniquement | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | Stockage distant uniquement | 32 | 8|24 000 |
| Standard_D64_v4 | 64 | 256 | Stockage distant uniquement | 32 | 8|30000 |

<sup>1</sup>La mise en réseau accélérée ne peut être appliquée qu’à une seule carte réseau. 


## <a name="dsv4-series"></a>Série Dsv4

Les tailles de la série Dsv4 s’exécutent sur Intel &reg;​​Xeon&reg; Platinum 8272CL (Cascade Lake). Elles offrent une combinaison de processeur virtuel, de mémoire et d’options de stockage distant adaptée à la plupart des charges de travail de production. Les machines virtuelles de série Dsv4 sont dotées de la technologie Hyper-Threading d’[Intel&reg;](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). Le stockage sur disque de données à distance est facturé séparément des machines virtuelles.

[ACU](acu.md) : 195-210<br>
[Stockage Premium](premium-storage-performance.md) : Pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Prise en charge de la génération de machine virtuelle](generation-2.md) : Générations 1 et 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge<br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Non pris en charge <br>
[Virtualisation imbriquée](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) : prise en charge <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit du disque non mis en cache max. : IOPS/Mbits/s | Débit du disque maximal de rafale non mis en cache : IOPS/Mo/s<sup>1</sup> | Nombre max de cartes réseau|Bande passante réseau attendue (Mbit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v4<sup>2</sup> | 2 | 8  | Stockage distant uniquement | 4 | 3 200/48 | 4 000/200 |2|5 000 |
| Standard_D4s_v4 | 4 | 16 | Stockage distant uniquement | 8 | 6 400/96 | 8 000/200 |2|10000 |
| Standard_D8s_v4 | 8 | 32 | Stockage distant uniquement | 16 | 12 800/192 | 16 000/400 |4|12 500 |
| Standard_D16s_v4 | 16 | 64  | Stockage distant uniquement | 32 | 25 600/384 | 32 000/800 |8|12 500 |
| Standard_D32s_v4 | 32 | 128 | Stockage distant uniquement | 32 | 51 200/768 | 64 000/1 600 |8|16000 |
| Standard_D48s_v4 | 48 | 192 | Stockage distant uniquement | 32 | 76 800/1152 | 80 000/2 000 |8|24 000 |
| Standard_D64s_v4 | 64 | 256 | Stockage distant uniquement | 32 | 80 000/1 200 | 80 000/2 000 |8|30000 |

<sup>1</sup> Les machines virtuelles de la série Dsv4 peuvent [augmenter via le mode rafale](./disk-bursting.md) leurs performances de disque et atteindre le maximum du mode rafale pendant au plus 30 minutes à la fois.<br>
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

Pour plus d’informations sur les types de disques, consultez [Quels sont les types de disque disponibles dans Azure ?](disks-types.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
