---
title: Actions webhook pour les alertes de journal dans les alertes Azure
description: Décrit comment configurer des notifications Push d’alerte de journalisation avec une action webhook et des personnalisations disponibles
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 27e92c0434dcc78600f6b5a28ab6bb346c1ac87d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012318"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Actions webhook pour les règles d’alerte de journal

L’[alerte de journal](alerts-log.md) prend en charge la [configuration des groupes d’actions webhook](./action-groups.md#webhook). Dans cet article, nous allons décrire les propriétés disponibles. Les actions webhook permettent d’appeler une seule requête HTTP POST. Le service appelé doit prendre en charge les webhooks et savoir comment utiliser la charge utile qu’il reçoit.

> [!NOTE]
> Nous vous recommandons d’utiliser le [schéma d’alerte commun](../alerts/alerts-common-schema.md) pour vos intégrations de webhook. Le schéma d’alerte commun offre l’avantage de générer une seule charge utile d’alerte extensible et unifiée sur tous les services d’alerte dans Azure Monitor. Pour des règles d’alerte de journal contenant une charge utile JSON personnalisée définie, l’activation du schéma d’alerte courant a pour effet de rétablir le schéma de charge utile décrit [ici](../alerts/alerts-common-schema-definitions.md#log-alerts). Cela signifie que si vous souhaitez définir une charge utile JSON personnalisée, le webhook ne peut pas utiliser le schéma d’alerte commun. Les alertes sur lesquelles le schéma commun est activé ont une limite de taille maximale de 256 Ko par alerte ; les alertes de plus grande taille n’incluent pas les résultats de la recherche. Lorsque les résultats de la recherche ne sont pas inclus, vous devez utiliser `LinkToFilteredSearchResultsAPI` et `LinkToSearchResultsAPI` pour accéder aux résultats de la requête via l’API Log Analytics.

## <a name="sample-payloads"></a>Exemples de charges utiles
Cette section présente des exemples de charge utile pour les webhooks pour les alertes de journal. Les exemples de charge utile incluent des exemples lorsque la charge utile est standard et quand elle est personnalisée.

### <a name="log-alert-for-all-resources-logs-from-api-version-2020-08-01"></a>Alerte de journal pour les autres journaux de ressources (à partir de la version `2020-08-01` de l’API)

L’exemple de charge utile suivant concerne un webhook standard lorsqu’il est utilisé pour les alertes de journal basées sur des journaux de ressources :

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-for-log-analytics-up-to-api-version-2018-04-16"></a>alytique des journaux d'activité (jusqu’à la version de l’API `2018-04-16`)
L’exemple de charge utile suivant est destiné à une action webhook standard utilisée pour les alertes basées sur Log Analytics :

> [!NOTE]
> La valeur du champ « Gravité » change si vous avez [basculé sur l’API scheduledQueryRules actuelle](../alerts/alerts-log-api-switch.md) à partir de l’[API d’alerte Log Analytique des journaux d’activité héritée](./api-alerts.md).

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
```

### <a name="log-alert-for-application-insights-up-to-api-version-2018-04-16"></a>Alerte de journal pour Application Insights (jusqu’à la version de l’API `2018-04-16`)
L’exemple de charge utile suivant concerne un webhook standard lorsqu’il est utilisé pour les alertes de journal basées sur des ressources Application Insights :
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

### <a name="log-alert-with-a-custom-json-payload-up-to-api-version-2018-04-16"></a>Alerte de journal avec une charge utile JSON personnalisée (jusqu’à la version de l’API `2018-04-16`)

> [!NOTE]
> Le webhook JSON personnalisé n’est pas pris en charge à partir de la version de l’API `2021-08-01`.

Propriétés de l’action webhook par défaut et noms de paramètres JSON personnalisés :

| Paramètre | Variable | Description |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Nom de la règle d’alerte. |
| *Niveau de gravité* |#severity |Gravité définie pour l’alerte de journal déclenchée. |
| *AlertThresholdOperator* |#thresholdoperator |Opérateur de seuil de la règle d’alerte. |
| *AlertThresholdValue* |#thresholdvalue |Valeur de seuil de la règle d’alerte. |
| *LinkToSearchResults* |#linktosearchresults |Lien vers le portail Analytics qui retourne les enregistrements de la requête ayant créé l’alerte. |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |Lien vers l’API Analytics qui retourne les enregistrements de la requête ayant créé l’alerte. |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |Lien vers le portail Analytics qui retourne les enregistrements de la requête filtrés par combinaisons de valeurs de dimensions ayant créé l’alerte. |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |Lien vers l’API Analytics qui retourne les enregistrements de la requête filtrés par combinaisons de valeurs de dimensions ayant créé l’alerte. |
| *ResultCount* |#searchresultcount |Nombre d’enregistrements dans les résultats de recherche. |
| *Heure de fin de l’intervalle de recherche* |#searchintervalendtimeutc |Heure de fin de la requête au format UTC, au format mm/jj/aaaa HH:mm:ss AM/PM. |
| *Intervalle de recherche* |#searchinterval |Fenêtre de temps de la règle d’alerte, au format HH:mm:ss. |
| *Heure de début de l’intervalle de recherche* |#searchintervalstarttimeutc |Heure de début de la requête au format UTC, au format mm/jj/aaaa HH:mm:ss AM/PM. 
| *SearchQuery* |#searchquery |Requête de recherche de journal utilisée par la règle d’alerte. |
| *SearchResults* |"IncludeSearchResults": true|Enregistrements retournés par la requête sous forme de table JSON, limitée aux 1 000 premiers enregistrements. « IncludeSearchResults » : true est ajouté à une définition de webhook JSON personnalisée en tant que propriété de niveau supérieur. |
| *Dimensions* |« IncludeDimension »" : true|Combinaisons de valeurs de dimensions ayant déclenché une alerte en tant que section JSON. « IncludeDimensions » : true est ajouté à une définition de webhook JSON personnalisée en tant que propriété de niveau supérieur. |
| *Type d’alerte*| #alerttype | Le type de règle d’alerte de journal configuré en tant que [Mesure de métriques ou Nombre de résultats](./alerts-unified-log.md#measure).|
| *WorkspaceID* |#workspaceid |ID de votre espace de travail Log Analytics. |
| *ID d’application* |#applicationid |ID de votre application Application Insights. |
| *Identifiant d’abonnement* |#subscriptionid |ID de votre abonnement Azure utilisé. | 

Vous pouvez utiliser l’option **Inclure une charge utile JSON personnalisée pour webhook** pour obtenir une charge utile JSON personnalisée à l’aide des paramètres. Vous pouvez également générer des propriétés supplémentaires.
Par exemple, vous pouvez spécifier la charge utile personnalisée suivante qui inclut un paramètre unique appelé *text*. Le service appelé par ce webhook s’attend à recevoir ce paramètre :

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Cette charge utile donne ce qui suit quand elle est envoyée au webhook :

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Les variables d’un webhook personnalisé doivent être spécifiées dans un boîtier JSON. Par exemple, le fait de référencer « #searchresultcount » dans l’exemple de webhook génère une sortie en fonction des résultats de l’alerte.

Pour inclure les résultats de la recherche, ajoutez **IncludeSearchResults** en tant que propriété de niveau supérieur dans le JSON personnalisé. Les résultats de la recherche sont inclus en tant que structure JSON, ce qui signifie que les résultats ne peuvent pas être référencés dans des champs définis personnalisés. 

> [!NOTE]
> Le bouton **Afficher le webhook** à côté de l’option **Inclure une charge utile JSON personnalisée pour webhook** affiche un aperçu de ce qui a été fourni. Il ne contient aucune donnée réelle, mais il est représentatif du schéma JSON utilisé.

Par exemple, pour créer une charge utile personnalisée qui inclut uniquement le nom de l’alerte et les résultats de la recherche, utilisez cette configuration : 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Voici un exemple de charge utile pour une action de webhook personnalisée pour une alerte de journal quelconque :
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [alertes de journal dans les alertes Azure ](./alerts-unified-log.md).
- Comprendre comment [gérer les alertes de journal dans Azure](alerts-log.md).
- Créer et gérer des [groupes d’actions dans Azure](./action-groups.md).
- En savoir plus sur [Application Insights](../logs/log-query-overview.md).
- Découvrez plus en détail les [requêtes dans les journaux](../logs/log-query-overview.md).
