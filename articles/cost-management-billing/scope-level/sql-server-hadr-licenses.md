---
title: Coexistence de SQL Server HADR et Azure Hybrid Benefit managé de manière centralisée
description: Cet article explique la manière dont coexistent l’avantage Software Assurance de SQL Server HADR et Azure Hybrid Benefit managé de manière centralisée.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: f475fa19c06eb2ecc8ed863db06f5a647308ee00
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229424"
---
# <a name="sql-server-hadr-and-centrally-managed-azure-hybrid-benefit-coexistence"></a>Coexistence de SQL Server HADR et Azure Hybrid Benefit managé de manière centralisée

L’un des avantages de Software Assurance (SA) est qu’il permet aux clients Azure d’installer et d’exécuter des instances de SQL Server passives pour la récupération d’urgence en prévision d’un événement de basculement. La gestion de niveau étendue d’Azure Hybrid Benefit prend en charge l’avantage SQL Server HADR en s’assurant que les réplicas de récupération d’urgence qualifiés ne consomment pas de licences SQL Server attribuées. Par conséquent, vous ne devez pas gérer les réplicas séparément. 

## <a name="hadr-benefit-selection"></a>Sélection de l’avantage HADR

Pour utiliser l’avantage HADR, sélectionnez **HADR** à l’aide du volet **Configurer** de la ressource **Machine virtuelle SQL**, comme illustré dans l’image suivante.

:::image type="content" source="./media/sql-server-hadr-licenses/select-hadr-benefit.png" alt-text="Capture d’écran montrant l’option HADR dans la configuration de la machine virtuelle SQL." lightbox="./media/sql-server-hadr-licenses/select-hadr-benefit.png" :::

Si la propriété HADR est sélectionnée, les licences affectées de manière centralisées à une étendue ne sont pas utilisées pour la remise sur le coût logiciel SQL de cette ressource. Le compteur de paiement à l’utilisation passe plutôt automatiquement à un compteur HADR de 0 $. Cette méthode garantit que les licences SQL attribuées ne sont utilisées que pour les ressources qui requièrent des licences. Vous ne devez pas augmenter le nombre d’attributions de licences SQL pour pouvoir bénéficier des avantages HADR. Le schéma suivant illustre ce concept.

Les prix indiqués dans l'image suivante sont donnés à titre d'exemple uniquement.

:::image type="content" source="./media/sql-server-hadr-licenses/fully-discounted-consumption-hadr.svg" alt-text="Diagramme montrant la consommation de vCore à laquelle la remise est entièrement appliquée avec HADR sélectionné." border="false" lightbox="./media/sql-server-hadr-licenses/fully-discounted-consumption-hadr.svg":::

> [!NOTE]
> L’option HADR reflète le rôle spécifique de cette instance SQL Server dans le groupe de disponibilité AlwaysOn. La sélection de cette option relève de la responsabilité du propriétaire du service ou de l’administrateur de base de données, et requiert au moins un rôle [Contributeur SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor). Cette tâche n’est pas liée aux attributions de licence de niveau étendue.

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue les [questions fréquentes (FAQ) sur Azure Hybrid Benefit managé de manière centralisée](faq-azure-hybrid-benefit-scope.yml).
- Découvrez la façon dont les remises sont appliquées dans [Qu’est-ce qu’Azure Hybrid Benefit managé de manière centralisée ?](sql-server-hadr-licenses.md)
- Découvrez comment [effectuer la transition à partir d’une expérience Azure Hybrid Benefit existante](transition-existing.md).