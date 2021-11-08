---
title: Séries DCsv3 et DCdsv3 – Machines virtuelles Azure
description: Spécifications pour les machines virtuelles des séries DCsv3 et DCdsv3.
author: mmcrey
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: mmcrey
ms.custom: ignite-fall-2021
ms.openlocfilehash: 09d4c88bec94c881fd9895557aa35264ea4ffab4
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561211"
---
# <a name="dcsv3-and-dcdsv3-series"></a>Séries DCsv3 et DCdsv3

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

> [!NOTE] 
> Les séries DCsv3 et DCdsv3 sont en préversion publique depuis le 1er novembre 2021

Les machines virtuelles des séries DCsv3 et DCdsv3 permettent de protéger la confidentialité et l’intégrité de vos données et de votre code lors de leur traitement dans le cloud public. En tirant parti des extensions Intel® Software Guard et Intel® Total Memory Encryption - Multi Key, les clients peuvent s’assurer que leurs données sont toujours chiffrées et protégées lors de leur utilisation. 

Ces machines sont optimisées par les derniers processeurs Intel® Xeon Scalable de 3e génération et tirent parti de la technologie Intel® Turbo Boost max 3.0 pour atteindre une cadence de 3.5 GHz. 

Avec cette génération, les cœurs de processeur ont été multipliés par 6 (jusqu’à un maximum de 48 cœurs physiques), la mémoire chiffrée (EPC) a été multipliée par 1 500 jusqu’à 256 Go, la mémoire normale a été multipliée par 12 jusqu’à 384 Go. Toutes ces modifications améliorent considérablement les performances entre générations et ouvrent la porte à des scénarios entièrement nouveaux. 

> [!NOTE]
> L’hyperthreading est désactivé pour plus de sécurité. La tarification est basée sur les performances supérieures des cœurs physiques par rapport aux vCores, ainsi que sur les capacités de sécurité uniques de la série DC.

Nous proposons deux variantes selon que la charge de travail bénéficie d’un disque local ou non. Que vous choisissiez une machine virtuelle avec ou sans disque local, vous pouvez attacher un stockage sur disque persistant distant à toutes les machines virtuelles. Dans tous les cas, comme toujours, les options de disque distant (par exemple, pour le disque de démarrage de machine virtuelle) sont facturées séparément des machines virtuelles. 

## <a name="configuration"></a>Configuration

Processeur : processeur Intel® Xeon Scalable 8370C de 3e génération<br>
Fréquence tous noyaux de base : 2.8 GHz<br>
[Turbo Boost Max 3.0](https://www.intel.com/content/www/us/en/gaming/resources/turbo-boost.html): activé, fréquence maximale 3.5 GHz<br>
[Hyper-threading](https://www.intel.com/content/www/us/en/gaming/resources/hyper-threading.html) : non pris en charge<br>
[Chiffrement de mémoire total : multi-clé](https://itpeernetwork.intel.com/memory-encryption/) : Activé<br>
[Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Stockage sur disque Ultra](disks-enable-ultra-ssd.md) : pris en charge<br>
[Mise en réseau accélérée](../virtual-network/create-vm-accelerated-networking-cli.md) : prise en charge (approvisionnement CLI ou modèle ARM uniquement)<br>
[Service Azure Kubernetes](../aks/intro-kubernetes.md) : pris en charge (approvisionnement CLI uniquement)<br>
[Migration dynamique](maintenance-and-updates.md) : Non pris en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Non pris en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Génération 2<br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge <br>
[Hôte dédié](dedicated-hosts.md) : bientôt disponible<br>

## <a name="dcsv3-series-technical-specifications"></a>Spécifications techniques de la série DCsv3

| Taille             | Cœurs physiques | Mémoire en Go | Stockage temporaire (SSD) en Gio | Disques de données max. | Nombre max de cartes réseau |  Go de mémoire EPC |
|------------------|----------------|-------------|------------------------|----------------|---------|---------------------|
| Standard_DC1s_v3 | 1              | 8           | N/A                    | 4              | 2     |  4                 |
| Standard_DC2s_v3 | 2              | 16          | N/A                    | 8              | 2     |  8                 |
| Standard_DC4s_v3 | 4              | 32          | N/A                    | 16             | 4     |  16                |
| Standard_DC8s_v3 | 8              | 64          | N/A                    | 32             | 8     |  32                |
| Standard_DC16s_v3  | 16           | 128         | N/A                    | 32             | 8     |  64                |
| Standard_DC24s_v3  | 24           | 192         | N/A                    | 32             | 8     |  128               |
| Standard_DC32s_v3  | 32           | 256         | N/A                    | 32             | 8     |  192               |
| Standard_DC48s_v3  | 48           | 384         | N/A                    | 32             | 8     |  256               |

## <a name="dcdsv3-series-technical-specifications"></a>Spécifications techniques de la série DCdsv3

| Taille             | Cœurs physiques | Mémoire en Go | Stockage temporaire (SSD) en Gio | Disques de données max. | Nombre max de cartes réseau |  Go de mémoire EPC |
|------------------|----------------|-------------|------------------------|----------------|---------|---------------------|
| Standard_DC1ds_v3 | 1              | 8           | 75                    | 4              | 2     |  4                 |
| Standard_DC2ds_v3 | 2              | 16          | 150                    | 8              | 2     |  8                 |
| Standard_DC4ds_v3 | 4              | 32          | 300                    | 16             | 4     |  16                |
| Standard_DC8ds_v3 | 8              | 64          | 600                    | 32             | 8     |  32                |
| Standard_DC16ds_v3  | 16           | 128         | 1200                    | 32             | 8     |  64                |
| Standard_DC24ds_v3  | 24           | 192         | 1800                    | 32             | 8     |  128               |
| Standard_DC32ds_v3  | 32           | 256         | 2 400                    | 32             | 8     |  192               |
| Standard_DC48ds_v3  | 48           | 384         | 2 400                    | 32             | 8     |  256               |

## <a name="get-started"></a>Bien démarrer

- Créer des machines virtuelles DCsv3 et DCdsv3 à l’aide du [portail Azure](./linux/quick-create-portal.md)
- Les machines virtuelles des séries DCsv3 et DCdsv3 sont des [machines virtuelles de 2e génération](./generation-2.md#creating-a-generation-2-vm) qui ne prennent en charge que les images `Gen2`.
- Actuellement disponibles dans les régions listées sur la page [Produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).

## <a name="more-sizes-and-information"></a>Autres tailles et informations

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)
- [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)

Calculatrice de prix : [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
