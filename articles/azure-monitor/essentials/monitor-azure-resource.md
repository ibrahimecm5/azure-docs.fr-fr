---
title: Superviser des ressources Azure avec Azure Monitor | Microsoft Docs
description: Décrit comment collecter et analyser des données de supervision à partir de ressources dans Azure en utilisant Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/15/2021
ms.openlocfilehash: f93cd688ef22d7765edbd50e742262e9febec14c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297837"
---
# <a name="tutorial-monitor-azure-resources-with-azure-monitor"></a>Tutoriel : Superviser des ressources Azure avec Azure Monitor
Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. Cette supervision est fournie par Azure Monitor, un service de supervision de pile complète dans Azure qui fournit un ensemble complet de fonctionnalités pour superviser vos ressources Azure en plus des ressources figurant dans d’autres clouds et sur site.

Dans ce tutoriel, vous allez voir comment :

> [!div class="checklist"]
> * Ce qu’est Azure Monitor et comment il est intégré au portail pour d’autres services Azure
> * Les types de données collectés par Azure Monitor pour les ressources Azure
> * Les outils Azure Monitor utilisés pour collecter et analyser des données

> [!NOTE]
> Ce tutoriel décrit des concepts Azure Monitor et vous guide dans les différents éléments de menu. Pour passer directement à l’utilisation des fonctionnalités Azure Monitor, commencez par le [Tutoriel : Analyser les métriques d’une ressource Azure](../essentials/tutorial-metrics.md).


## <a name="monitoring-data"></a>Données de surveillance

### <a name="azure-monitor-data-collection"></a>Collecte de données Azure Monitor
Dès que vous créez une ressource Azure, Azure Monitor est activé et démarre la collecte des métriques et des journaux d’activité. Avec une certaine configuration, vous pouvez collecter des données de supervision supplémentaires et activer des fonctionnalités supplémentaires. La plateforme de données Azure Monitor est constituée de métriques et de journaux. Chacun collecte différents types de données et active différentes fonctionnalités Azure Monitor.

- Les [métriques Azure Monitor](../essentials/data-platform-metrics.md) stockent des données numériques à partir de ressources supervisées dans une base de données de séries chronologiques. La base de données de métriques est créée automatiquement pour chaque abonnement Azure. Utilisez [Metrics Explorer](../essentials/tutorial-metrics.md) pour analyser les données des journaux Azure Monitor.
- Les [journaux Azure Monitor](../logs/data-platform-logs.md) collectent des journaux et des données de performances qui peuvent être récupérés et analysés de différentes façons à l’aide de requêtes de journal. Vous devez créer un espace de travail Log Analytics pour collecter les données de journal. Utilisez [Log Analytics](../logs/log-analytics-tutorial.md) pour analyser les données des journaux Azure Monitor.

### <a name="monitoring-data-from-azure-resources"></a>Superviser des données à partir de ressources Azure
Alors que les ressources de différents services Azure ont différentes exigences de supervision, elles génèrent des données de supervision dans les mêmes formats afin que vous puissiez utiliser les mêmes outils Azure Monitor pour analyser toutes les ressources Azure.

Les ressources Azure génèrent les données de supervision suivantes :

- [Journal d’activité](./platform-logs-overview.md) : événements de niveau abonnement qui effectuent le suivi des opérations pour chaque ressource Azure, par exemple la création d’une ressource ou le démarrage d’une machine virtuelle. Les événements de journal d’activité sont automatiquement générés et collectés pour être affichés dans le portail Azure. Vous pouvez créer un paramètre de diagnostic pour envoyer le journal d’activité aux journaux Azure Monitor.
- [Métriques de plateforme](../essentials/data-platform-metrics.md) – Valeurs numériques qui sont collectées automatiquement à intervalles réguliers et qui décrivent un certain aspect d’une ressource à un moment donné. Les métriques de plateforme sont générées et collectées automatiquement dans les Métriques Azure Monitor.
- [Journaux de ressources](./platform-logs-overview.md) – Fournissent des insights sur les opérations effectuées par une ressource Azure, par exemple l’obtention d’un secret à partir d’un coffre de clés ou l’envoi d’une demande à une base de données. Les journaux de ressources sont générés automatiquement, mais vous devez créer un paramètre de diagnostic pour les envoyer aux Journaux Azure Monitor.
- [Métriques et journaux de l’invité de machine virtuelle]() : les données de performances et de journalisation du système d’exploitation invité des machines virtuelles Azure. Vous devez installer un agent sur la machine virtuelle pour collecter ces données et les envoyer aux métriques Azure Monitor et aux journaux Azure Monitor.


## <a name="menu-options"></a>Options de menu
Bien que vous puissiez accéder aux fonctionnalités Azure Monitor à partir du menu **Superviser** dans le portail Azure, les fonctionnalités Azure Monitor peuvent être accessibles directement à partir du menu de différents services Azure. Même si différents services Azure peuvent avoir des expériences légèrement différentes, ils partagent un ensemble commun d’options de supervision dans le portail Azure. Cela inclut une **vue d’ensemble** et un **journal d’activité** et plusieurs options dans la section **Supervision** du menu. 

:::image type="content" source="media/monitor-azure-resource/menu-01.png" lightbox="media/monitor-azure-resource/menu-01.png" alt-text="Menu Superviser 1":::

:::image type="content" source="media/monitor-azure-resource/menu-02.png" lightbox="media/monitor-azure-resource/menu-02.png" alt-text="Menu Superviser 2":::


## <a name="overview-page"></a>Page Vue d’ensemble
La page **Vue d’ensemble** contient des détails sur la ressource et, souvent, son état actuel. Par exemple, une machine virtuelle affiche son état d’exécution actuel. De nombreux services Azure disposent d’un onglet **Supervision** qui inclut des graphiques pour un ensemble de métriques clés. Il s’agit d’un moyen rapide d’afficher le fonctionnement de la ressource. Vous pouvez cliquer sur l’un des graphiques pour les ouvrir dans l’explorateur de métriques pour une analyse plus détaillée. 

Consultez le [Tutoriel : Analyser les métriques d’une ressource Azure](../essentials/tutorial-metrics.md) pour en savoir plus sur l’utilisation de l’explorateur de métriques.

![Page Vue d’ensemble](media/monitor-azure-resource/overview-page.png)
### <a name="activity-log"></a>Journal d’activité 
L’élément de menu **Journal d’activité** vous permet d’afficher les entrées du [Journal d’activité](../essentials/activity-log.md) pour la ressource actuelle. 

:::image type="content" source="media/monitor-azure-resource/activity-log.png" lightbox="media/monitor-azure-resource/activity-log.png" alt-text="Journal d’activité":::

## <a name="alerts"></a>Alertes
La page **Alertes** affiche toutes les alertes récentes qui ont été déclenchées pour la ressource. Les alertes vous informent de façon proactive lorsque des conditions importantes sont trouvées dans vos données de supervision et peuvent utiliser des données de métriques ou de journaux.

Consultez le [Tutoriel : Créer une alerte de métrique pour une ressource Azure](../alerts/tutorial-metric-alert.md) ou le [Tutoriel : Créer une alerte de requête de journal pour une ressource Azure](../alerts/tutorial-log-alert.md) pour accéder à des tutoriels sur la création de règles d’alerte et l’affichage des alertes.

:::image type="content" source="media/monitor-azure-resource/alerts-view.png" lightbox="media/monitor-azure-resource/alerts-view.png" alt-text="Affichage des alertes":::

## <a name="metrics"></a>Mesures
L’élément de menu **Métriques** ouvre l’[explorateur de métriques](./metrics-getting-started.md) qui vous permet d’utiliser des métriques individuelles ou d’en combiner plusieurs pour identifier les corrélations et les tendances. Il s’agit du même explorateur de métriques que celui qui est ouvert lorsque vous cliquez sur l’un des graphiques dans la page **Vue d’ensemble**.

Consultez le [Tutoriel : Analyser les métriques d’une ressource Azure](../essentials/tutorial-metrics.md) pour en savoir plus sur l’utilisation de l’explorateur de métriques.

:::image type="content" source="media/monitor-azure-resource/metrics.png" lightbox="media/monitor-azure-resource/metrics.png" alt-text="Metrics Explorer":::


## <a name="diagnostic-settings"></a>Paramètres de diagnostic
La page **Paramètres de diagnostic** vous permet de créer un [paramètre de diagnostic](../essentials/diagnostic-settings.md) pour collecter les journaux de ressources pour votre ressource. Vous pouvez les envoyer à plusieurs emplacements, mais la méthode la plus courante consiste à les envoyer à un espace de travail Log Analytics pour pouvoir les analyser avec Log Analytics.

Pour accéder à un tutoriel sur la création d’un paramètre de diagnostic, consultez [Tutoriel : Collecter et analyser les journaux des ressources d’une ressource Azure](../essentials/tutorial-resource-logs.md).

:::image type="content" source="media/monitor-azure-resource/diagnostic-settings.png" lightbox="media/monitor-azure-resource/diagnostic-settings.png" alt-text="Paramètres de diagnostic":::



## <a name="insights"></a>Insights 
L’élément de menu **Insights** ouvre l’insight pour la ressource si le service Azure en a un. Des [insights](../monitor-reference.md) fournissent une expérience de supervision personnalisée basée sur les fonctionnalités standard et la plateforme de données d’Azure Monitor. 


Pour obtenir la liste des insights disponibles et des liens vers leur documentation, consultez [Insights et solutions principales](../monitor-reference.md#insights-and-curated-visualizations).

:::image type="content" source="media/monitor-azure-resource/insights.png" lightbox="media/monitor-azure-resource/insights.png" alt-text="Capture d’écran Insights":::

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez une compréhension de base d’Azure Monitor, commencez à analyser certaines métriques pour une ressource Azure.

> [!div class="nextstepaction"]
> [Analyser les métriques d’une ressource Azure](../essentials/tutorial-metrics.md)
