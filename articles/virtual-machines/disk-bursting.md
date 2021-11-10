---
title: Mode rafale des disques managés
description: Découvrez le bursting de disque pour les disques Azure et les machines virtuelles Azure.
author: roygara
ms.author: rogarana
ms.date: 11/03/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: bd9cc8e9a9e60655d7d1805c1ceae4168353ec20
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506318"
---
# <a name="managed-disk-bursting"></a>Mode rafale des disques managés

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Azure offre la possibilité d’accélérer les performances des E/S par seconde et des Mo/s du stockage sur disque : on parle alors de « bursting », aussi bien pour les machines virtuelles que pour les disques. Vous pouvez efficacement utiliser le bursting à la fois au niveau des machines virtuelles et au niveau des disques pour obtenir de meilleures performances.

Le bursting pour les machines virtuelles Azure et le bursting pour les ressources de disque ne sont pas interdépendants. Vous n’avez pas besoin d’avoir une machine virtuelle prenant en charge le bursting pour pouvoir utiliser un disque attaché prenant en charge le bursting. De même, vous n’avez pas besoin d’avoir un disque attaché prenant en charge le bursting pour utiliser une machine virtuelle prenant en charge le bursting.

## <a name="common-scenarios"></a>Scénarios courants
Le mode rafale peut être très avantageux pour les scénarios suivants :
- **Amélioration des temps de démarrage** : avec le bursting, le démarrage de votre instance est plus rapide. Par exemple, le disque de système d’exploitation par défaut pour les machines virtuelles Premium est le disque P4 dont les performances plafonnent à 120 IOPS et 25 Mo/s. Avec le bursting, les performances du disque P4 peuvent atteindre 3 500 IOPS et 170 Mo/s, ce qui permet de diviser jusqu’à 6 le temps de démarrage.
- **Gestion des programmes de traitement par lots** : certaines charges de travail d’application sont cycliques par nature. Elles nécessitent une performance de base la plupart du temps et des performances supérieures pour de courtes périodes. Ce peut être le cas d’un programme de comptabilité qui traite des transactions quotidiennes nécessitant un trafic de disque assez modéré. À la fin du mois, le programme rapproche des rapports, ce qui nécessite un trafic de disque beaucoup plus élevé.
- **Pics de trafic** : les serveurs web et leurs applications peuvent être confrontés à des pics de trafic à tout moment. Si votre serveur web est adossé à des machines virtuelles ou à des disques utilisant le bursting, il est mieux équipé pour gérer les pics de trafic. 

## <a name="disk-level-bursting"></a>Bursting de disque

Actuellement, il existe deux types de disques gérés qui peuvent utiliser le bursting, les [SSD Premium](disks-types.md#premium-ssds) et les [SSD standard](disks-types.md#standard-ssds). Les autres types de disque ne peuvent actuellement pas utiliser le bursting. Il existe deux modèles de bursting pour les disques :

- Un modèle de bursting à la demande, où un bursting de disque a lieu chaque fois que les besoins du disque dépassent sa capacité actuelle. Ce modèle entraîne des frais supplémentaires à chaque bursting de disque. Le bursting à la demande est disponible uniquement pour les disques SSD Premium d’une taille supérieure à 512 Gio.
- Un modèle basé sur les crédits, où le bursting de disque n’a lieu que si des crédits de bursting ont été accumulés dans le compartiment de crédits associé. Avec ce modèle, les burstings de disque n’entraînent pas de frais supplémentaires. Le bursting basé sur les crédits est disponible uniquement pour les disques SSD Premium et SSD Standard de 512 Gio ou moins.

Les disques [SSD Premium](disks-types.md#premium-ssds) d’Azure peuvent utiliser le modèle de bursting, mais les disques [SSD standard](disks-types.md#standard-ssds) ne proposent actuellement que le bursting basé sur le crédit.

En outre, le [niveau de performance des disques managés peut être modifié](disks-change-performance.md), ce qui peut être idéal dans le cas où votre charge de travail s’exécuterait sinon avec le bursting.

|  |Bursting basé sur les crédits  |Bursting à la demande  |Modification du niveau de performance  |
|---------|---------|---------|---------|
| **Scénarios**|Idéal pour la mise à l’échelle à court terme (30 minutes ou moins).|Idéal pour la mise à l’échelle à court terme (sans limite de temps).|Idéal dans le cas où votre charge de travail s’exécuterait sinon en continu avec le bursting.|
|**Coût**     |Gratuit         |Le coût est variable. Pour plus d’informations, consultez la section [Facturation](#billing).        |Le coût de chaque niveau de performance est fixe. Pour plus d’informations, consultez les [tarifs des disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).         |
|**Disponibilité**     |Disponible uniquement pour les disques SSD Premium et SSD Standard de 512 Gio ou moins.         |Disponible uniquement pour les disques SSD Premium de taille supérieure à 512 Gio.         |Disponible pour toutes les tailles de disques SSD Premium.         |
|**Activation**     |Activé par défaut sur les disques éligibles.         |Doit être activé par l’utilisateur.         |L’utilisateur doit modifier manuellement son niveau.         |

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Étapes suivantes

- Pour activer le bursting à la demande, consultez [Activer le bursting à la demande](disks-enable-bursting.md).
- Pour savoir comment obtenir des insights sur vos ressources utilisant le bursting, consultez [Métriques de bursting de disque](disks-metrics.md).
- Pour voir exactement quel est le niveau de bursting applicable à chaque taille de disque, consultez [Cibles de scalabilité et de niveau de performance des disques de machines virtuelles](disks-scalability-targets.md).