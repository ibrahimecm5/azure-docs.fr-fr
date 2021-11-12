---
title: Tailles des machines virtuelles Azure – Mémoire | Microsoft Docs
description: Répertorie les différentes tailles de mémoire optimisée disponibles pour les machines virtuelles dans Azure. Répertorie des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
services: virtual-machines
documentationcenter: ''
author: brbell
tags: azure-resource-manager,azure-service-management
keywords: Isolation de machine virtuelle, machine virtuelle isolée, isolation, isolée
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/20/2021
ms.author: brbell
ms.openlocfilehash: 50a187435b1a47fe2559ce5ce2d36905570b8419
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456591"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Tailles de machine virtuelle à mémoire optimisée

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

> [!TIP]
> Essayez l’ **[outil de sélection des machines virtuelles](https://aka.ms/vm-selector)** pour trouver d’autres tailles mieux adaptées à votre charge de travail.

Les tailles de machine virtuelle à mémoire optimisée offrent un ration mémoire/processeur supérieur pour les serveurs de base de données relationnelle, les caches moyens à grands et l’analytique en mémoire. Cet article fournit des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur la bande passante réseau et le débit de stockage pour chaque taille de ce regroupement.

- Les [séries Dv2 et DSv2](dv2-dsv2-series-memory.md), suites de la série D d’origine, comprennent un UC plus puissant. La série Dv2 est environ 35 % plus rapide que la série D. Elle s’exécute sur les processeurs Intel&reg; Xeon&reg; 8171M 2,1 GHz (Skylake), Intel&reg; Xeon&reg; E5-2673 v4 2,3 GHz (Broadwell) ou Intel&reg; Xeon&reg; E5-2673 v3 2,4 GHz (Haswell) avec la technologie Intel Turbo Boost 2.0. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.

    Les séries Dv2 et DSv2 sont idéales pour les applications qui exigent des processeurs virtuels plus rapides, de meilleures performances de stockage temporaire, ou qui ont des exigences de mémoire plus élevées. Elles offrent une combinaison puissante pour de nombreuses applications professionnelles.

- Les [séries Eav4 et Easv4](eav4-easv4-series.md) utilisent le processeur EPYC<sup>TM</sup> 7452 2,35 GHz d’AMD dans une configuration multithread avec un cache L3 jusqu’à 256 Mo, ce qui améliore les options d’exécution de la plupart des charges de travail à mémoire optimisée. Les séries Eav4 et Easv4 ont les mêmes configurations de mémoire et de disque que les séries Ev3 et Esv3.

- Les [séries Ev3 et Esv3](ev3-esv3-series.md) disposent du processeur Intel&reg; Xeon&reg; 8171M 2,1 GHz (Skylake) ou Intel&reg; Xeon&reg; E5-2673 v4 2,3 GHz (Broadwell) dans une configuration hyperthread, ce qui leur permet d’offrir ce qui se fait de mieux pour la plupart des charges de travail à usage général et d’aligner la série Ev3 sur les machines virtuelles à usage général de la plupart des autres clouds. La mémoire a été étendue (de 7 Gio/processeur virtuel à 8 Gio/processeur virtuel) et les limites de disque et de réseau ont été ajustées au niveau du cœur pour s’aligner sur la transition vers l’hyperthreading. La série Ev3 constitue la suite des tailles de machines virtuelles à mémoire haute des familles D/Dv2.

- Les [séries Ev4 et Esv4](ev4-esv4-series.md) s’exécutent sur les processeurs Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) de seconde génération dans une configuration hyper-thread. Elles sont idéales pour diverses applications d’entreprise gourmandes en mémoire et offrent jusqu’à 504 Gio de RAM. Caractéristiques : [technologie Intel&reg; Turbo Boost 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [technologie Intel&reg; Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) et [Intel&reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Les séries Ev4 et Esv4 n’incluent pas de disque temporaire local. Pour plus d’informations, consultez [Tailles de machines virtuelles Azure sans disque temporaire local](azure-vms-no-temp-disk.yml).

- Les [séries Edv4 et Edsv4](edv4-edsv4-series.md) s’exécutent sur les processeurs Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) de seconde génération. Elle sont idéales pour les très grandes bases de données ou d’autres applications qui bénéficient d’un nombre élevé de processeurs virtuels et de grandes quantités de mémoire. En outre, ces tailles de machines virtuelles incluent un stockage SSD local rapide et plus important pour les applications qui bénéficient ainsi d’un stockage local à faible latence et à haut débit. Caractéristiques : vitesse d’horloge Turbo continue de 3,4 GHz, [technologie Intel&reg; Turbo Boost 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [technologie Intel&reg; Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) et [Intel&reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html).

- Les [séries Easv5 et Eadsv5](easv5-eadsv5-series.md) utilisent le processeur EPYC<sup>TM</sup> 7763V de 3e génération d’AMD dans une configuration multithread avec jusqu’à 256 Mo de cache L3, donnant plus options aux clients pour l’exécution de la plupart des charges de travail à mémoire optimisée. Ces machines virtuelles offrent une combinaison de processeurs virtuels et de mémoire pour répondre aux exigences associées à la plupart des applications professionnelles gourmandes en mémoire, telles que les serveurs de base de données relationnelle et les charges de travail d’analytique en mémoire. 

- Les [séries Edv5 et Edsv5](edv5-edsv5-series.md) s’exécutent sur les processeurs Intel&reg; Xeon&reg; Platinum 8272CL (Ice Lake) dans une configuration hyper-thread. Idéales pour les applications d’entreprise gourmandes en mémoire, elles proposent jusqu’à 512 Gio de RAM, la technologie [Intel&reg; Turbo Boost 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), la technologie [Intel&reg; Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) et la technologie [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512).](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) Elles prennent également en charge la technologie [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Ces nouvelles tailles de machines virtuelles disposeront d’un stockage local 50 % plus volumineux ainsi que de meilleures IOPS de disque local en lecture et en écriture par rapport aux tailles [Ev3/Esv3](./ev3-esv3-series.md) avec des [machines virtuelles Gen2](./generation-2.md). Elles sont dotées d’une vitesse d’horloge de Turbo cœur de 3,4 GHz.

- Les [séries Ev5 et Esv5](ev5-esv5-series.md) s’exécutent sur les processeurs Intel&reg; Xeon&reg; Platinum 8272CL (Ice Lake) dans une configuration hyper-thread. Elles sont idéales pour diverses applications d’entreprise utilisant beaucoup de mémoire et offrent jusqu’à 512 Gio de RAM. Elles sont dotées d’une vitesse d’horloge de Turbo cœur de 3,4 GHz.

- La [série M](m-series.md) propose un nombre élevé de processeurs virtuels (jusqu’à 128 processeurs virtuels) et une grande quantité de mémoire (jusqu’à 3,8 Tio). Elle est également idéale pour les très grandes bases de données ou d’autres applications qui bénéficient d’un nombre élevé de processeurs virtuels et de grandes quantités de mémoire.

- La [série Mv2](mv2-series.md) offre le nombre de processeurs virtuels le plus élevé (jusqu’à 416 processeurs virtuels) et la plus grande mémoire (jusqu’à 11,4 Tio) parmi toutes les machines virtuelles dans le cloud. Elle est idéale pour les très grandes bases de données ou d’autres applications qui bénéficient d’un nombre élevé de processeurs virtuels et de grandes quantités de mémoire.

Le Calcul Azure propose des tailles de machines virtuelles qui sont isolées pour un type de matériel spécifique et dédiées à un seul et même client. Ces tailles de machines virtuelles conviennent mieux aux charges de travail qui nécessitent un niveau élevé d’isolation par rapport aux autres clients pour les charges de travail qui impliquent des éléments tels que les exigences réglementaires et de conformité. Les clients peuvent également choisir de subdiviser les ressources de ces machines virtuelles isolées à l’aide de la [prise en charge d’Azure pour les machines virtuelles imbriquées](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Consultez les pages des familles de machines virtuelles ci-dessous pour connaître les options de machine virtuelle isolée.

## <a name="other-sizes"></a>Autres tailles

- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.

Pour plus d’informations sur la façon dont Azure nomme ses machines virtuelles, consultez [Conventions de nommage des tailles de machines virtuelles Azure](./vm-naming-conventions.md).
