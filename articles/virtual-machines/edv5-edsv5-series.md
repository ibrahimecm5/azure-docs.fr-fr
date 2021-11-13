---
title: Séries Edv5 et Edsv5 - Machines virtuelles Azure
description: Spécifications pour les machines virtuelles des séries Edv5 et Edsv5.
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 8a2fac1ded1cd17d9e47500eda06e58ca21fc6e7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850049"
---
# <a name="edv5-and-edsv5-series"></a>Séries Edv5 et Edsv5

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les séries Edv5 et Edsv5 s’exécutent sur les processeurs Intel&reg; ​​Xeon&reg; Platinum 8370C (Ice Lake) de 3e génération dans une configuration d’[hyperthreading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html), offrant une meilleure proposition de valeur pour la plupart des charges de travail universelles. Ce nouveau processeur a les caractéristiques suivantes : vitesse d’horloge Turbo avec tous les cœurs de 3,5 GHz, avec la technologie [Intel&reg; Turbo Boost](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Advanced-Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) et [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Avec jusqu’à 672 Gio de RAM, ces machines virtuelles sont idéales pour les applications d’entreprise gourmandes en mémoire, les serveurs de bases de données relationnelles et les charges de travail d’analytique en mémoire. Ces machines virtuelles sont également dotées d’un stockage SSD local rapide et de grande capacité (jusqu’à 3 900 Gio).

## <a name="edv5-series"></a>Série Edv5

Les machines virtuelles de la série Edv5 s’exécutent sur le processeur Intel® Xeon® Platinum 8370C (Ice Lake) de 3e génération, atteignant une vitesse d’horloge Turbo avec tous les cœurs jusqu’à 3,5 GHz.  Ces machines virtuelles offrent jusqu’à 104 processeurs virtuels et 672 Gio de RAM, ainsi qu’un stockage SSD local jusqu’à 3800 Gio. Les machines virtuelles de la série Edv5 sont idéales pour les applications d’entreprise gourmandes en mémoire et les applications qui bénéficient d’un stockage local à faible latence et à haut débit.

Les machines virtuelles de la série Edv5 prennent en charge les types de disques SSD Standard et HDD Standard. Pour utiliser un stockage SSD Premium ou Disque Ultra, sélectionnez des machines virtuelles de la série Edsv5. Le stockage disque de données est facturé séparément des machines virtuelles. [Consultez les prix des disques](https://azure.microsoft.com/pricing/details/managed-disks/).

[Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Non pris en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Générations 1 et 2<br>
[Accélération réseau](../virtual-network/create-vm-accelerated-networking-cli.md) : Obligatoire <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Non pris en charge <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit maximal du stockage temporaire et mis en cache : IOPS/Mbits/s<sup>*</sup>  | Nombre max de cartes réseau|Bande passante réseau maximale (Mbits/s) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v5<sup>1,2</sup>  | 2   | 16  | 75   | 4  | 9 000/125    | 2 | 12 500 |
| Standard_E4d_v5                | 4   | 32  | 150  | 8  | 19 000/250   | 2 | 12 500 |
| Standard_E8d_v5                | 8   | 64  | 300  | 16 | 38 000/500   | 4 | 12 500 |
| Standard_E16d_v5               | 16  | 128 | 600  | 32 | 75 000/1 000  | 8 | 12 500 |
| Standard_E20d_v5               | 20  | 160 | 750  | 32 | 94 000/1 250  | 8 | 12 500  |
| Standard_E32d_v5               | 32  | 256 | 1200 | 32 | 150 000/2 000 | 8 | 16000  |
| Standard_E48d_v5               | 48  | 384 | 1800 | 32 | 225 000/3 000 | 8 | 24 000  |
| Standard_E64d_v5               | 64  | 512 | 2 400 | 32 | 300 000/4 000 | 8 | 30000  |
| Standard_E96d_v5               | 96  | 672 | 3600 | 32 | 450 000/4 000 | 8 | 35000  |
| Standard_E104id_v5<sup>3</sup> | 104 | 672 | 3 800 | 64 | 450 000/4 000 | 8 | 100000 |

<sup>*</sup> Ces valeurs IOPS peuvent être garanties avec des [machines virtuelles de deuxième génération](generation-2.md)<br>
<sup>1</sup> L’accélération réseau est impérative et activée par défaut sur toutes les machines virtuelles Edv5.<br>
<sup>2</sup> L’accélération réseau peut être appliquée à deux cartes réseau.<br>
<sup>3</sup> L’instance est [isolée](../security/fundamentals/isolation-choices.md#compute-isolation) sur un matériel dédié à un seul client.


## <a name="edsv5-series"></a>Série Edsv5

Les machines virtuelles de la série Edsv5 s’exécutent sur le processeur Intel® Xeon® Platinum 8370C (Ice Lake) de 3e génération, atteignant une vitesse d’horloge Turbo avec tous les cœurs jusqu’à 3,5 GHz.  Ces machines virtuelles offrent jusqu’à 104 processeurs virtuels et 672 Gio de RAM, ainsi qu’un stockage SSD local jusqu’à 3800 Gio. Les machines virtuelles de la série Edsv5 sont idéales pour les applications d’entreprise gourmandes en mémoire et les applications qui bénéficient d’un stockage local à faible latence et à haut débit.

Les machines virtuelles de la série Edsv5 prennent en charge les types de disques SSD Standard et HDD Standard. Vous pouvez attacher des stockages sur disque SSD Standard, HDD Standard et SSD Premium à ces machines virtuelles. Vous pouvez également attacher un stockage Disque Ultra en fonction de sa disponibilité régionale. Le stockage sur disque de données est facturé séparément des machines virtuelles. [Consultez les prix des disques](https://azure.microsoft.com/pricing/details/managed-disks/).

[Stockage Premium](premium-storage-performance.md) : Pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Pris(e) en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Pris(e) en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Générations 1 et 2<br>
[Accélération réseau](../virtual-network/create-vm-accelerated-networking-cli.md) : Obligatoire <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit maximal du stockage temporaire et mis en cache : IOPS/Mbits/s<sup>*</sup> | Débit du disque non mis en cache max. : IOPS/Mbits/s | Débit maximal du disque sans mise en cache avec mode rafale (IOPS/Mbits/s)<sup>5</sup> | Nombre max de cartes réseau | Bande passante réseau maximale (Mbits/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v5<sup>1,2</sup>  | 2   | 16  | 75   | 4  | 9 000/125    | 3 750/85      | 10 000/1 200 | 2 | 12 500 |
| Standard_E4ds_v5                | 4   | 32  | 150  | 8  | 19 000/250   | 6 400/145     | 20 000/1200 | 2 | 12 500 |
| Standard_E8ds_v5                | 8   | 64  | 300  | 16 | 38 000/500   | 12 800/290    | 20 000/1200 | 4 | 12 500 |
| Standard_E16ds_v5               | 16  | 128 | 600  | 32 | 75 000/1 000  | 25 600/600    | 40 000/1 200 | 8 | 12 500 |
| Standard_E20ds_v5               | 20  | 160 | 750  | 32 | 94 000/1 250  | 32 000/750    | 64 000/1 600 | 8 | 12 500  |
| Standard_E32ds_v5               | 32  | 256 | 1200 | 32 | 150 000/2 000 | 51 200/865    | 80 000/2 000 | 8 | 16000  |
| Standard_E48ds_v5               | 48  | 384 | 1800 | 32 | 225 000/3 000 | 76 800/1 315   | 80 000/3 000 | 8 | 24 000  |
| Standard_E64ds_v5               | 64  | 512 | 2 400 | 32 | 375 000/4 000 | 80 000/1 735   | 80 000/3 000 | 8 | 30000  |
| Standard_E96ds_v5<sup>3</sup>   | 96  | 672 | 3600 | 32 | 450 000/4 000 | 80 000/2 600   | 80 000/4 000 | 8 | 35000  |
| Standard_E104ids_v5<sup>4</sup> | 104 | 672 | 3 800 | 64 | 450 000/4 000 | 120 000/4 000  | 120 000/4 000 | 8 | 100000 |

<sup>*</sup> Ces valeurs IOPS peuvent être garanties avec des [machines virtuelles de deuxième génération](generation-2.md)<br>
<sup>1</sup> L’accélération réseau est impérative et activée par défaut sur toutes les machines virtuelles Edsv5.<br>
<sup>2</sup> L’accélération réseau peut être appliquée à deux cartes réseau.<br>
<sup>3</sup> Tailles avec [contraintes de cœurs](constrained-vcpu.md) disponibles.<br>
<sup>4</sup> L’instance est [isolée](../security/fundamentals/isolation-choices.md#compute-isolation) sur un matériel dédié à un seul client.<br>
<sup>5</sup> Les machines virtuelles de la série Edsv5 peuvent disposer d’un [bursting](disk-bursting.md) accélérant les performances de leurs disques et atteignant le niveau maximal du bursting pendant 30 minutes d’affilée.

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
