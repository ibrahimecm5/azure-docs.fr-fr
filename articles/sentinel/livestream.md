---
title: Utiliser le flux temps réel de chasse dans Microsoft Sentinel pour détecter les menaces | Microsoft Docs
description: Cet article décrit comment utiliser le flux temps réel de chasse dans Microsoft Sentinel pour effectuer le suivi des données.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 22e3eafbbae9259ddea56e9927055da08236eedb
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520082"
---
# <a name="use-hunting-livestream-in-microsoft-sentinel-to-detect-threats"></a>Utiliser le flux temps réel de chasse dans Microsoft Sentinel pour détecter les menaces

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - L’expérience de requête inter-ressource (voir les éléments marqués ci-dessous) est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.
>

Utilisez le livestream de chasse pour créer des sessions interactives dans lesquelles vous pouvez tester les nouvelles requêtes créées à mesure que des événements se produisent, recevoir les notifications des sessions quand une correspondance est trouvée et lancer des investigations si nécessaire. Vous pouvez créer rapidement une session de livestream à l’aide d’une requête Log Analytics.

- **Tester les nouvelles requêtes créées à mesure que des événements se produisent**
    
    Vous pouvez tester et modifier des requêtes sans que cela provoque des conflits avec des règles actuelles qui sont activement appliquées aux événements. Une fois que vous avez vérifié que ces nouvelles requêtes fonctionnent comme prévu, vous pouvez facilement les promouvoir en règles d’alerte personnalisées, simplement en sélectionnant une option qui élève la session au niveau d’alerte.

- **Recevoir une notification en présence de menaces**
    
    Vous pouvez comparer les flux de données de menace aux données de journal agrégées et être averti quand une correspondance est établie. Les flux de données de menace sont des flux continus de données qui ont trait à des menaces potentielles ou actuelles. La notification peut donc indiquer une menace potentielle pour votre organisation. Créez une session de livestream à la place d’une règle d’alerte personnalisée si vous souhaitez être averti d’un problème potentiel sans pour autant consacrer du temps à la gestion d’une règle d’alerte personnalisée.

- **Lancer des investigations**
    
    Si une investigation active existe déjà pour une ressource telle qu’un hôte ou un utilisateur, vous pouvez examiner une activité spécifique (ou toutes les activités) dans les données de journal à mesure que l’activité est effectuée sur cette ressource. Vous pouvez recevoir une notification quand cette activité se produit.


## <a name="create-a-livestream-session"></a>Créer une session de livestream

Vous pouvez créer une session de livestream à partir d’une requête de chasse existante ou en créer une intégralement.

1. Dans le portail Azure, accédez à **Sentinel** > **Gestion des menaces** > **Repérage**.

1. Pour créer une session de livestream à partir d’une requête de chasse :
    
    1. Sous l’onglet **Requêtes**, recherchez la requête de chasse à utiliser.
    1. Cliquez avec le bouton droit sur la requête et sélectionnez **Ajouter à livestream**. Par exemple :
    
    > [!div class="mx-imgBorder"]
    > ![créer une session de livestream à partir d’une requête de chasse Microsoft Sentinel](./media/livestream/livestream-from-query.png)

1. Pour créer une session de livestream depuis le début : 
    
    1. Sélectionnez l’onglet **Flux temps réel**.
    1. Cliquez sur **+ Nouveau livestream**.
    
1. Dans le volet **Flux temps réel** :
    
    - Si vous avez démarré une session de livestream à partir d’une requête existante, examinez la requête et apportez-y les éventuelles modifications souhaitées.
    - Si vous avez créé une session de livestream depuis le début, créez votre requête.

    > [!NOTE]
    > Livestream prend en charge les **requêtes inter-ressources** (en préversion) des données dans Azure Data Explorer. [**En savoir plus sur les requêtes inter-ressources**](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md#cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer).

1. Dans la barre de commandes, sélectionnez **Lecture**.
    
    La barre d’état, sous la barre de commandes, indique si votre session de livestream est active ou suspendue. Dans l’exemple suivant, la session est active :
    
    > [!div class="mx-imgBorder"]
    > ![créer une session de livestream à partir d’une chasse Microsoft Sentinel](./media/livestream/livestream-session.png)

1. Dans la barre de commandes, sélectionnez **Enregistrer**.
    
    Sauf si vous sélectionnez **Suspendre**, la session reste active jusqu’à ce que vous vous déconnectiez du portail Azure.

## <a name="view-your-livestream-sessions"></a>Afficher vos sessions de livestream

1. Dans le portail Azure, accédez à l’onglet **Sentinel** > **Gestion des menaces** > **Chasse** > **Flux temps réel**.

1. Sélectionnez la session de livestream que vous souhaitez voir ou modifier. Par exemple :
    
    > [!div class="mx-imgBorder"]
    > ![créer une session de livestream à partir d’une requête de chasse Microsoft Sentinel](./media/livestream/livestream-tab.png)
    
    La session de livestream sélectionnée s’ouvre. Vous pouvez ensuite la lire, la mettre en pause, la modifier, etc.

## <a name="receive-notifications-when-new-events-occur"></a>Recevoir des notifications quand de nouveaux événements se produisent

Étant donné que les notifications de livestream pour les nouveaux événements utilisent les notifications du portail Azure, vous voyez toujours ces notifications dans le portail Azure. Par exemple :

![Notification du portail Azure concernant le livestream](./media/livestream/notification.png)

Sélectionnez la notification pour ouvrir le volet **Flux temps réel**.
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Élever une session de livestream au niveau d’alerte

Vous pouvez promouvoir une session de livestream en une nouvelle alerte en sélectionnant **Élever au niveau d’alerte** dans la barre de commandes de la session de livestream correspondante :

> [!div class="mx-imgBorder"]
> ![Élever une session de livestream au niveau d’alerte](./media/livestream/elevate-to-alert.png)

Cette action ouvre l’Assistant Création de règle, qui est prérempli avec la requête associée à la session de livestream.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser le livestream de chasse dans Microsoft Sentinel. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :

- [Hunt for threats with in Azure Sentinel Preview](hunting.md) (Rechercher des menaces dans Azure Sentinel en préversion)
- [Use notebooks to run automated hunting campaigns](notebooks.md) (Utiliser des notebooks pour exécuter des campagnes de repérage automatisées)
