---
title: Séries Ev5 et Esv5 – Machines virtuelles Azure
description: Spécifications pour les machines virtuelles des séries Ev5 et Esv5.
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 897ae578ca1ff088e2f3f937bb9f00977ee233ad
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399349"
---
# <a name="ev5-and-esv5-series"></a>Séries Ev5 et Esv5

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les machines virtuelles séries Ev5 et Esv5 s’exécutent sur les processeurs Intel&reg; ​​Xeon&reg; Platinum 8370C (Ice Lake) de 3e génération dans une configuration [d’hyperthreading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html), offrant une meilleure proposition de valeur pour la plupart des charges de travail universelles. Ce nouveau processeur a les caractéristiques suivantes : vitesse d’horloge Turbo avec tous les cœurs de 3,5 GHz, avec la technologie [Intel&reg; Turbo Boost](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Advanced-Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) et [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Avec jusqu’à 672 Gio de RAM, ces machines virtuelles sont idéales pour les applications d’entreprise gourmandes en mémoire, les serveurs de bases de données relationnelles et les charges de travail d’analytique en mémoire. Les séries Ev5 et Esv5 constituent une meilleure proposition de valeur pour les charges de travail ne nécessitant pas de disque temporaire local. Pour plus d’informations sur les machines virtuelles similaires avec un disque local, consultez [Machines virtuelles séries Edv5 et Edsv5](edv5-edsv5-series.md).

> [!NOTE]
> Pour accéder à la FAQ, consultez [Tailles de machines virtuelles Azure sans disque temporaire local](azure-vms-no-temp-disk.yml).

## <a name="ev5-series"></a>Série Ev5

Les machines virtuelles de la série Ev5 s’exécutent sur le processeur Intel® Xeon® Platinum 8370C (Ice Lake) de 3e génération, atteignant une vitesse d’horloge Turbo avec tous les cœurs jusqu’à 3,5 GHz.  Ces machines virtuelles offrent jusqu’à 104 processeurs virtuels et 672 Gio de RAM. Les tailles de machine virtuelle de la série Ev5 n’ont aucun stockage temporaire, ce qui réduit le prix de base.

La série Ev5 prend en charge les types de disques SSD Standard et HDD Standard. Pour utiliser un stockage SSD Premium ou SSD Ultra, sélectionnez des machines virtuelles de la série Esv5. Le stockage sur disque de données est facturé séparément des machines virtuelles. [Consultez les prix des disques](https://azure.microsoft.com/pricing/details/managed-disks/).

[Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Générations 1 et 2<br>
[Accélération réseau](../virtual-network/create-vm-accelerated-networking-cli.md) : Obligatoire <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Non pris en charge <br>
[Virtualisation imbriquée](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) : prise en charge <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Nombre max de cartes réseau|Bande passante réseau maximale (Mbits/s) |
|---|---|---|---|---|---|---|
| Standard_E2_v5<sup>1,2</sup>  | 2   | 16  | Stockage distant uniquement | 4  | 2 | 12 500 |
| Standard_E4_v5                | 4   | 32  | Stockage distant uniquement | 8  | 2 | 12 500 |
| Standard_E8_v5                | 8   | 64  | Stockage distant uniquement | 16 | 4 | 12 500 |
| Standard_E16_v5               | 16  | 128 | Stockage distant uniquement | 32 | 8 | 12 500 |
| Standard_E20_v5               | 20  | 160 | Stockage distant uniquement | 32 | 8 | 12 500  |
| Standard_E32_v5               | 32  | 256 | Stockage distant uniquement | 32 | 8 | 16000  |
| Standard_E48_v5               | 48  | 384 | Stockage distant uniquement | 32 | 8 | 24 000  |
| Standard_E64_v5               | 64  | 512 | Stockage distant uniquement | 32 | 8 | 30000  |
| Standard_E96_v5               | 96  | 672 | Stockage distant uniquement | 32 | 8 | 30000  |
| Standard_E104i_v5<sup>3</sup> | 104 | 672 | Stockage distant uniquement | 64 | 8 | 100000 |

<sup>1</sup> L’accélération réseau est impérative et activée par défaut sur toutes les machines virtuelles Ev5.<br>
<sup>2</sup> L’accélération réseau peut être appliquée à deux cartes réseau.<br>
<sup>3</sup> L’instance est [isolée](../security/fundamentals/isolation-choices.md#compute-isolation) sur un matériel dédié à un seul client.<br>

## <a name="esv5-series"></a>Série Esv5

Les machines virtuelles de la série Esv5 s’exécutent sur le processeur Intel® Xeon® Platinum 8370C (Ice Lake) de 3e génération, atteignant une vitesse d’horloge Turbo avec tous les cœurs jusqu’à 3,5 GHz.  Ces machines virtuelles offrent jusqu’à 104 processeurs virtuels et 672 Gio de RAM. Les tailles de machine virtuelle de la série Esv5 n’ont aucun stockage temporaire, ce qui réduit le prix de base.

La série Esv5 prend en charge les types de disques Ssd Standard, Hdd Standard et Ssd Premium. Vous pouvez également attacher un stockage Disque Ultra en fonction de sa disponibilité régionale. Le stockage sur disque de données est facturé séparément des machines virtuelles. [Consultez les prix des disques](https://azure.microsoft.com/pricing/details/managed-disks/).

[Stockage Premium](premium-storage-performance.md) : Pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Générations 1 et 2<br>
[Accélération réseau](../virtual-network/create-vm-accelerated-networking-cli.md) : Obligatoire <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Non pris en charge <br>
[Virtualisation imbriquée](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) : prise en charge <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit du disque non mis en cache max. : IOPS/Mbits/s | Débit maximal du disque sans mise en cache avec mode rafale (IOPS/Mbits/s)<sup>5</sup> | Nombre max de cartes réseau | Bande passante réseau maximale (Mbits/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v5<sup>1,2</sup>  | 2   | 16  | Stockage distant uniquement | 4  | 3 750/85     | 10 000/1 200 | 2 | 12 500 |
| Standard_E4s_v5                | 4   | 32  | Stockage distant uniquement | 8  | 6 400/145    | 20 000/1 200 | 2 | 12 500 |
| Standard_E8s_v5                | 8   | 64  | Stockage distant uniquement | 16 | 12 800/290   | 20 000/1 200 | 4 | 12 500 |
| Standard_E16s_v5               | 16  | 128 | Stockage distant uniquement | 32 | 25 600/600   | 40 000/1 200 | 8 | 12 500 |
| Standard_E20s_v5               | 20  | 160 | Stockage distant uniquement | 32 | 32 000/750   | 64 000/1 600 | 8 | 12 500  |
| Standard_E32s_v5               | 32  | 256 | Stockage distant uniquement | 32 | 51 200/865   | 80 000/2 000 | 8 | 16000  |
| Standard_E48s_v5               | 48  | 384 | Stockage distant uniquement | 32 | 76 800/1 315  | 80 000/3 000 | 8 | 24 000  |
| Standard_E64s_v5               | 64  | 512 | Stockage distant uniquement | 32 | 80 000/1 735  | 80 000/3 000 | 8 | 30000  |
| Standard_E96s_v5<sup>3</sup>   | 96  | 672 | Stockage distant uniquement | 32 | 80 000/2 600  | 80 000/4 000 | 8 | 35000  |
| Standard_E104is_v5<sup>4</sup> | 104 | 672 | Stockage distant uniquement | 64 | 120 000/4 000 | 120 000/4 000 | 8 | 100000 |

<sup>1</sup> L’accélération réseau est impérative et activée par défaut sur toutes les machines virtuelles Esv5.<br>
<sup>2</sup> L’accélération réseau peut être appliquée à deux cartes réseau.<br>
<sup>3</sup> Tailles avec [contraintes de cœurs](constrained-vcpu.md) disponibles.<br>
<sup>4</sup> L’instance est [isolée](../security/fundamentals/isolation-choices.md#compute-isolation) sur un matériel dédié à un seul client.<br>
<sup>5</sup> Les machines virtuelles de la série Esv5 peuvent disposer d’un [bursting](disk-bursting.md) des performances de leurs disques et atteindre le niveau maximal de bursting pendant 30 minutes d’affilée.

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
