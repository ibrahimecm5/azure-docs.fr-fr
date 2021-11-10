---
title: Régions et zones de disponibilité Azure
description: Découvrez les régions et les zones de disponibilité ainsi que leur fonctionnement, pour vous aider à obtenir une véritable résilience.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: rarco
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: 48cb14166a229e6bcaf3337f0be92b7d07acb3d6
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843116"
---
# <a name="regions-and-availability-zones"></a>Régions et zones de disponibilité

Les régions et les zones de disponibilité Azure sont conçues pour vous aider à faire en sorte que vos charges de travail stratégiques soient fiables et résilientes. Azure gère plusieurs zones géographiques. Ces démarcations discrètes définissent les limites de la reprise d’activité et de la résidence des données dans une ou plusieurs régions Azure. La maintenance de nombreuses régions garantit la prise en charge des clients dans le monde entier. 

## <a name="regions"></a>Régions

Chaque région Azure propose des centres de données déployés dans un périmètre défini par la latence. Ils sont connectés par un réseau dédié régional avec une faible latence. Cette conception garantit que les services Azure d’une même région offrent les meilleures performances et la meilleure sécurité possibles.

## <a name="availability-zones"></a>Zones de disponibilité

Les *zones de disponibilité* sont des emplacements physiquement séparés au sein de chaque région Azure qui tolèrent les défaillances locales. Les défaillances sont aussi bien des défaillances logicielles et matérielles que des événements de type tremblements de terre, inondations et incendies. La tolérance aux défaillances est obtenue grâce à la redondance et à l’isolation logique des services Azure. Pour garantir la résilience, au moins trois zones de disponibilité distinctes sont présentes dans toutes les régions qui ont des zones de disponibilité. 

Les zones de disponibilité Azure sont connectées par un réseau hautes performances avec une latence aller-retour de moins de 2 ms. Elles permettent à vos données d’être synchronisées et accessibles en cas de problème. Chaque zone est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’une infrastructure réseau indépendants. Les zones de disponibilité sont conçues de telle sorte que si une zone est affectée, les services, la capacité et la haute disponibilité de la région sont pris en charge par les deux autres zones.

![Image montrant des localisations de zones de disponibilité distinctes au sein d’une région Azure.](media/availability-zones.png)

Les localisations des centres de données sont sélectionnées à l’aide de critères rigoureux d’évaluation des risques de vulnérabilité. Ce processus identifie tous les risques significatifs propres au centre de données et prend en compte les risques partagés entre les zones de disponibilité.

Avec les zones de disponibilité, vous pouvez concevoir et utiliser des applications et des bases de données qui passent automatiquement d’une zone à l’autre sans interruption. Les zones de disponibilité Azure sont hautement disponibles, tolérantes aux pannes et plus scalables que les infrastructures traditionnelles ayant un ou plusieurs centres de données.

Vous pouvez créer une haute disponibilité dans votre architecture d’application en utilisant des services Azure qui offrent une résilience. Colocalisez vos ressources de calcul, de stockage, de réseau et de données dans une zone de disponibilité, et répliquez cette configuration dans d’autres zones de disponibilité.

Les *services Azure compatibles avec les zones de disponibilité* sont conçus pour fournir le niveau approprié de résilience et de flexibilité. Ils peuvent être configurés de deux façons. Un service peut être *redondant interzone*, avec une réplication automatique entre les zones, ou *zonal*, avec des instances épinglées à une zone spécifique. Vous pouvez également combiner ces approches.

Certaines organisations nécessitent une haute disponibilité des zones de disponibilité et une protection contre les phénomènes à grande échelle et les catastrophes régionales. Les régions Azure sont conçues pour offrir une protection contre les catastrophes localisées avec les zones de disponibilité, et une protection contre les catastrophes affectant une région ou une grande zone géographique avec la reprise d’activité, en utilisant une autre région. Pour en savoir plus sur la continuité d’activité, la reprise d’activité et la réplication entre les régions, consultez [Réplication entre les régions dans Azure](../best-practices-availability-paired-regions.md).

![Image montrant des zones de disponibilité qui protègent contre les catastrophes localisées, et les catastrophes affectant une région ou une grande zone géographique en utilisant une autre région.](media/availability-zones-region-geography.png)

## <a name="azure-regions-with-availability-zones"></a>Régions Azure avec des zones de disponibilité
Azure fournit l’empreinte mondiale la plus complète de tous les fournisseurs de cloud et ouvre rapidement de nouvelles régions et zones de disponibilité.

| Amérique | Europe | Afrique | Asie-Pacifique |
|--------------------|----------------------|---------------------|----------------|
| Brésil Sud | France Centre | Afrique du Sud Nord | Australie Est |
| Centre du Canada | Allemagne Centre-Ouest | | Inde centrale\* |
| USA Centre | Europe Nord | | Japon Est |
| USA Est | Norvège Est | | Centre de la Corée |
| USA Est 2 | Sud du Royaume-Uni | | Asie Sud-Est |
| États-Unis - partie centrale méridionale | Europe Ouest |  | Asie Est |
| Gouvernement américain - Virginie | Suède | | |
| USA Ouest 2 | | | |
| USA Ouest 3 | | | |

\* Pour en savoir plus sur les zones de disponibilité et sur la prise en charge des services disponibles dans ces régions, contactez votre représentant Microsoft. Pour savoir quelles sont les prochaines régions qui prendront en charge les zones de disponibilité, consultez [Zones géographiques Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Étapes suivantes

- [Engagement de Microsoft à étendre les zones de disponibilité Azure à plus de régions](https://azure.microsoft.com/blog/our-commitment-to-expand-azure-availability-zones-to-more-regions/)
- [Services Azure prenant en charge les zones de disponibilité](az-region.md)
- [Services Azure](region-types-service-categories-azure.md)
