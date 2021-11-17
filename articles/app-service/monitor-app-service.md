---
title: Superviser App Service avec Azure Monitor
description: Commencez ici pour découvrir comment analyser App Service
author: msangapu-msft
ms.author: msangapu
ms.topic: article
ms.service: app-service
ms.custom: subject-monitoring
ms.date: 04/16/2021
ms.openlocfilehash: d0bef29fc9e91d847046bc5d8157a70bd2fdfd9a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470981"
---
# <a name="monitoring-app-service"></a>Analyse d'App Service

Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. Cet article décrit les données d’analyse générées par App Service et expédiée à [Azure Monitor](../azure-monitor/overview.md). Vous pouvez également utiliser les [diagnostics intégrés pour analyser les ressources](troubleshoot-diagnostic-logs.md) pour aider au débogage d’une application App Service. Si vous n’êtes pas familiarisé avec les fonctionnalités d’Azure Monitor communes à tous les services Azure, consultez [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).

## <a name="monitoring-data"></a>Données de surveillance 

App Service collecte les mêmes types de données d’analyse que d’autres ressources Azure, lesquelles sont décrites dans [Analyse des données de ressources Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Consultez la référence de données sur [Supervision *App Service*](monitor-app-service-reference.md) pour obtenir des informations détaillées sur les métriques et les journaux App Service.

App Service fournit également des diagnostics intégrés pour aider au débogage des applications. Consultez [Activer la journalisation des diagnostics](troubleshoot-diagnostic-logs.md) pour plus d’informations sur l’activation des journaux intégrés. Pour analyser les instances App Service, consultez [Analyser des instances de App Service à l’aide de la vérification d’intégrité](monitor-instances-health-check.md).

## <a name="collection-and-routing"></a>Collecte et routage

Les métriques de plateforme et le journal d’activité sont collectés et stockés automatiquement, mais ils peuvent être acheminés vers d’autres emplacements à l’aide d’un paramètre de diagnostic.  

Les journaux de ressources ne sont pas collectés ni stockés tant que vous n’avez pas créé un paramètre de diagnostic et que vous ne les acheminez pas vers un ou plusieurs emplacements.

Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md). Lorsque vous créez un paramètre de diagnostic, vous spécifiez les catégories de journaux à collecter. Les catégories pour [App Service](monitor-app-service-reference.md#resource-logs) sont répertoriées dans la *Référence des données d’analyse App Service*.

Les métriques et les journaux que vous pouvez collecter sont décrits dans les sections suivantes.

## <a name="analyzing-metrics"></a>Analyse des métriques

Vous pouvez analyser les mesures pour **App Service** avec les mesures d’autres services Azure à l’aide de l’explorateur des mesures en ouvrant *Mesures* dans le menu **Azure Monitor**. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md). 

Pour obtenir la liste des mesures de plateforme collectées pour App Service, consultez [Analyse des mesures de référence des données App Service](monitor-app-service-reference.md#metrics)  

Pour référence, vous pouvez voir une liste de [toutes les métriques de ressources prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="analyzing-logs"></a>Analyse des journaux d’activité

Les données des journaux Azure Monitor sont stockées dans des tables, chacune ayant son propre ensemble de propriétés uniques.  

Tous les journaux de ressources dans Azure Monitor ont les mêmes champs suivis de champs spécifiques au service. Le schéma commun est décrit dans [Schéma des journaux des ressources Azure Monitor](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor).

Le [journal d’activité](../azure-monitor/essentials/activity-log.md) est un type de journal de plateforme qui fournit des insights de tous les événements de niveau abonnement. Vous pouvez l’afficher indépendamment ou l’acheminer vers des journaux Azure Monitor. Le routage vers des journaux Azure Monitor présente l’avantage de pouvoir utiliser Log Analytics pour exécuter des requêtes complexes.

Pour obtenir une liste des types de journaux de ressources pour App Service, consultez [Analyse de la référence des données App Service](monitor-app-service-reference.md#resource-logs)  

Pour obtenir une liste des tables requêtables utilisées par les journaux Azure Monitor et Log Analytics, consultez [Analyse de la référence des données App Service](monitor-app-service-reference.md#azure-monitor-logs-tables).  

### <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

> [!IMPORTANT]
> Quand vous sélectionnez **Journaux** dans le menu App Service, Log Analytics est ouvert avec l’étendue de requête définie sur la ressource actuelle. Cela signifie que les requêtes de journal n’incluront que les données de cette ressource. Si vous voulez exécuter une requête qui inclut des données provenant d’autres [ressources] ou d’autres services Azure, sélectionnez **Journaux** dans le menu **Azure Monitor**. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](/azure/azure-monitor/log-query/scope/).

L’exemple de requête suivant peut vous aider à analyser les journaux d’application à l’aide de `AppServiceAppLogs` :

```Kusto
AppServiceAppLogs 
| project CustomLevel, _ResourceId
| summarize count() by CustomLevel, _ResourceId
```

L’exemple de requête suivant peut vous aider à analyser les journaux HTTP à l’aide de `AppServiceHTTPLogs`, lorsque le `HTTP response code` est `500` ou supérieur :

```Kusto
AppServiceHTTPLogs 
//| where ResourceId = "MyResourceId" // Uncomment to get results for a specific resource Id when querying over a group of Apps
| where ScStatus >= 500
| reduce by strcat(CsMethod, ':\\', CsUriStem)
```

L’exemple de requête suivant peut vous aider à analyser les erreurs HTTP 500 en joignant `AppServiceConsoleLogs` et `AppserviceHTTPLogs` :

```Kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;   
```

Consultez [Requêtes Azure Monitor pour App Service](https://github.com/microsoft/AzureMonitorCommunity/tree/master/Azure%20Services/App%20Services/Queries) pour plus d’exemples de requêtes.

## <a name="alerts"></a>Alertes

Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes affectant votre système avant que vos clients ne les remarquent. Vous pouvez définir des alertes sur des [métriques](../azure-monitor/alerts/alerts-metric-overview.md), sur des [journaux](../azure-monitor/alerts/alerts-unified-log.md) et sur le [journal d’activité](../azure-monitor/alerts/activity-log-alerts.md).

Si vous exécutez une application sur App Service, [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) offre des types d’alertes supplémentaires.

La table suivante répertorie les règles d’alerte courantes et recommandées pour App Service.

| Type d’alerte | Condition | Exemples  |
|:---|:---|:---|
| Métrique | Connexions moyennes| Lorsque le nombre de connexions dépasse une valeur définie|
| Métrique | HTTP 404| Lorsque les réponses HTTP 404 dépassent une valeur définie|
| Métrique | Erreurs de serveur HTTP| Lorsque les erreurs HTTP 5xx dépassent une valeur définie|
| Journal d’activité | Créer ou mettre à jour une application web | Lorsqu'un l’application est créée ou mise à jour|
| Journal d’activité | Supprimer une application web | Lorsque l’application est supprimée|
| Journal d’activité | Redémarrer l’application web| Lorsque l’application est redémarrée|
| Journal d’activité | Arrêter l’application Web| Lorsque l’application est arrêtée|

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Analyse de la référence des données App Service](monitor-app-service-reference.md) pour obtenir une référence des mesures, des journaux et d’autres valeurs importantes créées par App Service.

- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
