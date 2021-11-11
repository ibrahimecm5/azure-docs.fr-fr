---
title: Séries Ev4 et Esv4 – Azure Virtual Machines
description: Spécifications pour les machines virtuelles des séries Ev4 et Esv4.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: c5ad35114edaae42019e0949cc4ad8aa4b291ec6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441188"
---
# <a name="ev4-and-esv4-series"></a>Séries Ev4 et Esv4

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les séries Ev4 et Esv4 s’exécutent sur les processeurs Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) dans une configuration hyper-thread. Elles sont idéales pour diverses applications d’entreprise utilisant beaucoup de mémoire et offrent jusqu’à 504 Gio de RAM. Elles sont dotées d’une vitesse d’horloge de Turbo cœur de 3,4 GHz.

> [!NOTE]
> Pour accéder à la FAQ, consultez [Tailles de machines virtuelles Azure sans disque temporaire local](azure-vms-no-temp-disk.yml).

## <a name="ev4-series"></a>Série Ev4

Les tailles de la série Ev4 s’exécutent sur Intel Xeon&reg; Platinum 8272CL (Cascade Lake). Ces instances sont idéales pour les applications d’entreprise utilisant beaucoup de mémoire. Les machines virtuelles de cette série sont dotées de la technologie Intel&reg; Hyper-Threading.

Le stockage sur disque de données à distance est facturé séparément des machines virtuelles. Pour utiliser des disques Stockage Premium, choisissez les tailles Esv4. Les tarifs et compteurs de facturation de ces tailles sont identiques à ceux de la série Ev4.

[ACU](acu.md) : 195 - 210<br>
[Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Génération 1<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Non pris en charge <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Nombre max de cartes réseau|Bande passante réseau attendue (Mbits/s) |
|---|---|---|---|---|---|---|
| Standard_E2_v4<sup>1</sup>  | 2 | 16   | Stockage distant uniquement | 4 | 2|5 000  |
| Standard_E4_v4  | 4 | 32  | Stockage distant uniquement | 8 | 2|10000  |
| Standard_E8_v4  | 8 | 64 | Stockage distant uniquement | 16 | 4|12 500 |
| Standard_E16_v4 | 16 | 128 | Stockage distant uniquement | 32 | 8|12 500 |
| Standard_E20_v4 | 20 | 160 | Stockage distant uniquement | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | Stockage distant uniquement | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | Stockage distant uniquement | 32 | 8|24 000 |
| Standard_E64_v4 | 64 | 504 | Stockage distant uniquement | 32| 8|30000 |

<sup>1</sup> La mise en réseau accélérée ne peut être appliquée qu’à une seule carte réseau. 


## <a name="esv4-series"></a>Série Esv4

Les tailles de la série Esv4 s’exécutent sur Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). Ces instances sont idéales pour les applications d’entreprise utilisant beaucoup de mémoire. Les machines virtuelles de cette série sont dotées de la technologie Intel&reg; Hyper-Threading. Le stockage sur disque de données à distance est facturé séparément des machines virtuelles.

[ACU](acu.md) : 195-210<br>
[Stockage Premium](premium-storage-performance.md) : Pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Prise en charge de la génération de machine virtuelle](generation-2.md) : Générations 1 et 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Non pris en charge <br>
<br>


| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit du disque non mis en cache max. : IOPS/Mbits/s | Débit du disque maximal de rafale non mis en cache : IOPS/Mo/s<sup>1</sup> |Nombre max de cartes réseau|Bande passante réseau attendue (Mbits/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v4<sup>4</sup>  | 2 | 16  | Stockage distant uniquement | 4 | 3 200/48 | 4 000/200 | 2|5 000  |
| Standard_E4s_v4  | 4 | 32  | Stockage distant uniquement | 8 | 6 400/96 | 8 000/200 | 2|10000  |
| Standard_E8s_v4  | 8 | 64  | Stockage distant uniquement | 16 | 12 800/192 | 16 000/400 | 4|12 500 |
| Standard_E16s_v4 | 16 | 128 | Stockage distant uniquement | 32 | 25 600/384 | 32 000/800 | 8|12 500 |
| Standard_E20s_v4 | 20 | 160 | Stockage distant uniquement | 32 | 32 000/480  | 40 000/1 000 | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | Stockage distant uniquement | 32 | 51 200/768  | 64 000/1 600 | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | Stockage distant uniquement | 32 | 76 800/1152 | 80 000/2 000 | 8|24 000 |
| Standard_E64s_v4 <sup>2</sup> | 64 | 504| Stockage distant uniquement | 32 | 80 000/1 200 | 80 000/2 000 | 8|30000 |
| Standard_E80is_v4 <sup>3</sup> | 80 | 504 | Stockage distant uniquement | 64 | 80 000/1 200 | 80 000/2 000 | 8|30000 |

<sup>1</sup> Les machines virtuelles de la série Esv4 peuvent [augmenter](./disk-bursting.md) leurs performances de disque et atteindre leur maximum de bursting pendant 30 minutes à la fois.<br>
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
