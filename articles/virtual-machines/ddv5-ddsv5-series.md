---
title: 'Séries Ddv5 et Ddsv5 : Machines virtuelles Azure'
description: Spécifications pour les machines virtuelles des séries Ddv5 et Ddsv5.
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: f2f4cb3a9d0af5bdf5dd873151b7adc899a24295
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401988"
---
# <a name="ddv5-and-ddsv5-series"></a>Séries Ddv5 et Ddsv5

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les séries Ddv5 et Ddsv5 s’exécutent sur les processeurs Intel&reg; ​​Xeon&reg; Platinum 8370C (Ice Lake) de 3e génération dans une configuration d’[hyperthreading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html), offrant une meilleure proposition de valeur pour la plupart des charges de travail universelles. Ce nouveau processeur a les caractéristiques suivantes : vitesse d’horloge Turbo avec tous les cœurs de 3,5 GHz, avec la technologie [Intel&reg; Turbo Boost](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Advanced-Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) et [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Ces machines virtuelles offrent une combinaison de processeurs virtuels, de mémoire et de stockage temporaire permettant de répondre aux exigences associées à la plupart des charges de travail d’entreprise, comme les bases de données de taille petite à moyenne, les serveurs web avec un trafic faible à moyen, les serveurs d’applications, etc.


## <a name="ddv5-series"></a>Série Ddv5
Les machines virtuelles de la série Ddv5 s’exécutent sur le processeur Intel® Xeon® Platinum 8370C (Ice Lake) de 3e génération, atteignant une vitesse d’horloge Turbo avec tous les cœurs jusqu’à 3,5 GHz. Ces machines virtuelles offrent jusqu’à 96 processeurs virtuels et 384 Gio de RAM, ainsi qu’un stockage SSD local jusqu’à 3 600 Gio. Les machines virtuelles de la série Ddv5 fournissent une meilleure proposition de valeur pour la plupart des charges de travail à usage général par rapport à la génération précédente (par exemple, une évolutivité accrue et une classe d’UC mise à niveau). Ces machines virtuelles sont également dotées d’un stockage SSD local rapide et de grande capacité (jusqu’à 3 600 Gio).

Les machines virtuelles de la série Ddv5 prennent en charge les types de disques SSD Standard et HDD Standard. Pour utiliser un stockage SSD Premium ou Disque Ultra, sélectionnez des machines virtuelles de la série Ddsv5. Le stockage sur disque de données est facturé séparément des machines virtuelles. [Consultez les prix des disques](https://azure.microsoft.com/pricing/details/managed-disks/).


[Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Générations 1 et 2<br>
[Accélération réseau](../virtual-network/create-vm-accelerated-networking-cli.md) : Obligatoire <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge <br>
[Virtualisation imbriquée](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) : prise en charge <br>
<br> 

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit maximal du stockage temporaire et mis en cache : IOPS/Mbits/s<sup>*</sup> | Nombre max de cartes réseau|Bande passante réseau maximale (Mbits/s) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v5<sup>1,2</sup> | 2  | 8   | 75   | 4  | 9000/125    | 2 | 12 500 |
| Standard_D4d_v5               | 4  | 16  | 150  | 8  | 19000/250   | 2 | 12 500 |
| Standard_D8d_v5               | 8  | 32  | 300  | 16 | 38000/500   | 4 | 12 500 |
| Standard_D16d_v5              | 16 | 64  | 600  | 32 | 75000/1000  | 8 | 12 500 |
| Standard_D32d_v5              | 32 | 128 | 1200 | 32 | 150000/2000 | 8 | 16000 |
| Standard_D48d_v5              | 48 | 192 | 1800 | 32 | 225000/3000 | 8 | 24 000 |
| Standard_D64d_v5              | 64 | 256 | 2 400 | 32 | 300000/4000 | 8 | 30000 |
| Standard_D96d_v5              | 96 | 384 | 3600 | 32 | 450 000/4 000 | 8 | 35000 |

<sup>*</sup> Ces valeurs IOPS peuvent être garanties avec des [machines virtuelles de deuxième génération](generation-2.md)<br>
<sup>1</sup> L’accélération réseau est impérative et activée par défaut sur toutes les machines virtuelles Ddv5.<br>
<sup>2</sup> L’accélération réseau peut être appliquée à deux cartes réseau.

## <a name="ddsv5-series"></a>Série Ddsv5

Les machines virtuelles de la série Ddsv5 s’exécutent sur le processeur Intel® Xeon® Platinum 8370C (Ice Lake) de 3e génération, atteignant une vitesse d’horloge Turbo avec tous les cœurs jusqu’à 3,5 GHz.  Ces machines virtuelles offrent jusqu’à 96 processeurs virtuels et 384 Gio de RAM, ainsi qu’un stockage SSD local jusqu’à 3 600 Gio. Les machines virtuelles de la série Ddsv5 fournissent une meilleure proposition de valeur pour la plupart des charges de travail à usage général par rapport à la génération précédente (par exemple, une évolutivité accrue et une classe d’UC mise à niveau).

Les machines virtuelles de la série Ddsv5 prennent en charge les types de disques SSD Standard, HDD Standard et SSD Premium. Vous pouvez également attacher un stockage Disque Ultra en fonction de sa disponibilité régionale. Le stockage sur disque de données est facturé séparément des machines virtuelles. [Consultez les prix des disques](https://azure.microsoft.com/pricing/details/managed-disks/).

[Stockage Premium](premium-storage-performance.md) : Pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Générations 1 et 2<br>
[Accélération réseau](../virtual-network/create-vm-accelerated-networking-cli.md) : Obligatoire <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge <br>
[Virtualisation imbriquée](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) : prise en charge <br>
<br> 


| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit maximal du stockage temporaire et mis en cache : IOPS/Mbits/s<sup>*</sup> | Débit du disque non mis en cache max. : IOPS/Mbits/s | Débit maximal du disque sans mise en cache avec mode rafale : IOPS/Mbits/s<sup>3</sup> | Nombre max de cartes réseau | Bande passante réseau maximale (Mbits/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v5<sup>1,2</sup> | 2  | 8   | 75   | 4  | 9 000/125    | 3 750/85     | 10 000/1 200 | 2 | 12 500 |
| Standard_D4ds_v5               | 4  | 16  | 150  | 8  | 19 000/250   | 6 400/145    | 20 000/1 200 | 2 | 12 500 |
| Standard_D8ds_v5               | 8  | 32  | 300  | 16 | 38 000/500   | 12 800/290   | 20 000/1 200 | 4 | 12 500 |
| Standard_D16ds_v5              | 16 | 64  | 600  | 32 | 75 000/1 000  | 25 600/600   | 40 000/1 200 | 8 | 12 500 |
| Standard_D32ds_v5              | 32 | 128 | 1200 | 32 | 150 000/2 000 | 51 200/865   | 80 000/2 000 | 8 | 16000 |
| Standard_D48ds_v5              | 48 | 192 | 1800 | 32 | 225 000/3 000 | 76 800/1 315  | 80 000/3 000 | 8 | 24 000 |
| Standard_D64ds_v5              | 64 | 256 | 2 400 | 32 | 375 000/4 000 | 80 000/1 735  | 80 000/3 000 | 8 | 30000 |
| Standard_D96ds_v5              | 96 | 256 | 3600 | 32 | 450 000/4 000 | 80 000/2 600  | 80 000/4 000 | 8 | 35000 |

<sup>*</sup> Ces valeurs IOPS peuvent être garanties avec des [machines virtuelles de deuxième génération](generation-2.md)<br>
<sup>1</sup> L’accélération réseau est impérative et activée par défaut sur toutes les machines virtuelles Ddsv5.<br>
<sup>2</sup> L’accélération réseau peut être appliquée à deux cartes réseau.<br>
<sup>3</sup> Les machines virtuelles de la série Ddsv5 peuvent disposer d’un [bursting](disk-bursting.md) accélérant les performances de leurs disques et atteignant le niveau maximal du bursting pendant 30 minutes d’affilée.

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
