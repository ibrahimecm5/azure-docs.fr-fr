---
title: 'Séries Dv5 et Dsv5 : Machines virtuelles Azure'
description: Spécifications pour les machines virtuelles des séries Dv5 et Dsv5.
author: styli365
ms.author: sttsinar
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: e8297681a102455134bff09ef30afb91acf920fa
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479019"
---
# <a name="dv5-and-dsv5-series"></a>Séries Dv5 et Dsv5

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les séries de machines virtuelles Dv5 et Dsv5 s’exécutent sur les processeurs Intel&reg; ​​Xeon&reg; Platinum 8370C (Ice Lake) de 3e génération dans une configuration d’[hyperthreading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html), offrant une meilleure proposition de valeur pour la plupart des charges de travail universelles. Ce nouveau processeur a les caractéristiques suivantes : vitesse d’horloge Turbo avec tous les cœurs de 3,5 GHz, avec la technologie [Intel&reg; Turbo Boost](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Advanced-Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) et [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Ces machines virtuelles offrent une combinaison de processeurs virtuels et de mémoire permettant de répondre aux exigences associées à la plupart des charges de travail d’entreprise, comme les bases de données de taille petite à moyenne, les serveurs web avec un trafic faible à moyen, les serveurs d’applications, etc. Les séries Dv5 et Dsv5 constituent une meilleure proposition de valeur pour les charges de travail ne nécessitant pas de disque temporaire local. .

> [!NOTE]
> Pour accéder à la FAQ, consultez [Tailles de machines virtuelles Azure sans disque temporaire local](azure-vms-no-temp-disk.yml).
## <a name="dv5-series"></a>Série Dv5

Les machines virtuelles de la série Dv5 s’exécutent sur le processeur Intel® Xeon® Platinum 8370C (Ice Lake) de 3e génération, atteignant une vitesse d’horloge Turbo avec tous les cœurs jusqu’à 3,5 GHz.  Ces machines virtuelles offrent jusqu’à 96 processeurs virtuels et 384 Gio de RAM.  Les machines virtuelles de la série Dv5 fournissent une meilleure proposition de valeur pour la plupart des charges de travail à usage général par rapport à la génération précédente (par exemple, une évolutivité accrue et une classe d’UC mise à niveau).

Les tailles de machine virtuelle de la série Dv5 n’ont aucun stockage temporaire, ce qui réduit le prix de base.  Vous pouvez associer les stockages de disque SSD Standard et HDD Standard à ces machines virtuelles. Pour utiliser un stockage SSD Premium ou Disque Ultra, sélectionnez des machines virtuelles de la série Dsv5. Le stockage sur disque de données est facturé séparément des machines virtuelles. [Consultez les prix des disques](https://azure.microsoft.com/pricing/details/managed-disks/).

[Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Générations 1 et 2<br>
[Accélération réseau](../virtual-network/create-vm-accelerated-networking-cli.md) : Obligatoire <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Non pris en charge <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Nombre max de cartes réseau|Bande passante réseau maximale (Mbits/s) |
|---|---|---|---|---|---|---|
| Standard_D2_v5<sup>1, 2</sup> | 2  | 8   | Stockage distant uniquement | 4  | 2 | 12 500 |
| Standard_D4_v5                | 4  | 16  | Stockage distant uniquement | 8  | 2 | 12 500 |
| Standard_D8_v5                | 8  | 32  | Stockage distant uniquement | 16 | 4 | 12 500 |
| Standard_D16_v5               | 16 | 64  | Stockage distant uniquement | 32 | 8 | 12 500 |
| Standard_D32_v5               | 32 | 128 | Stockage distant uniquement | 32 | 8 | 16000 |
| Standard_D48_v5               | 48 | 192 | Stockage distant uniquement | 32 | 8 | 24 000 |
| Standard_D64_v5               | 64 | 256 | Stockage distant uniquement | 32 | 8 | 30000 |
| Standard_D96_v5               | 96 | 384 | Stockage distant uniquement | 32 | 8 | 35000 |

<sup>1</sup> L’accélération réseau est impérative et activée par défaut sur toutes les machines virtuelles Dv5.<br>
<sup>2</sup> L’accélération réseau peut être appliquée à deux cartes réseau.

## <a name="dsv5-series"></a>Série Dsv5

Les machines virtuelles de la série Dsv5 s’exécutent sur le processeur Intel® Xeon® Platinum 8370C (Ice Lake) de 3e génération, atteignant une vitesse d’horloge Turbo avec tous les cœurs jusqu’à 3,5 GHz.  Ces machines virtuelles offrent jusqu’à 96 processeurs virtuels et 384 Gio de RAM.  Les machines virtuelles de la série Dsv5 fournissent une meilleure proposition de valeur pour la plupart des charges de travail à usage général par rapport à la génération précédente (par exemple, une évolutivité accrue et une classe d’UC mise à niveau).

Les tailles de machine virtuelle de la série Dsv5 n’ont aucun stockage temporaire, ce qui réduit le prix de base.  Vous pouvez attacher des stockages sur disque SSD Standard, HDD Standard et SSD Premium à ces machines virtuelles. Vous pouvez également attacher un stockage Disque Ultra en fonction de sa disponibilité régionale. Le stockage sur disque de données est facturé séparément des machines virtuelles. [Consultez les prix des disques](https://azure.microsoft.com/pricing/details/managed-disks/).

[Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Générations 1 et 2<br>
[Accélération réseau](../virtual-network/create-vm-accelerated-networking-cli.md) : Obligatoire <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Non pris en charge <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit du disque non mis en cache max. : IOPS/Mbits/s | Débit maximal du disque sans mise en cache avec mode rafale : IOPS/Mbits/s<sup>3</sup> | Nombre max de cartes réseau | Bande passante réseau maximale (Mbits/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v5<sup>1,2</sup> | 2  | 8   | Stockage distant uniquement | 4  | 3 750/85    | 10 000/1 200 | 2 | 12 500 |
| Standard_D4s_v5               | 4  | 16  | Stockage distant uniquement | 8  | 6 400/145   | 20 000/1 200 | 2 | 12 500 |
| Standard_D8s_v5               | 8  | 32  | Stockage distant uniquement | 16 | 12 800/290  | 20 000/1 200 | 4 | 12 500 |
| Standard_D16s_v5              | 16 | 64  | Stockage distant uniquement | 32 | 25 600/600  | 40 000/1 200 | 8 | 12 500 |
| Standard_D32s_v5              | 32 | 128 | Stockage distant uniquement | 32 | 51 200/865  | 80 000/2 000 | 8 | 16000 |
| Standard_D48s_v5              | 48 | 192 | Stockage distant uniquement | 32 | 76 800/1 315 | 80 000/3 000 | 8 | 24 000 |
| Standard_D64s_v5              | 64 | 256 | Stockage distant uniquement | 32 | 80 000/1 735 | 80 000/3 000 | 8 | 30000 |
| Standard_D96s_v5              | 96 | 384 | Stockage distant uniquement | 32 | 80 000/2 600 | 80 000/4 000 | 8 | 35000 |

<sup>1</sup> L’accélération réseau est impérative et activée par défaut sur toutes les machines virtuelles Dsv5.<br>
<sup>2</sup> L’accélération réseau peut être appliquée à deux cartes réseau.<br>
<sup>3</sup> Les machines virtuelles de la série Dsv5 peuvent disposer d’un [bursting](disk-bursting.md) accélérant les performances de leurs disques et atteignant le niveau maximal du bursting pendant 30 minutes d’affilée.

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
