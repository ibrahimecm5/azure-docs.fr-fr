---
title: Surveiller les opérations SSIS avec Azure Monitor
description: Découvrez comment utiliser Azure Monitor pour surveiller les opérations SSIS dans Azure Data Factory.
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 91e291b27301cee8b7ec8f65e248e09c4f79799c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842736"
---
# <a name="monitor-ssis-operations-with-azure-monitor"></a>Surveiller les opérations SSIS avec Azure Monitor

Pour lever & déplacer vos charges de travail SSIS, vous pouvez [approvisionner le runtime d’intégration SSIS dans ADF](./tutorial-deploy-ssis-packages-azure.md) qui prend en charge les aspects suivants :

- L’exécution de packages déployés dans le catalogue SSIS (SSISDB) hébergé par un serveur Azure SQL Database/Managed Instance (modèle de déploiement de projet)
- L’exécution de packages déployés dans le système de fichiers, Azure Files ou une base de données SQL Server (MSDB) hébergée par Azure SQL Managed Instance (modèle de déploiement de package)

Une fois approvisionnée, vous pouvez [vérifier l’état opérationnel de l’IR SSIS à l’aide de Azure PowerShell ou le **surveiller** sur le hub du portail ADF](./monitor-integration-runtime.md#azure-ssis-integration-runtime). Avec le modèle de déploiement de projets, les journaux d’exécution de packages SSIS sont stockés dans des tables ou vues internes SSISDB, de sorte que vous pouvez les interroger, analyser et présenter visuellement à l’aide d’outils désignés tels que SSMS. Avec le modèle de déploiement de package, les journaux d’exécution de packages SSIS peuvent être stockés dans un système de fichiers ou Azure Files en tant que fichiers CSV que vous devez toujours analyser et traiter à l’aide d’autres outils désignés avant de pouvoir les interroger, analyser et présenter visuellement.

Désormais, avec l’intégration d’[Azure Monitor ](../azure-monitor/data-platform.md), vous pouvez interroger, analyser et présenter visuellement l’ensemble des métriques et journaux générés à partir d’opérations de runtime d’intégration SSIS et d’exécutions de packages SSIS sur portail Azure. Vous pouvez en outre déclencher des alertes sur celles-ci.

## <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>Configurer les paramètres de diagnostic et l’espace de travail pour les opérations SSIS

Pour envoyer toutes les métriques et tous les journaux générés à partir des opérations IR SSIS et des exécutions de packages SSIS à Azure Monitor, vous devez [configurer des paramètres de diagnostic et un espace de travail pour votre ADF](monitor-configure-diagnostics.md).

## <a name="ssis-operational-metrics"></a>Métriques opérationnelles SSIS

Les [métriques](../azure-monitor/essentials/data-platform-metrics.md) opérationnelles SSIS sont des compteurs de performances ou des valeurs numériques décrivant l’état des opérations de démarrage et arrêt du runtime d’intégration SSIS, ainsi que des exécutions de packages SSIS à un moment donné. Elles font partie des [métriques d’ADF dans Azure Monitor](monitor-metrics-alerts.md).

Quand vous configurez les paramètres de diagnostic et l’espace de travail de votre ADF sur Azure Monitor, le fait de cocher la case _AllMetrics_ rend les métriques opérationnelles SSIS disponibles pour [l’analyse interactive à l’aide d’Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md), [la présentation sur le tableau de bord Azure](../azure-monitor/app/tutorial-app-dashboards.md)et [les alertes en quasi-temps réel](../azure-monitor/alerts/alerts-metric.md).

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="Nommer vos paramètres et sélectionner un espace de travail Log Analytics":::

## <a name="ssis-operational-alerts"></a>Alertes opérationnelles SSIS

Pour déclencher des alertes sur les métriques opérationnelles SSIS à partir du portail ADF, [sélectionnez la page **Alertes et métriques** du hub ADF **Monitor** et suivez les instructions fournies](./monitor-visually.md#alerts).

:::image type="content" source="media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png" alt-text="Multiplier les alertes opérationnelles SSIS à partir du portail ADF":::

Pour déclencher des alertes sur les métriques opérationnelles SSIS à partir du Portail Azure, [sélectionnez la page **Alertes** du hub Azure **Monitor** et suivez les instructions fournies](monitor-metrics-alerts.md).

:::image type="content" source="media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png" alt-text="Multiplier les alertes opérationnelles SSIS à partir du Portail Azure":::

## <a name="ssis-operational-logs"></a>Journaux opérationnels SSIS

Les [journaux](../azure-monitor/logs/data-platform-logs.md) opérationnels SSIS sont des événements générés par les opérations IR SSIS et les exécutions de packages SSIS qui fournissent suffisamment de contexte sur tous les problèmes identifiés et sont utiles pour l’analyse de la cause racine. 

Quand vous configurez les paramètres de diagnostic et l’espace de travail pour votre ADF sur Azure Monitor, vous pouvez sélectionner les journaux des opérations SSIS appropriés et les envoyer à Log Analytics basé sur Azure Data Explorer. À partir de là, ils seront mis à disposition pour [l’analyse à l’aide d’un langage de requête riche](../azure-monitor/logs/log-query-overview.md), [la présentation sur le tableau de bord Azure](../azure-monitor/app/tutorial-app-dashboards.md)et [les alertes en quasi-temps réel](../azure-monitor/alerts/alerts-log.md).

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="Nommer vos paramètres et sélectionner un espace de travail Log Analytics":::

Les schémas et le contenu des journaux d’exécution de packages SSIS dans Azure Monitor et Log Analytics sont similaires à ceux des tables ou vues internes de SSISDB.

| Catégories de journaux Azure Monitor          | Tableaux Log Analytics                     | Tableaux/vues internes SSISDB              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

Pour plus d’informations sur les attributs/propriétés du journal opérationnel SSIS, consultez [Schémas sur Azure Monitor et Log Analytics pour ADF](monitor-schema-logs-events.md).

Les journaux d’exécution de package SSIS sélectionnés sont toujours envoyés à Log Analytics, indépendamment de leurs méthodes d’invocation. Par exemple, vous pouvez appeler des exécutions de packages sur des SSDT compatible avec Azure, via T-SQL sur SSMS, SQL Server Agent ou d’autres outils désignés, et comme des exécutions déclenchées ou de débogage d’activités d’exécution de package SSIS dans des pipelines ADF.

Lorsque vous interrogez des journaux des opérations de runtime d’intégration SSIS sur Logs Analytics, vous pouvez utiliser les propriétés **OperationName** et **ResultType** définies respectivement sur `Start/Stop/Maintenance/Heartbeat` et `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy`.

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query.png" alt-text="Interrogation des journaux des opérations de runtime d’intégration SSIS sur Log Analytics":::

Pour interroger l’état des nœuds SSIS IR, vous pouvez définir la propriété **OperationName** sur `Heartbeat`. Chaque nœud envoie un enregistrement `Heartbeat` par minute à Log Analytics avec la propriété **ResultType** qui indique son état : `Healthy` quand le nœud est disponible pour les exécutions de package, et `Unhealthy` sinon. Par exemple, si votre SSIS IR a deux nœuds disponibles, vous voyez toujours les enregistrements `Heartbeat` avec la propriété **ResultType** définie sur `Healthy` à chaque période d’une minute.

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query-3.png" alt-text="Interrogation des pulsations SSIS IR sur Log Analytics":::

Vous pouvez interroger les modèles suivants pour détecter si un ou plusieurs nœuds SSIS IR sont indisponibles :

* Il manque des enregistrements `Heartbeat` dans de nombreuses périodes d’une minute durant l’exécution de votre SSIS IR.
* Il y a des enregistrements `Heartbeat` avec la propriété **ResultType** définie sur `Unhealthy` dans de nombreuses périodes d’une minute durant l’exécution de votre SSIS IR.

Vous pouvez convertir les requêtes ci-dessus en [alertes](../azure-monitor/alerts/alerts-unified-log.md) et accéder à la [page de supervision SSIS IR](monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) pour confirmer à la réception de ces alertes.

Lors de l’interrogation des journaux d’exécution des packages SSIS sur Logs Analytics, vous pouvez les joindre à l’aide des propriétés **OperationId**/**ExecutionId**/**CorrelationId**. Les propriétés **OperationId**/**ExecutionId** ont toujours la valeur `1` pour toutes les opérations/exécutions relatives à des packages **non** stockés dans SSISDB/invoqués via T-SQL.

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query2.png" alt-text="Interroger les journaux d’exécution des packages SSIS sur Log Analytics":::

## <a name="next-steps"></a>Étapes suivantes

[Schéma des journaux et des événements](monitor-schema-logs-events.md)
