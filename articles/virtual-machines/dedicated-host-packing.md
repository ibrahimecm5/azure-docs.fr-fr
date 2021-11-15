---
title: Tables de configuration des références SKU Dedicated Host
description: Spécifications de compression de machine virtuelle des références SKU ADH.
author: brittanyrowe
ms.author: brittanyrowe
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 67b17324b550196728da62cb0e5b306d6387fa25
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479127"
---
# <a name="azure-dedicated-host-sku-configuration-tables"></a>Tables de configuration des références SKU Azure Dedicated Host
Les références SKU Azure Dedicated Host sont la combinaison d’une famille de machines virtuelles et d’une certaine spécification matérielle. Vous pouvez uniquement déployer des machines virtuelles de la série de machines virtuelles que la référence SKU Dedicated Host spécifie. Par exemple, sur Dsv3-Type3, vous pouvez uniquement approvisionner la série de machines virtuelles [Dsv3](dv3-dsv3-series.md#dsv3-series). 

Ce document passe en revue les spécifications matérielles et les compressions de machines virtuelles pour toutes les références SKU Dedicated Host.

## <a name="dasv4"></a>Dasv4
### <a name="dasv4-type1"></a>Dasv4-Type1
Dasv4-Type1 est une référence SKU Dedicated Host utilisant le processeur 2.35 GHz EPYC™ 7452 d’AMD. Elle offre 64 cœurs physiques, 96 processeurs virtuels et 672 Gio de RAM. Dasv4-Type1 exécute la série de machines virtuelles [Dasv4](dav4-dasv4-series.md#dasv4-series). Reportez-vous à la documentation sur la taille des machines virtuelles pour mieux comprendre les performances spécifiques des machines virtuelles.

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Dasv4-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle  | Nombre de machines virtuelles |
|----------------|-----------------|---------------|----------|-------|
| 64             | 96              | 672 Gio       | D2as v4  | 32    |
|                |                 |               | D4as v4  | 24    |
|                |                 |               | D8as v4  | 12    |
|                |                 |               | D16as v4 | 6     |
|                |                 |               | D32as v4 | 3     |
|                |                 |               | D48as v4 | 2     |
|                |                 |               | D64as v4 | 1     |
|                |                 |               | D96as v4 | 1     |

Vous pouvez également mélanger plusieurs tailles de machines virtuelles sur Dasv4-Type1. Voici des exemples de combinaisons de compressions de machines virtuelles sur Dasv4-type1 :
- 1 D48asv4 + 3 D16asv4
- 1 D32asv4 + 2 D16asv4 + 8 D4asv4
- 20 D4asv4 + 8 D2asv4

## <a name="ddsv4"></a>Ddsv4
### <a name="ddsv4-type1"></a>Ddsv4-Type1
Ddsv4-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® Cascade Lake (Xeon® Platinum 8272CL). Elle offre 52 cœurs physiques, 68 processeurs virtuels et 504 Gio de RAM. Ddsv4-Type1 exécute la série de machines virtuelles [Ddsv4](ddv4-ddsv4-series.md#ddsv4-series).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Ddsv4-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle  | Nombre de machines virtuelles |
|----------------|-----------------|---------------|----------|-------|
| 52             | 68              | 504 Gio       | D2ds v4  | 32    |
|                |                 |               | D4ds v4  | 17    |
|                |                 |               | D8ds v4  | 8     |
|                |                 |               | D16ds v4 | 4     |
|                |                 |               | D32ds v4 | 1     |
|                |                 |               | D48ds v4 | 1     |
|                |                 |               | D64ds v4 | 1     |

Vous pouvez également mélanger plusieurs tailles de machines virtuelles sur Ddsv4-Type1. Voici des exemples de combinaisons de compressions de machines virtuelles sur Ddsv4-type1 :
- 1 D48dsv4 + 4 D4dsv4 + 2 D2dsv4
- 1 D32dsv4 + 2 D16dsv4 + 1 D4dsv4
- 10 D4dsv4 + 14 D2dsv4

## <a name="dsv4"></a>Dsv4
### <a name="dsv4-type1"></a>Dsv4-Type1

Dsv4-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® Cascade Lake (Xeon® Platinum 8272CL). Elle offre 52 cœurs physiques, 80 processeurs virtuels et 504 Gio de RAM. Dsv4-Type1 exécute la série de machines virtuelles [Dsv4](dv4-dsv4-series.md#dsv4-series).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Dsv4-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 Gio       | D2s v4  | 32    |
|                |                 |               | D4s v4  | 20    |
|                |                 |               | D8s v4  | 10    |
|                |                 |               | D16s v4 | 5     |
|                |                 |               | D32s v4 | 2     |
|                |                 |               | D48s v4 | 1     |
|                |                 |               | D64s v4 | 1     |

Vous pouvez également mélanger plusieurs tailles de machines virtuelles sur Dsv4-Type1. Voici des exemples de combinaisons de compressions de machines virtuelles sur Dsv4-type1 :
- 1 D64sv4 + 1 D16sv4
- 1 D32sv4 + 2 D16dsv4 + 2 D8sv4
- 10 D4sv4 + 20 D2sv4

## <a name="dsv3"></a>Dsv3
### <a name="dsv3-type1"></a>Dsv3-Type1

Dsv3-Type1 est une référence SKU hôte dédiée utilisant le processeur Intel® Broadwell (2.3 GHz Xeon® E5-2673 v4). Elle offre 40 cœurs physiques, 64 processeurs virtuels et 256 Gio de RAM. Dsv3-Type1 exécute la série de machines virtuelles [Dsv3](dv3-dsv3-series.md#dsv3-series).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Dsv3-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------|-------|
| 40             | 64              | 256 Gio       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 16    |
|                |                 |               | D8s v3  | 8     |
|                |                 |               | D16s v3 | 4     |
|                |                 |               | D32s v3 | 2     |
|                |                 |               | D48s v3 | 1     |
|                |                 |               | D64s v3 | 1     |

Vous pouvez également mélanger plusieurs tailles de machines virtuelles sur Dsv3-Type1. Voici des exemples de combinaisons de compressions de machines virtuelles sur Dsv3-type1 :
- 1 D32sv3 + 1 D16sv3 + 1 D8sv3
- 1 D48sv3 + 3 D4sv3 + 2 D2sv3
- 10 D4sv3 + 12 D2sv3

### <a name="dsv3-type2"></a>Dsv3-Type2

Dsv3-Type2 est une référence SKU Dedicated Host utilisant le processeur Intel® Skylake (2.1 GHz Xeon® Platinum 8171M). Elle offre 48 cœurs physiques, 76 processeurs virtuels et 504 Gio de RAM. Dsv3-Type2 exécute la série de machines virtuelles [Dsv3](dv3-dsv3-series.md#dsv3-series).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Dsv3-Type2.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------|-------|
| 48             | 76              | 504 Gio       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 18    |
|                |                 |               | D8s v3  | 9     |
|                |                 |               | D16s v3 | 4     |
|                |                 |               | D32s v3 | 2     |
|                |                 |               | D48s v3 | 1     |
|                |                 |               | D64s v3 | 1     |

Vous pouvez également mélanger plusieurs tailles de machines virtuelles sur Dsv3-Type2. Voici des exemples de combinaisons de compressions de machines virtuelles sur Dsv3-type2 :
- 1 D64sv3 + 2 D4vs3 + 2 D2sv3
- 1 D48sv3 + 4 D4sv3 + 6 D2sv3
- 12 D4sv3 + 14 D2sv3

### <a name="dsv3-type3"></a>Dsv3-Type3

Dsv3-Type3 est une référence SKU Dedicated Host utilisant le processeur Intel® Cascade Lake (Xeon® Platinum 8272CL). Elle offre 52 cœurs physiques, 80 processeurs virtuels et 504 Gio de RAM. Dsv3-Type3 exécute la série de machines virtuelles [Dsv3](dv3-dsv3-series.md#dsv3-series).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Dsv3-Type3.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 Gio       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 20    |
|                |                 |               | D8s v3  | 10    |
|                |                 |               | D16s v3 | 5     |
|                |                 |               | D32s v3 | 2     |
|                |                 |               | D48s v3 | 1     |
|                |                 |               | D64s v3 | 1     |

Vous pouvez également mélanger plusieurs tailles de machines virtuelles sur Dsv3-Type3. Voici des exemples de combinaisons de compressions de machines virtuelles sur Dsv3-type3 :
- 1 D64sv3 + 1 D8sv3 + 2 D4sv3
- 1 D48sv3 + 1 D16sv3 + 4 D4sv3
- 15 D4sv3 +  10 D2sv3

## <a name="dcsv2"></a>DCsv2
### <a name="dcsv2-type1"></a>DCsv2-Type1

Le DCsv2-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® Coffee Lake (Xeon® E-2288G avec la technologie SGX). Elle offre 8 cœurs physiques, 8 processeurs virtuels et 64 Gio de RAM. DCsv2-Type1 exécute la série de machines virtuelles [DCsv2](dcv2-series.md).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte DCsv2-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------|-------|
| 8              | 8               | 64 Gio        | DC8s v2 | 1     |

## <a name="easv4"></a>Easv4
### <a name="easv4-type1"></a>Easv4-Type1

Easv4-Type1 est une référence SKU Dedicated Host utilisant le processeur 2.35 GHz EPYC™ 7452 d’AMD. Elle offre 64 cœurs physiques, 96 processeurs virtuels et 672 Gio de RAM. Easv4-Type1 exécute la série de machines virtuelles [Easv4](eav4-easv4-series.md#easv4-series).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Easv4-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle  | Nombre de machines virtuelles |
|----------------|-----------------|---------------|----------|-------|
| 64             | 96              | 672 Gio       | E2as v4  | 32    |
|                |                 |               | E4as v4  | 21    |
|                |                 |               | E8as v4  | 10    |
|                |                 |               | E16as v4 | 5     |
|                |                 |               | E20as v4 | 4     |
|                |                 |               | E32as v4 | 2     |
|                |                 |               | E48as v4 | 1     |
|                |                 |               | E64as v4 | 1     |
|                |                 |               | E96as v4 | 1     |

## <a name="edsv4"></a>Edsv4
### <a name="edsv4-type1"></a>Edsv4-Type1

Edsv4-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® Cascade Lake (Xeon® Platinum 8272CL). Elle offre 52 cœurs physiques, 64 processeurs virtuels et 504 Gio de RAM. Edsv4-Type1 exécute la série de machines virtuelles [Edsv4](edv4-edsv4-series.md#edsv4-series).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Edsv4-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle  | Nombre de machines virtuelles |
|----------------|-----------------|---------------|----------|-------|
| 52             | 64              | 504 Gio       | E2ds v4  | 31    |
|                |                 |               | E4ds v4  | 15    |
|                |                 |               | E8ds v4  | 7     |
|                |                 |               | E16ds v4 | 3     |
|                |                 |               | E20ds v4 | 3     |
|                |                 |               | E32ds v4 | 1     |
|                |                 |               | E48ds v4 | 1     |
|                |                 |               | E64ds v4 | 1     |

## <a name="esv4"></a>Esv4
### <a name="esv4-type1"></a>Esv4-Type1

Esv4-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® Cascade Lake (Xeon® Platinum 8272CL). Elle offre 52 cœurs physiques, 80 processeurs virtuels et 504 Gio de RAM. Esv4-Type1 exécute la série de machines virtuelles [Esv4](ev4-esv4-series.md#esv4-series).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Esv4-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 Gio       | E2s v4  | 31    |
|                |                 |               | E4s v4  | 15    |
|                |                 |               | E8s v4  | 7     |
|                |                 |               | E16s v4 | 3     |
|                |                 |               | E20s v4 | 3     |
|                |                 |               | E32s v4 | 1     |
|                |                 |               | E48s v4 | 1     |
|                |                 |               | E64s v4 | 1     |

## <a name="esv3"></a>Esv3
### <a name="esv3-type1"></a>Esv3-Type1

Esv3-Type1 est une référence SKU hôte dédiée utilisant le processeur Intel® Broadwell (2.3 GHz Xeon® E5-2673 v4). Elle offre 40 cœurs physiques, 64 processeurs virtuels et 448 Gio de RAM. Esv3-Type1 exécute la série de machines virtuelles [Esv3](ev3-esv3-series.md#ev3-series).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Esv3-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------|-------|
| 40             | 64              | 448 Gio       | E2s v3  | 28    |
|                |                 |               | E4s v3  | 14    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 2     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

### <a name="esv3-type2"></a>Esv3-Type2

Esv3-Type2 est une référence SKU Dedicated Host utilisant le processeur Intel® Skylake (Xeon® 8171M). Elle offre 48 cœurs physiques, 78 processeurs virtuels et 504 Gio de RAM. Esv3-Type2 exécute la série de machines virtuelles [Esv3](ev3-esv3-series.md#ev3-series).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Esv3-Type2.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------|-------|
| 48             | 76              | 504 Gio       | E2s v3  | 31    |
|                |                 |               | E4s v3  | 15    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 3     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

### <a name="esv3-type3"></a>Esv3-Type3

Esv3-Type3 est une référence SKU Dedicated Host utilisant le processeur Intel® Cascade Lake (Xeon® Platinum 8272CL). Elle offre 52 cœurs physiques, 80 processeurs virtuels et 504 Gio de RAM. Esv3-Type3 exécute la série de machines virtuelles [Esv3](ev3-esv3-series.md#ev3-series).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Esv3-Type3.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 Gio       | E2s v3  | 31    |
|                |                 |               | E4s v3  | 15    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 3     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

## <a name="fsv2"></a>Fsv2
### <a name="fsv2-type2"></a>Fsv2-Type2

Fsv2-Type2 est une référence SKU Dedicated Host utilisant le processeur Intel® Skylake (Xeon® Platinum 8168). Elle offre 48 cœurs physiques, 72 processeurs virtuels et 144 Gio de RAM. Fsv2-Type2 exécute la série de machines virtuelles [Fsv2](fsv2-series.md).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Fsv2-Type2.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------|-------|
| 48             | 72              | 144 Gio       | F2s v2  | 32    |
|                |                 |               | F4s v2  | 18    |
|                |                 |               | F8s v2  | 9     |
|                |                 |               | F16s v2 | 4     |
|                |                 |               | F32s v2 | 2     |
|                |                 |               | F48s v2 | 1     |
|                |                 |               | F64s v2 | 1     |
|                |                 |               | F72s v2 | 1     |

### <a name="fsv2-type3"></a>Fsv2-Type3

Fsv2-Type3 est une référence SKU Dedicated Host utilisant le processeur Intel® Cascade Lake (Xeon® Platinum 8272CL). Elle offre 52 cœurs physiques, 86 processeurs virtuels et 504 Gio de RAM. Fsv2-Type3 exécute la série de machines virtuelles [Fsv2](fsv2-series.md).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Fsv2-Type3.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------|-------|
| 52             | 86              | 504 Gio       | F2s v2  | 32    |
|                |                 |               | F4s v2  | 21    |
|                |                 |               | F8s v2  | 10    |
|                |                 |               | F16s v2 | 5     |
|                |                 |               | F32s v2 | 2     |
|                |                 |               | F48s v2 | 1     |
|                |                 |               | F64s v2 | 1     |
|                |                 |               | F72s v2 | 1     |

## <a name="fxmds"></a>FXmds
### <a name="fxmds-type1"></a>FXmds-Type1

FXmds-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® Cascade Lake (Xeon® Gold 6246R). Elle offre 32 cœurs physiques, 48 processeurs virtuels et 1 152 Gio de RAM. FXmds-Type1 exécute la série de machines virtuelles [FX](fx-series.md).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte FXmds-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------|-------|
| 32             | 48              | 1 152 Gio     | FX4mds  | 12    |
|                |                 |               | FX12mds | 4     |
|                |                 |               | FX24mds | 2     |
|                |                 |               | FX36mds | 1     |
|                |                 |               | FX48mds | 1     |

## <a name="lsv2"></a>Lsv2
### <a name="lsv2-type1"></a>Lsv2-Type1

Lsv2-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® Cascade Lake (Xeon® Gold 6246R). Elle offre 64 cœurs physiques, 80 processeurs virtuels et 640 Gio de RAM. Lsv2-Type1 exécute la série de machines virtuelles [Lsv2](lsv2-series.md).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Lsv2-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------|-------|
| 64             | 80              | 640 Gio       | L8s v2  | 10    |
|                |                 |               | L16s v2 | 5     |
|                |                 |               | L32s v2 | 2     |
|                |                 |               | L48s v2 | 1     |
|                |                 |               | L64s v2 | 1     |
|                |                 |               | L80s v2 | 1     |

## <a name="m"></a>M
### <a name="ms-type1"></a>Ms-Type1

Ms-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® Cascade Lake (Xeon® Platinum 8280). Elle offre 112 cœurs physiques, 128 processeurs virtuels et 2 048 Gio de RAM. Ms-Type1 exécute la série de machines virtuelles [M](m-series.md), y compris les machines virtuelles M, Mls, Ms, et Mts.

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Ms-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------|-------|
| 112            | 128             | 2 048 Gio     | M32ls   | 4     |
|                |                 |               | M32ts   | 4     |
|                |                 |               | M64     | 2     | 
|                |                 |               | M64s    | 2     |
|                |                 |               | M64ls   | 2     |
|                |                 |               | M128    | 1     | 
|                |                 |               | M128s   | 1     |

### <a name="msm-type1"></a>Msm-Type1

Msm-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® Cascade Lake (Xeon® Platinum 8280). Elle offre 112 cœurs physiques, 128 processeurs virtuels et 3 892 Gio de RAM. Msm-Type1 exécute la série de machines virtuelles [M](m-series.md), y compris les machines virtuelles Ms, Mms, Mts, et Mls.

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Msm-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle   | Nombre de machines virtuelles |
|----------------|-----------------|---------------|-----------|-------|
| 112            | 128             | 3 892 Gio     | M8ms      | 16    |
|                |                 |               | M8-2ms    | 16    |
|                |                 |               | M8-4ms    | 16    |
|                |                 |               | M16ms     | 8     |
|                |                 |               | M16-4ms   | 8     |
|                |                 |               | M16-8ms   | 8     |
|                |                 |               | M32ts     | 4     |
|                |                 |               | M32ls     | 4     |
|                |                 |               | M32ms     | 4     |
|                |                 |               | M32-8ms   | 4     |
|                |                 |               | M32-16ms  | 4     |
|                |                 |               | M64ms     | 2     |
|                |                 |               | M64       | 2     | 
|                |                 |               | M64s      | 2     |
|                |                 |               | M64m      | 2     |
|                |                 |               | M64ls     | 2     |
|                |                 |               | M64-16ms  | 2     |
|                |                 |               | M64-32ms  | 2     |
|                |                 |               | M128ms    | 1     |
|                |                 |               | M128s     | 1     |
|                |                 |               | M128m     | 1     |
|                |                 |               | M128      | 1     | 
|                |                 |               | M128-32ms | 1     |
|                |                 |               | M128-64ms | 1     |

## <a name="mv2"></a>Mv2
### <a name="msmv2-type1"></a>Msmv2-Type1

Msm-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® SkyLake (Xeon® Platinum 8180M). Elle offre 224 cœurs physiques, 416 processeurs virtuels et 11 400 Gio de RAM. Msmv2-Type1 exécute la série de machines virtuelles [Mv2](mv2-series.md), y compris les machines virtuelles Msv2 et Mmsv2.

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Msm-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle       | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------------|-------|
| 224            | 416             | 11 400 Gio    | M208ms v2     | 2     |
|                |                 |               | M208s  v2     | 2     |
|                |                 |               | M416-208ms v2 | 1     | 
|                |                 |               | M416-208s v2  | 1     | 
|                |                 |               | M416ms v2     | 1     |  
|                |                 |               | M416s v2      | 1     |  

### <a name="msv2-type1"></a>Msv2-Type1

Msv2-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® SkyLake (Xeon® Platinum 8180M). Elle offre 224 cœurs physiques, 416 processeurs virtuels et 5 700 Gio de RAM. Msv2-Type1 exécute la série de machines virtuelles [Mv2](mv2-series.md), y compris les machines virtuelles Msv2 et Mmsv2.

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte Msv2-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle       | Nombre de machines virtuelles |
|----------------|-----------------|---------------|---------------|-------|
| 224            | 416             | 5 700 Gio     | M208ms v2     | 2     |
|                |                 |               | M208s v2      | 1     |
|                |                 |               | M416-208s v2  | 1     |
|                |                 |               | M416s v2      | 1     |

## <a name="msv2"></a>Msv2
### <a name="mmsv2medmem-type1"></a>Mmsv2MedMem-Type1
Mmsv2MedMem-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® Cascade Lake (Xeon® Platinum 8280). Elle offre 112 cœurs physiques, 192 processeurs virtuels et 4 096 Gio de RAM. Mmsv2MedMem-Type1 exécute la série de machines virtuelles [Msv2](msv2-mdsv2-series.md), y compris les machines virtuelles Msv2 et Mmsv2.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle   | Nombre de machines virtuelles |
|----------------|-----------------|---------------|-----------|-------|
| 112            | 192             | 4 096 Gio     | M32ms v2  | 4     |
|                |                 |               | M64s v2   | 3     |
|                |                 |               | M64ms v2  | 2     |
|                |                 |               | M128ms v2 | 1     |
|                |                 |               | M128s v2  | 1     |

### <a name="msv2medmem-type1"></a>Msv2MedMem-Type1
Msv2MedMem-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® Cascade Lake (Xeon® Platinum 8280). Elle offre 112 cœurs physiques, 192 processeurs virtuels et 2 048 Gio de RAM. Msv2MedMem-Type1 exécute la série de machines virtuelles [Msv2](msv2-mdsv2-series.md), y compris les machines virtuelles Msv2 et Mmsv2.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle    | Nombre de machines virtuelles |
|----------------|-----------------|---------------|------------|-------|
| 112            | 192             | 2 048 Gio     | M32ms v2   | 2     |
|                |                 |               | M64s v2    | 2     |
|                |                 |               | M64ms v2   | 1     |
|                |                 |               | M128s v2   | 1     |

## <a name="mdsv2"></a>Mdsv2
### <a name="mdmsv2medmem-type1"></a>Mdmsv2MedMem-Type1
Mdmsv2MedMem-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® Cascade Lake (Xeon® Platinum 8280). Elle offre 112 cœurs physiques, 192 processeurs virtuels et 4 096 Gio de RAM. Mdmsv2MedMem-Type1 exécute la série de machines virtuelles [Msv2](msv2-mdsv2-series.md), y compris les machines virtuelles Mdsv2 et Mdmsv2.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle    | Nombre de machines virtuelles |
|----------------|-----------------|---------------|------------|-------|
| 112            | 192             | 4 096 Gio     | M32dms v2  | 4     |
|                |                 |               | M64ds v2   | 2     |
|                |                 |               | M64dms v2  | 2     |
|                |                 |               | M128ds v2  | 1     |
|                |                 |               | M128dms v2 | 1     |

### <a name="mdsv2medmem-type1"></a>Mdsv2MedMem-Type1
Mdsv2MedMem-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® Cascade Lake (Xeon® Platinum 8280). Elle offre 112 cœurs physiques, 192 processeurs virtuels et 2 048 Gio de RAM. Mdsv2MedMem-Type1 exécute la série de machines virtuelles [Msv2](msv2-mdsv2-series.md), y compris les machines virtuelles Mdsv2 et Mdmsv2.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle   | Nombre de machines virtuelles |
|----------------|-----------------|---------------|-----------|-------|
| 112            | 192             | 2 048 Gio     | M32dms v2 | 2     |
|                |                 |               | M64ds v2  | 2     |
|                |                 |               | M64dms v2 | 1     |
|                |                 |               | M128ds v2 | 1     |

## <a name="nvasv4"></a>NVasv4
### <a name="nvasv4-type1"></a>NVasv4-Type1

NVasv4-Type1 est une référence SKU Dedicated Host utilisant le processeur AMD® Rome (EPYC™ 7V12) avec des GPU AMD Radeon Instinct MI25. Elle offre 128 cœurs physiques, 128 processeurs virtuels et 448 Gio de RAM. NVasv4-Type1 exécute la série de machines virtuelles [NVsv4](nvv4-series.md).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte NVasv4-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle   | Nombre de machines virtuelles |
|----------------|-----------------|---------------|-----------|-------|
| 128            | 128             | 448 Gio       | NV4as v4  | 30    |
|                |                 |               | NV8as v4  | 15    |
|                |                 |               | NV16as v4 | 7     |
|                |                 |               | NV32as v4 | 3     |

## <a name="nvsv3"></a>NVsv3
### <a name="nvsv3-type1"></a>NVsv3-Type1

NVsv3-Type1 est une référence SKU Dedicated Host utilisant le processeur Intel® Broadwell (E5-2690 v4) avec des GPU NVDIDIA Tesla M60 et la technologie NVIDIA GRID. Elle offre 28 cœurs physiques, 48 processeurs virtuels et 448 Gio de RAM. NVsv3-Type1 exécute la série de machines virtuelles [NVv3](nvv3-series.md).

La configuration de compression suivante présente le nombre maximal de machines virtuelles uniformes que vous pouvez placer sur un hôte NVsv3-Type1.

| Cœurs physiques | Processeurs virtuels disponibles | RAM disponible | Taille de la machine virtuelle  | Nombre de machines virtuelles |
|----------------|-----------------|---------------|----------|-------|
| 28             | 48              | 448 Gio       | NV12s v3 | 4     |
|                |                 |               | NV24s v3 | 2     |
|                |                 |               | NV48s v3 | 1     | 


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus, voir la page de présentation [Hôtes dédiés](dedicated-hosts.md).

- Un exemple de modèle, disponible dans [Modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md), utilise les zones et les domaines d’erreur pour offrir une résilience maximale dans une région.