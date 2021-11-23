---
title: Redondance interzone dans App Service Environment
description: Vue d’ensemble de la redondance interzone dans un environnement App Service.
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: 3ce5a0925ae4c1dc1bc33fec73987455d612d661
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526372"
---
# <a name="availability-zone-support-for-app-service-environments"></a>Prise en charge des zones de disponibilité pour les environnements App Service

> [!NOTE]
> Cet article concerne la fonctionnalité App Service Environment v3 qui est utilisée avec les plans App Service v2 Isolé
>

Vous pouvez déployer un environnement App Service (ASE) dans des [Zones de disponibilité (AZ)](../../availability-zones/az-overview.md). Cette architecture est également appelée « redondance de zone ». Quand un ASE est configuré avec la redondance interzone, la plateforme répartit automatiquement les instances de plan App Service de l’ASE entre les trois zones de la région sélectionnée. Si une capacité supérieure à trois est spécifiée et que le nombre d’instances est divisible par trois, les instances sont réparties uniformément. Dans le cas contraire, les instances au-delà de 3*N sont réparties entre les une ou deux zones restantes.

Vous configurez la redondance interzone quand vous créez votre ASE et que tous les plans App Service créés dans cet ASE sont redondants interzone. La redondance interzone peut seulement être spécifiée pendant la création d’un *nouvel* environment App Service. La redondance interzone est prise en charge uniquement dans un [sous-ensemble de régions](./overview.md#regions).

En cas de défaillance d’une zone, la plateforme App Service détecte les instances perdues et tente automatiquement de trouver de nouvelles instances de remplacement. Si vous avez également configuré la mise à l’échelle automatique et si celle-ci décide que davantage d’instances sont nécessaires, la mise à l’échelle automatique adresse également à App Service une demande d’ajout d’instances (le comportement de la mise à l’échelle automatique est indépendant du comportement de la plateforme App Service). Notez qu’il n’existe aucune garantie que les demandes d’instances dans un scénario de zone défaillante aboutissent, car le remplissage des instances perdues se produit dans la mesure du possible. La solution recommandée est de mettre à l’échelle vos plans App Service pour tenir compte de la perte d’une zone.

Les applications déployées dans un ASE redondant interzone continuent à s’exécuter et à servir le trafic, même si d’autres zones de la même région tombent en panne. Cependant, il se peut que des comportements hors runtime, dont la mise à l’échelle de plan App Service, la création d’application, la configuration d’application et la publication d’application soient affectés par une interruption dans d’autres zones de disponibilité. La redondance interzone pour App Service Environment garantit un temps d’activité continu uniquement pour les applications déployées.

Quand la plateforme App Service alloue des instances à un plan App Service redondant interzone dans un ASE, elle utilise le [meilleur équilibrage de zone possible qu’offre les groupes de machines virtuelles identiques Azure sous-jacents](../../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing). Un plan App Service est « équilibré » si chaque zone a le même nombre d’instances ou qu’il y a +/- 1 instance dans toutes les autres zones utilisées par le plan App Service.

## <a name="pricing"></a>Tarifs

 Il y a un coût minimal de neuf instances de plan App Service dans un ASE redondant interzone. Aucun frais n’est facturé pour la prise en charge des zones de disponibilité si vous disposez d’au moins neuf instances de plan App Service. Si vous avez moins de neuf instances (de toutes tailles) dans l’ensemble de vos plans App Service dans l’ASE redondant interzone, la différence entre neuf et le nombre d’instances exécutées est facturée comme des instances Windows I1v2 supplémentaires.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [zones de disponibilité](../../availability-zones/az-overview.md)
