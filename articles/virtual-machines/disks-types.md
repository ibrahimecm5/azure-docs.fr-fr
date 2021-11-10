---
title: Sélectionner un type de disque pour les machines virtuelles Azure IaaS - disques managés
description: Découvrez les types de disques Azure disponibles pour les machines virtuelles, notamment les disques Ultra, les disques SSD Premium, les disques SSD Standard et les disques HDD Standard.
author: roygara
ms.author: rogarana
ms.date: 11/03/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 5e4eb581f8cf9b95e9a8ba4dffd442efc6c055ef
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505438"
---
# <a name="azure-managed-disk-types"></a>Types de disques managés Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Azure propose actuellement quatre types de disques managés, chaque type étant destiné à répondre à un scénario client spécifique :

- Disques Ultra
- Disques SSD Premium
- SSD Standard
- Disques durs Standard

## <a name="disk-type-comparison"></a>Comparaison du type de disque

Le tableau suivant compare les quatre types de disques pour vous aider à choisir celui que vous allez utiliser.

|         | Disque Ultra | SSD Premium | SSD Standard | <nobr>HDD Standard</nobr> |
| ------- | ---------- | ----------- | ------------ | ------------ |
| **Type de disque** | SSD | SSD | SSD | HDD |
| **Scénario**  | Charges de travail gourmandes en E/S, telles que le système [SAP HANA](workloads/sap/hana-vm-operations-storage.md), les bases de données de niveau supérieur (par exemple, SQL et Oracle), et autres charges de travail très lourdes en transactions. | Charges de travail de production et sensibles aux performances | Serveurs web, applications d’entreprise peu utilisées et Dev/Test | Sauvegarde, non critique, accès peu fréquent |
| **Taille maximale du disque** | 65 536 gibioctets (Gio) | 32 767 Gio | 32 767 Gio | 32 767 Gio |
| **Débit max.** | 4 000 Mo/s | 900 Mo/s | 750 Mo/s | 500 Mo/s |
| **Nb max. d’E/S par seconde** | 160 000 | 20 000 | 6 000 / 750 | 2 000 |

## <a name="ultra-disks"></a>Disques Ultra

Les disques Ultra Azure sont l’option de stockage la plus performante pour les machines virtuelles Azure. Vous pouvez modifier les paramètres de performances d’un disque Ultra sans avoir à redémarrer vos machines virtuelles. Les disques Ultra sont adaptés aux charges de travail qui consomment beaucoup de données, par exemple SAP HANA, les bases de données de niveau supérieur et les charges de travail avec un grand nombre de transactions.

Les disques Ultra doivent être utilisés en tant que disques de données et ne peuvent être créés que comme des disques vides. Microsoft recommande d’utiliser des disques SSD Premium en tant que disques de système d’exploitation.

### <a name="ultra-disk-size"></a>Taille de disque Ultra

Les disques Ultra Azure offrent jusqu’à 32 Tio par région et par abonnement par défaut, mais ils prennent en charge une plus grande capacité par demande. Pour demander une augmentation de la capacité, demandez une augmentation du quota ou contactez le support Azure.

Le tableau suivant compare les tailles des disques et les limites de performance pour vous aider à choisir celui que vous allez utiliser.

|Taille de disque (Gio)  |Plafond d’IOPS  |Débit limite (Mbits/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2 400         |600         |
|16     |4 800         |1,200         |
|32     |9 600         |2 400         |
|64     |19 200         |4 000         |
|128     |38 400         |4 000         |
|256     |76 800         |4 000         |
|512     |153 600         |4 000         |
|1 024 à 65 536 (dans cette plage, les tailles augmentent par incréments de 1 Tio)     |160 000         |4 000         |

Les disques Ultra sont conçus pour fournir des latences inférieure à la milliseconde ainsi que l’IOPS et le débit cibles décrits dans le tableau précédent 99,99 % du temps.

### <a name="ultra-disk-performance"></a>Performances du disque Ultra

Les disques Ultra offrent un modèle de configuration des performances flexible qui vous permet de configurer indépendamment l’IOPS et le débit avant et après le provisionnement du disque. Les disques Ultra sont disponibles en plusieurs tailles fixes, allant de 4 Gio à 64 Tio.

### <a name="ultra-disk-iops"></a>IOPS de disque Ultra

Les disques Ultra prennent en charge des limites d’IOPS de 300 IOPS/Gio, avec au maximum 160 000 IOPS par disque. Pour atteindre l’IOPS cible pour le disque, vérifiez que le nombre d’IOPS du disque sélectionné est inférieur à la limite d’IOPS de la machine virtuelle.

La limite maximale actuelle pour les IOPS pour une seule machine virtuelle dans les tailles généralement disponibles est de 80 000. Les disques Ultra avec un plus grand IOPS peuvent être utilisés en tant que disques partagés pour prendre en charge plusieurs machines virtuelles.

Le nombre minimal d’IOPS garanti par disque est de 1 IOPS/Gio avec une base de référence globale minimale de 100 IOPS. Par exemple, si vous avez provisionné un disque Ultra de 4 Gio, le nombre d’IOPS minimal pour ce disque est de 100, au lieu de huit.

Pour plus d’informations sur l’IOPS, consultez [Performances des machines virtuelles et des disques](disks-performance.md).

### <a name="ultra-disk-throughput"></a>Débit du disque Ultra

La limite de débit d’un seul disque Ultra est de 256 Kio/s pour chaque IOPS approvisionnée, avec au maximum 4 000 Mbits/s par disque (où 1 Mbits/s = 10^6 octets par seconde). Le débit minimal garanti par disque est de 4 Kio/s pour chaque IOPS provisionnée avec une base de référence globale minimale de 1 Mbits/s.

Vous pouvez ajuster les performances d’IOPS et de débit du disque lors de l’exécution sans détacher le disque de la machine virtuelle. Une fois qu’une opération de redimensionnement des performances est émise sur un disque, elle peut mettre jusqu’à une heure à être appliquée. Vous pouvez effectuer jusqu’à quatre opérations de redimensionnement des performances sur une période de 24 heures.

Une opération de redimensionnement des performances peut échouer si la capacité de la bande passante des performances est insuffisante.

### <a name="ultra-disk-limitations"></a>Limitations du disque Ultra

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

Si vous souhaitez commencer à utiliser des disques Ultra, consultez l’article sur [l’utilisation de disques Ultra Azure](disks-enable-ultra-ssd.md).

## <a name="premium-ssds"></a>SSD Premium

Les disques SSD Premium Azure offrent une prise en charge très performante et une faible latence pour les machines virtuelles avec des charges de travail qui utilisent beaucoup d’entrée/sortie (E/S). Pour tirer parti de la vitesse et des performances des disques SSD Premium, vous pouvez migrer les disques de machines virtuelles existants vers des disques SSD Premium. Les disques SSD Premium conviennent aux applications de production stratégiques, mais vous ne pouvez les utiliser qu’avec les séries de machines virtuelles compatibles.

Pour en savoir plus sur les types et les tailles de machines virtuelles Azure individuelles pour Windows ou Linux, notamment les tailles compatibles avec le stockage Premium, consultez [Tailles des machines virtuelles dans Azure](sizes.md). Vous devez vérifier chaque taille de machine virtuelle pour déterminer si elle est compatible avec le stockage Premium.

### <a name="premium-ssd-size"></a>Tailles de disque SSD Premium
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

La capacité, l’IOPS et le débit sont garantis lorsqu’un disque de stockage Premium est provisionné. Par exemple, si vous créez un disque P50, Azure configure une capacité de stockage de 4 095 Go, 7 500 E/S par seconde et un débit de 250 Mo/s pour ce disque. Votre application peut utiliser tout ou partie de la capacité et des performances. Les disques SSD Premium sont conçus pour fournir 99,9 % du temps les latences en millisecondes à un seul chiffre, l’IOPS et le débit cibles décrits dans le tableau précédent.

### <a name="premium-ssd-bursting"></a>Mode rafale SSD Premium

Les disques SSD Premium offrent le bursting de disque pour une meilleure tolérance aux changements imprévisibles des modèles d’E/S. Le bursting de disque est particulièrement utile pendant le démarrage du disque du système d’exploitation et pour les applications avec un trafic irrégulier. Pour en savoir plus sur le fonctionnement du bursting des disques Azure, consultez [Bursting de disque](disk-bursting.md#disk-level-bursting).

### <a name="premium-ssd-transactions"></a>Transactions sur le disque SSD Premium

Pour les disques SSD Premium, chaque opération d’E/S inférieure ou égale à 256 Kio de débit est considérée comme une seule opération d’E/S. Les opérations d’E/S dont le débit est supérieur à 256 Kio sont considérées comme des opérations d’E/S distinctes, de 256 Kio chacune.

## <a name="standard-ssds"></a>SSD Standard

Les disques SSD Standard Azure sont optimisés pour les charges de travail qui nécessitent une performance constante à des niveaux d’IOPS inférieurs. Ils sont particulièrement utiles pour les clients avec des charges de travail variables prises en charge par des solutions à disque dur locales. Par rapport aux disques HDD Standard, les disques SSD Standard offrent une meilleure disponibilité, cohérence, fiabilité et latence. Les disques SSD Standard sont adaptés aux serveurs web, aux serveurs d’applications avec peu d’IOPS, aux applications d’entreprise peu utilisées et aux charges de travail hors production. Comme les disques HDD Standard, les disques SSD Standard sont disponibles sur toutes les machines virtuelles Azure.

### <a name="standard-ssd-size"></a>Taille de disque SSD Standard

[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

Les disques SSD Standard sont conçus pour fournir des latences de quelques millisecondes ainsi qu’un IOPS et un débit dans les limites décrites dans le tableau précédent 99 % du temps. L’IOPS et le débit réels peuvent varier selon les modèles de trafic. Les disques SSD Standard offrent des performances plus cohérentes que les disques HDD avec une latence plus faible.

### <a name="standard-ssd-transactions"></a>Transactions sur le disque SSD Standard

Pour les disques SSD Standard, chaque opération d’E/S inférieure ou égale à 256 Kio de débit est considérée comme une seule opération d’E/S. Les opérations d’E/S dont le débit est supérieur à 256 Kio sont considérées comme des opérations d’E/S distinctes, de 256 Kio chacune. Ces transactions entraînent un coût facturable.

### <a name="standard-ssd-bursting"></a>Bursting de disque SSD Standard

Les disques SSD Standard offrent le bursting de disque pour une plus grande tolérance aux changements imprévisibles des modèles d’E/S. Les disques de démarrage du système d’exploitation et les applications sujettes aux pics de trafic tirent parti du bursting de disque. Pour en savoir plus sur le fonctionnement du bursting des disques Azure, consultez [Bursting de disque](disk-bursting.md#disk-level-bursting).

## <a name="standard-hdds"></a>HDD Standard

Les disques durs HDD Standard Azure offrent une prise en charge fiable et économique des disques des machines virtuelles exécutant des charges de travail tolérantes aux latences. Avec le stockage standard, vos données sont stockées sur des disques durs, et les performances peuvent varier plus largement que celles des disques SSD. Les disques durs Standard sont conçus pour fournir des latences d’écriture de moins de 10 ms et des latences de lecture de moins de 20 ms pour la plupart des opérations d’E/S. Toutefois, les performances réelles peuvent varier en fonction de la taille d’E/S et du modèle de charge de travail. Avec des machines virtuelles, vous pouvez utiliser des disques HDD Standard pour les scénarios Dev/Test et les charges de travail moins critiques. Les disques HDD Standard sont disponibles dans toutes les régions Azure et peuvent être utilisés avec toutes les machines virtuelles Azure.

### <a name="standard-hdd-size"></a>Taille de disque HDD standard
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="standard-hdd-transactions"></a>Transactions sur le disque HDD Standard

Pour les disques HDD Standard, chaque opération d’E/S est considérée comme une seule transaction, indépendamment de la taille des E/S. Ces transactions ont un impact sur la facturation.

## <a name="billing"></a>Facturation

Lorsque vous utilisez des disques managés, les considérations de facturation suivantes s’appliquent :

- Type de disque
- Taille de disque managé
- Instantanés
- Transferts de données sortantes
- Nombre de transactions

**Taille de disque managé** : les disques managés sont facturés selon leur taille provisionnée. Azure fait correspondre la taille provisionnée (arrondie à la valeur supérieure) à la taille de disque offerte la plus proche. Pour plus d’informations sur les tailles de disque proposées, consultez les tableaux précédents. Chaque disque correspond à une offre de taille de disque provisionnée prise en charge, et est facturé en conséquence. Par exemple, si vous avez provisionné un disque SSD Standard de 200 Gio, celui-ci correspond à l’offre de taille de disque E15 (256 Gio). La facturation de n’importe quel disque configuré est calculée au prorata horaire sur la base du tarif mensuel de l’offre de stockage. Par exemple, vous provisionnez un disque E10 et le supprimez après 20 heures d’utilisation. Dans ce cas, vous êtes facturé pour l’offre E10 au prorata de 20 heures, quelle que soit la quantité de données écrites sur le disque.

**Instantanés** : Les instantanés facturés en fonction de la taille utilisée. Par exemple, vous créez un instantané d’un disque managé avec une capacité provisionnée de 64 Gio et une taille des données utilisées réelle de 10 Gio. Dans ce cas, l’instantané est facturé uniquement pour la taille des données utilisées de 10 Gio.

Pour plus d'informations sur les captures instantanées, consultez la section qui leur est consacrée dans la [Vue d'ensemble des disques managés](managed-disks-overview.md#managed-disk-snapshots).

**Transferts de données sortantes**: les [transferts de données sortantes](https://azure.microsoft.com/pricing/details/bandwidth/) (données sortant des centres de données Azure) sont facturés en fonction de la bande passante utilisée.

**Transactions** : vous êtes facturé pour le nombre de transactions effectuées sur un disque managé standard. Pour les disques SSD Standard, chaque opération d’E/S inférieure ou égale à 256 Kio de débit est considérée comme une seule opération d’E/S. Les opérations d’E/S dont le débit est supérieur à 256 Kio sont considérées comme des opérations d’E/S distinctes, de 256 Kio chacune. Pour les disques HDD Standard, chaque opération d’E/S est considérée comme une seule transaction, indépendamment de la taille des E/S.

Pour plus d’informations sur la tarification des disques managés (notamment les coûts des transactions), consultez [Tarification des disques managés](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disks-vm-reservation-fee"></a>Frais de réservation de machine virtuelle avec disques Ultra

Les machines virtuelles Azure peuvent indiquer si elles sont compatibles avec les disques Ultra. Une machine virtuelle compatible avec les disques Ultra alloue une capacité de bande passante dédiée entre l’instance de machine virtuelle de calcul et l’unité d’échelle de stockage de blocs pour optimiser les performances et réduire la latence. Lorsque vous ajoutez cette fonctionnalité sur la machine virtuelle, des frais de réservation sont facturés. Les frais de réservation ne sont imposés que si vous avez activé la fonctionnalité de disque Ultra sur la machine virtuelle sans disque Ultra attaché. Lorsqu’un disque Ultra est attaché à la machine virtuelle compatible avec les disques Ultra, les frais de réservation ne s’appliquent pas. Les frais sont calculés par processeur virtuel provisionné sur la machine virtuelle.

> [!Note]
> Pour les [tailles de machine virtuelle avec des cœurs restreints](constrained-vcpu.md), les frais de réservation sont basés sur le nombre réel de processeurs virtuels et non sur les cœurs restreints. Pour Standard_E32-8s_v3, les frais de réservation sont basés sur 32 cœurs.

Pour plus d’informations sur la tarification des disques Ultra, consultez la [page relative à la tarification des disques Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="azure-disk-reservation"></a>Réservation de disque Azure

La réservation de disque vous offre une remise sur l’achat anticipé d’un an de stockage sur disque, réduisant ainsi le coût total. Lorsque vous achetez une réservation de disque, vous sélectionnez une référence SKU de disque spécifique dans une région cible. Par exemple, vous pouvez choisir cinq disques SSD Premium P30 (1 Tio) dans la région USA Centre pour une période d’un an. L’expérience de réservation de disque est similaire aux instances de machine virtuelle réservées Azure. Vous pouvez regrouper les réservations de machines virtuelles et de disques pour optimiser vos économies. Pour le moment, la réservation de disques Azure offre un forfait d’engagement d’un an pour les références SKU de disques SSD Premium P30 (1 Tio) à P80 (32 Tio) dans toutes les régions de production. Pour plus d’informations sur la tarification des disques réservés, consultez [Page de tarification des disques Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, consultez [Tarification des disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).
