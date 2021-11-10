---
title: Configurer les journaux de diagnostic via l’API REST Azure Monitor
description: Découvrez comment configurer des journaux de diagnostic pour Azure Data Factory à l’aide de l’API REST Azure Monitor.
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 41b7a817b35b15ad18d962f5fb073fac76118444
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842831"
---
# <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Configurer les journaux de diagnostic via l’API REST Azure Monitor

Cet article décrit comment configurer des journaux de diagnostic pour Azure Data Factory à l’aide de l’API REST Azure Monitor.

## <a name="diagnostic-settings"></a>Paramètres de diagnostic

Utilisez des paramètres de diagnostic pour configurer les journaux de diagnostic des ressources autres que de calcul. Les paramètres d’un contrôle de ressource présentent les caractéristiques suivantes :

* Ils spécifient l’emplacement d’envoi des journaux de diagnostic. Il peut s’agir, part exemple, d’un compte de stockage Azure, d’un Event Hub Azure ou de journaux Azure Monitor.
* Ils spécifient les catégories de journal qui sont envoyées.
* Ils spécifient la durée pendant laquelle chaque catégorie de journal doit être conservée dans un compte de stockage.
* Une durée de rétention de zéro jour signifie que les journaux d’activité sont conservés indéfiniment. Autrement, la valeur peut également être n’importe quel nombre de jours, compris entre 1 et 2 147 483 647.
* Si des stratégies de rétention sont définies, mais que le stockage des journaux d’activité dans un compte de stockage est désactivé, les stratégies de rétention n’ont aucun effet. Par exemple, cette condition peut se produire lorsque seules les options Event Hubs ou Journaux d’activité Monitor sont sélectionnées.
* Les stratégies de rétention sont appliquées par jour. La séparation entre les jours se produit à minuit en temps universel coordonné (UTC). À la fin de la journée, les journaux des jours qui dépassent la stratégie de rétention sont supprimés. Par exemple, si vous avez une stratégie de rétention d’une journée, au début de la journée les journaux antérieurs à la veille sont supprimés.

## <a name="enable-diagnostic-logs-via-the-monitor-rest-api"></a>Activer les journaux de diagnostic via l’API REST Monitor

Utilisez l’API REST Monitor pour activer les journaux de diagnostic.

### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Créer ou mettre à jour un paramètre de diagnostic dans l’API REST Azure Monitor

#### <a name="request"></a>Requête

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

#### <a name="headers"></a>headers

* Remplacez `{api-version}` par `2016-09-01`.
* Remplacez `{resource-id}` par l’ID de la ressource dont vous voulez modifier les paramètres de diagnostic. Pour plus d’informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Attribuez à l’en-tête `Content-Type` la valeur `application/json`.
* Définissez l’en-tête d’autorisation sur le jeton web JSON que vous avez obtenu d’Azure Active Directory (AAD). Pour plus d’informations, consultez [Authentification des demandes](../active-directory/develop/authentication-vs-authorization.md).

#### <a name="body"></a>body

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Propriété | Type | Description |
| --- | --- | --- |
| **storageAccountId** |String | ID de ressource du compte de stockage auquel vous voulez envoyer les journaux de diagnostic. |
| **serviceBusRuleId** |String | ID de règle service-bus de l’espace de noms service-bus dans lequel vous voulez que les Event Hubs soient créés pour la diffusion en continu des journaux de diagnostic. L’ID de règle a le format `{service bus resource ID}/authorizationrules/{key name}`.|
| **workspaceId** | String | ID de l’espace de travail dans lequel les journaux seront enregistrés. |
|**metrics**| Valeurs de paramètre de l’exécution de pipeline à passer au pipeline appelé| Objet JSON qui mappe des noms de paramètres à des valeurs d’arguments. |
| **logs**| Type complexe| Nom d’une catégorie de journal de diagnostic pour un type de ressource. Pour obtenir la liste des catégories de journal de diagnostic pour une ressource, effectuez une opération d’obtention (GET) des paramètres de diagnostic. |
| **category**| String| Tableau de catégories de journal et leurs stratégies de rétention. |
| **timeGrain** | String | Granularité des métriques capturées au format de durée ISO 8601. La valeur de la propriété doit être `PT1M`, qui spécifie une minute. |
| **activé**| Boolean | Indique si la collection de la métrique ou catégorie de journal est activée pour cette ressource. |
| **retentionPolicy**| Type complexe| Décrit la stratégie de rétention pour une métrique ou une catégorie de journal. Cette propriété est utilisée uniquement pour les comptes de stockage. |
|**days**| Int| Nombre de jours durant lesquels les métriques ou journaux sont conservés. Si la valeur de la propriété est 0, les journaux sont conservés indéfiniment. Cette propriété est utilisée uniquement pour les comptes de stockage. |

#### <a name="response"></a>response

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Obtenez des informations sur le paramètre de diagnostic dans l’API REST Azure Monitor

#### <a name="request"></a>Requête

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

#### <a name="headers"></a>headers

* Remplacez `{api-version}` par `2016-09-01`.
* Remplacez `{resource-id}` par l’ID de la ressource dont vous voulez modifier les paramètres de diagnostic. Pour plus d’informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Attribuez à l’en-tête `Content-Type` la valeur `application/json`.
* Définissez l’en-tête d’autorisation sur un jeton web JSON que vous avez obtenu d’Azure Active Directory. Pour plus d’informations, consultez [Authentification des demandes](../active-directory/develop/authentication-vs-authorization.md).

#### <a name="response"></a>response

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Pour plus d’informations, consultez [Paramètres de diagnostic](/rest/api/monitor/diagnosticsettings).

## <a name="next-steps"></a>Étapes suivantes

[Surveiller les opérations SSIS avec Azure Monitor](monitor-ssis.md)
