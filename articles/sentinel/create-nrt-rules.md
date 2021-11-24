---
title: Utiliser des règles d’analyse de détection en quasi-temps réel dans Microsoft Sentinel | Microsoft Docs
description: Cet article explique comment afficher et créer des règles d’analyse de détection en quasi-temps réel dans Microsoft Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 69e639ad74388756217f06922d8cfcae62d28197
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521690"
---
# <a name="work-with-near-real-time-nrt-detection-analytics-rules-in-microsoft-sentinel"></a>Utiliser des règles d’analyse de détection en quasi-temps réel (NRT) dans Microsoft Sentinel

> [!IMPORTANT]
>
> - Les règles NRT sont en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Les [règles d’analyse en quasi-temps réel](near-real-time-rules.md) de Microsoft Sentinel offrent une détection des menaces prête à l’emploi à la minute près. Ce type de règle a été conçu pour être très réactif en exécutant sa requête à des intervalles d’une minute seulement.

Ces modèles ont une application limitée comme indiqué ci-dessous, mais la technologie évolue rapidement.

## <a name="view-near-real-time-nrt-rules"></a>Afficher les règles de quasi-temps réel (NRT)

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Analyse**.

1. Sous l’onglet **Règles actives** du panneau **Analyse**, filtrez la liste des modèles **NRT** :

    1. Cliquez sur le filtre **Type de règle**, puis sur la liste déroulante qui apparaît dessous.

    1. Désactivez la case à cocher **Sélectionner tout**, puis cochez **NRT**.

    1. Si nécessaire, cliquez sur le haut de la liste déroulante pour la rétracter, puis cliquez sur **OK**.

## <a name="create-nrt-rules"></a>Créer des règles NRT

Vous créez des règles NRT de la même façon que vous créez des [règles analytiques de requête planifiée](detect-threats-custom.md) ordinaires :

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Analyse**.

1. Sélectionnez **Créer** dans la barre de boutons, puis **Règle de requête NRT** dans la liste déroulante.

    :::image type="content" source="media/create-nrt-rules/create-nrt-rule.png" alt-text="Créez une règle NRT.":::

1. Suivez les instructions de l’[**Assistant Règle analytique**](detect-threats-custom.md).

    La configuration des règles NRT est essentiellement la même que celle de règles analytiques planifiées. 

    - Vous pouvez faire référence aux [**watchlists**](watchlists.md) et aux [**flux de renseignements sur les menaces**](understand-threat-intelligence.md) dans votre logique de requête.

    - Vous pouvez utiliser toutes les méthodes d’enrichissement des alertes : [**mappage d’entité**](map-data-fields-to-entities.md), [**Détails personnalisés**](surface-custom-details-in-alerts.md) et détails de [**détails de l’alerte**](customize-alert-details.md).

    - Vous pouvez choisir la manière de regrouper des alertes en incidents, et supprimer une requête quand un résultat particulier a été généré.

    - Vous pouvez automatiser les réponses aux alertes et aux incidents.

    Toutefois, en raison de la [**nature et des limitations des règles NRT**](near-real-time-rules.md#considerations), les fonctionnalités suivantes des règles analytiques planifiées ne sont *pas disponibles* dans l’Assistant :

    - La **planification des requêtes** n’est pas configurable, car les requêtes sont automatiquement planifiées pour s’exécuter une fois par minute avec une période de recherche arrière d’une minute. 
    - Le **seuil d’alerte** n’est pas pertinent, car une alerte est toujours générée.
    - La configuration du **regroupement d’événements** n’est pas disponible, car les événements sont toujours regroupés dans l’alerte créée par la règle qui capture les événements. Les règles NRT ne peuvent pas produire d’alerte pour chaque événement.

    En outre, la requête elle-même présente les exigences suivantes :

    - La requête elle-même ne peut faire référence qu’à une seule table et ne peut pas contenir d’unions ou de jointures.

    - Vous ne pouvez pas exécuter la requête sur plusieurs espaces de travail.

    - En raison des limites de taille des alertes, votre requête doit utiliser des instructions `project` pour inclure uniquement les champs nécessaires de votre table. Dans le cas contraire, les informations que vous souhaitez mettre en surface risquent d’être tronquées.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à créer des règles d’analyse en quasi-temps réel dans Microsoft Sentinel.

- Apprenez-en davantage sur les [règles d’analyse en quasi-temps réel dans Microsoft Sentinel](near-real-time-rules.md).
- Explorez d’autres [types de règles d’analyse](detect-threats-built-in.md).
