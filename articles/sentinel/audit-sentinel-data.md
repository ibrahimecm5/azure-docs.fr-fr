---
title: Auditer les requêtes et les activités de Microsoft Sentinel | Microsoft Docs
description: Cet article explique comment auditer les requêtes et les activités effectuées dans Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: d1524139049b77cfeeff58563904489ae49728ef
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518049"
---
# <a name="audit-microsoft-sentinel-queries-and-activities"></a>Auditer les requêtes et les activités Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article explique comment vous pouvez afficher les données d’audit pour les requêtes exécutées et les activités effectuées dans votre espace de travail Microsoft Sentinel, par exemple, en lien avec les exigences de conformité internes et externes dans votre espace de travail Opérations de sécurité (SOC).

Microsoft Sentinel permet d’accéder aux ressources suivantes :

- La table **AzureActivity** qui fournit des détails sur toutes les actions entreprises dans Microsoft Sentinel, telles que la modification des règles d’alerte. La table **AzureActivity** ne journalise pas de données de requête spécifiques. Pour plus d’informations, consultez [Audit avec les journaux d’activité Azure](#auditing-with-azure-activity-logs).

- La table **LAQueryLogs** qui fournit des détails sur les requêtes exécutées dans log Analytics, dont celles exécutées à partir de Microsoft Sentinel. Pour plus d’informations, consultez [Audit avec LAQueryLogs](#auditing-with-laquerylogs).

> [!TIP]
> En plus des requêtes manuelles décrites dans cet article, Microsoft Sentinel fournit un classeur intégré pour vous aider à auditer les activités dans votre environnement SOC.
>
> Dans la zone **Classeurs** de Microsoft Sentinel, recherchez le classeur **Audit d’espace de travail**.

## <a name="auditing-with-azure-activity-logs"></a>Audit avec les journaux d’activité Azure

Les journaux d’audit de Microsoft Sentinel sont conservés dans les [Journaux d’activité Azure](../azure-monitor/essentials/platform-logs-overview.md), où la table **AzureActivity** comprend toutes les actions entreprises dans votre espace de travail Microsoft Sentinel.

Vous pouvez utiliser la table **AzureActivity** pour auditer l’activité dans votre environnement SOC avec Microsoft Sentinel.

**Pour interroger la table AzureActivity** :

1. Connectez la source de données [Activité Azure](./data-connectors-reference.md#azure-activity) pour démarrer la diffusion en continu d’événements d’audit dans une nouvelle table de l’écran **Journaux** appelée AzureActivity.

1. Ensuite, interrogez les données en utilisant KQL, comme vous le feriez avec n’importe quelle autre table.

    La table **AzureActivity** inclut des données de nombreux services, dont Microsoft Sentinel. Pour filtrer uniquement les données de Microsoft Sentinel, démarrez votre requête avec le code suivant :

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    Par exemple, pour déterminer qui a été le dernier utilisateur à modifier une règle d’analyse particulière, utilisez la requête suivante (en remplaçant `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` par l’ID de la règle que vous souhaitez vérifier) :

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

Ajoutez des paramètres à votre requête pour explorer la table **AzureActivities** plus avant, en fonction de ce que vous devez signaler. Les sections suivantes fournissent d’autres exemples de requêtes à utiliser lors de l’audit avec les données de la table **AzureActivity**.

Pour plus d’informations, consultez [Données Microsoft Sentinel incluses dans les journaux d’activité Azure](#microsoft-sentinel-data-included-in-azure-activity-logs).

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>Rechercher toutes les actions entreprises par un utilisateur spécifique au cours des dernières 24 heures

La requête de table **AzureActivity** suivante répertorie toutes les actions entreprises par un utilisateur Azure AD spécifique au cours des dernières 24 heures.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>Rechercher toutes les opérations de suppression

La requête de table **AzureActivity** suivante répertorie toutes les opérations de suppression effectuées dans votre espace de travail Microsoft Sentinel.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
```

### <a name="microsoft-sentinel-data-included-in-azure-activity-logs"></a>Données Microsoft Sentinel incluses dans les journaux d’activité Azure

Les journaux d’audit de Microsoft Sentinel sont conservés dans les [Journaux d’activité Azure](../azure-monitor/essentials/platform-logs-overview.md) et incluent les types d’informations suivants :

|Opération  |Types d’informations  |
|---------|---------|
|**Créé le**     |Règles d'alerte <br> Commentaires de cas <br>Commentaires sur l’incident <br>Recherches enregistrées<br>Watchlists    <br>Workbooks     |
|**Supprimé**     |Règles d'alerte <br>Signets <br>Connecteurs de données <br>Incidents <br>Recherches enregistrées <br>Paramètres <br>Rapports de renseignement sur les menaces <br>Watchlists      <br>Workbooks <br>Workflow  |
|**Updated**     |  Règles d'alerte<br>Signets <br> Cas <br> Connecteurs de données <br>Incidents <br>Commentaires sur l’incident <br>Rapports de renseignement sur les menaces <br> Workbooks <br>Workflow       |
|     |         |

Vous pouvez également utiliser les journaux d’activité Azure pour rechercher des autorisations et des licences utilisateur.

Par exemple, le tableau suivant répertorie les opérations sélectionnées trouvées dans les journaux d’activité Azure avec la ressource spécifique dont les données de journal sont extraites.

|Nom d’opération| Type de ressource|
|----|----|
|Créer ou mettre à jour un classeur |Microsoft.Insights/workbooks|
|Supprimer un classeur |Microsoft.Insights/workbooks|
|Définir un flux de travail |Microsoft.Logic/workflows|
|Supprimer un flux de travail |Microsoft.Logic/workflows|
|Créer une recherche enregistrée |Microsoft.OperationalInsights/workspaces/savedSearches|
|Supprimer une recherche enregistrée |Microsoft.OperationalInsights/workspaces/savedSearches|
|Mettre à jour des règles d’alerte |Microsoft.SecurityInsights/alertRules|
|Supprimer des règles d’alerte |Microsoft.SecurityInsights/alertRules|
|Mettre à jour des actions de réponse aux règles d’alerte |Microsoft.SecurityInsights/alertRules/actions|
|Supprimer des actions de réponse aux règles d’alerte |Microsoft.SecurityInsights/alertRules/actions|
|Mettre à jour des signets |Microsoft.SecurityInsights/bookmarks|
|Supprimer des signets |Microsoft.SecurityInsights/bookmarks|
|Mettre à jour des cas |Microsoft.SecurityInsights/Cases|
|Mettre à jour une investigation de cas |Microsoft.SecurityInsights/Cases/investigations|
|Créer des commentaires de cas |Microsoft.SecurityInsights/Cases/comments|
|Mettre à jour des connecteurs de données |Microsoft.SecurityInsights/dataConnectors|
|Supprimer des connecteurs de données |Microsoft.SecurityInsights/dataConnectors|
|Mettre à jour les paramètres |Microsoft.SecurityInsights/settings|
| | |

Pour plus d’informations, consultez [Schéma d’événement du journal d’activité Azure](../azure-monitor/essentials/activity-log-schema.md).

## <a name="auditing-with-laquerylogs"></a>Audit avec LAQueryLogs

La table **LAQueryLogs** fournit des détails sur les requêtes de journal exécutées dans Log Analytics. Étant donné que Log Analytics est utilisé comme magasin de données sous-jacent de Microsoft Sentinel, vous pouvez configurer votre système pour collecter des données LAQueryLogs dans votre espace de travail Microsoft Sentinel.

Les données LAQueryLogs incluent des informations telles que les suivantes :

- Quand des requêtes ont été exécutées.
- Qui a exécuté des requêtes dans Log Analytics.
- Quel outil a été utilisé pour exécuter des requêtes dans Log Analytics, tel Microsoft Sentinel
- Textes de requête proprement dits.
- Données de performances à chaque exécution de requête.

> [!NOTE]
> - La table **LAQueryLogs** inclut uniquement les requêtes exécutées dans le panneau Journaux de Microsoft Sentinel. Elle n’inclut pas les requêtes exécutées par des règles d’analyse planifiée, à l’aide du **Graphique d’examen** ou dans la page **Chasse** de Microsoft Sentinel.
> - Un bref délai peut s’écouler entre le moment où une requête est exécutée et celui où les données apparaissent dans la table **LAQueryLogs**. Nous vous recommandons d’attendre environ 5 minutes avant d’interroger la table **LAQueryLogs** afin d’obtenir des données d’audit.

**Pour interroger la table LAQueryLogs** :

1. La table **LAQueryLogs** n’est pas activée par défaut dans votre espace de travail Log Analytics. Pour utiliser les données de la table **LAQueryLogs** lors de l’audit dans Microsoft Sentinel, commencez par activer la table **LAQueryLogs** dans la zone **Paramètres de diagnostic** de votre espace de travail Log Analytics.

    Pour plus d’informations, consultez [Requêtes d’audit dans les journaux d’Azure Monitor](../azure-monitor/logs/query-audit.md).

1. Ensuite, interrogez les données en utilisant KQL, comme vous le feriez avec n’importe quelle autre table.

    Par exemple, la requête suivante indique le nombre de requêtes qui ont été exécutées au cours de la semaine dernière, sur une base quotidienne :

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

Les sections suivantes présentent d’autres exemples de requêtes à exécuter sur la table **LAQueryLogs** lors de l’audit d’activités dans votre environnement SOC à l’aide de Microsoft Sentinel.

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>Nombre de requêtes exécutées où la réponse n’a pas été « OK »

La requête suivante sur la table **LAQueryLogs** affiche le nombre de requêtes exécutées, où autre chose qu’une réponse HTTP **200 OK** a été reçue. Par exemple, ce nombre inclut les requêtes dont l’exécution a échoué.

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>Afficher les utilisateurs pour les requêtes faisant un usage intensif du processeur

La requête suivante sur la table **LAQueryLogs** répertorie les utilisateurs qui ont exécuté les requêtes faisant l’usage le plus intensif du processeur, en fonction de l’UC utilisée et de la durée de la requête.

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>Afficher les utilisateurs qui ont exécuté le plus de requêtes au cours la semaine dernière

La requête suivante sur la table **LAQueryLogs** répertorie les utilisateurs qui ont exécuté le plus de requêtes au cours de la semaine dernière.

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-microsoft-sentinel-activities"></a>Configuration des alertes pour les activités de Microsoft Sentinel

Vous pouvez utiliser des ressources d’audit Microsoft Sentinel pour créer des alertes proactives.

Par exemple, si vous avez des tables sensibles dans votre espace de travail Microsoft Sentinel, utilisez la requête suivante afin d’être averti chaque fois que ces tables sont interrogées :

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```

## <a name="monitor-microsoft-sentinel-with-workbooks-rules-and-playbooks"></a>Superviser Microsoft Sentinel avec des workbooks, des règles et des playbooks

Utilisez les propres fonctionnalités de Microsoft Sentinel pour superviser les événements et les actions qui se produisent dans Microsoft Sentinel.

- **Supervisez avec des workbooks**. Les workbooks suivants ont été créés pour superviser l’activité de l’espace de travail :

  - **Audit de l’espace de travail**. Contient des informations sur les utilisateurs de l’environnement qui effectuent des actions, sur les actions qu’ils ont effectuées et bien plus encore.
  - **Efficacité analytique**. Fournit des insights sur les règles analytiques utilisées, les tactiques MITRE les plus couvertes et les incidents générés à partir des règles.
  - **Efficacité des opérations de sécurité**. Présente les métriques sur les performances de l’équipe SOC, les incidents ouverts, les incidents fermés et plus encore. Ce workbook peut être utilisé pour afficher les performances de l’équipe et mettre en évidence les zones qui peuvent faire défaut et nécessitent une attention particulière.
  - **Supervision de l’intégrité de la collecte de données**. Permet de surveiller les ingestions bloquées ou arrêtées.

  Pour plus d’informations, consultez [Classeurs Microsoft Sentinel courants](top-workbooks.md).

- **Surveillez le délai d’ingestion**.  Si vous avez des inquiétudes concernant le délai d’ingestion, [définissez une variable dans une règle analytique](ingestion-delay.md) pour représenter le délai.

  Par exemple, la règle analytique suivante peut aider à garantir que les résultats n’incluent pas de doublons et que les journaux ne sont pas oubliés lors de l’exécution des règles :

  ```kusto
  let ingestion_delay= 2min;let rule_look_back = 5min;CommonSecurityLog| where TimeGenerated >= ago(ingestion_delay + rule_look_back)| where ingestion_time() > (rule_look_back)
  - Calculating ingestion delay
    CommonSecurityLog| extend delay = ingestion_time() - TimeGenerated| summarize percentiles(delay,95,99) by DeviceVendor, DeviceProduct
  ```

  Pour plus d’informations, consultez [Automatiser la gestion des incidents dans Microsoft Sentinel à l’aide de règles d’automatisation](automate-incident-handling-with-automation-rules.md).

- **Supervisez l’intégrité du connecteur de données** à l’aide du playbook [Connector Health Push Notification Solution](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Send-ConnectorHealthStatus) pour surveiller l’ingestion bloquée ou arrêtée et envoyer des notifications quand un connecteur a arrêté la collecte de données ou que des machines ont cessé de générer des rapports.

## <a name="next-steps"></a>Étapes suivantes

Dans Microsoft Sentinel, utilisez le classeur d’**audit de l’espace de travail** pour auditer les activités dans votre environnement SOC.

Pour plus d’informations, consultez [Visualiser et superviser vos données](monitor-your-data.md).
