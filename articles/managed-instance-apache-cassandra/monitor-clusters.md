---
title: Azure Monitor
description: Azure Monitor
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: dcb4dcd6037f8efcbac33e970566babf10c7d262
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858654"
---
# <a name="monitor-azure-managed-instance-for-apache-cassandra-using-azure-monitor"></a>Surveiller Azure Managed Instance pour Apache Cassandra à l’aide d’Azure Monitor

Azure Managed Instance pour Apache Cassandra fournit des métriques et une journalisation des diagnostics à l’aide d’[Azure Monitor](../azure-monitor/overview.md). 

## <a name="azure-metrics"></a>Métriques Azure

Vous pouvez visualiser les métriques pour Azure Managed Instance pour Apache Cassandra en accédant à votre ressource de cluster, puis en sélectionnant l’onglet Métriques. Vous pouvez ensuite choisir parmi les métriques et les agrégations disponibles.

:::image type="content" source="./media/azure-monitor/metrics.png" alt-text="Visualiser les métriques":::

## <a name="diagnostic-settings-in-azure"></a>Paramètres de diagnostic dans Azure

Les paramètres de diagnostic dans Azure sont utilisés pour collecter les journaux des ressources. Les journaux de ressources Azure sont émis par une ressource et fournissent des données riches et fréquentes sur le fonctionnement de cette ressource. Ces journaux sont capturés par requête et sont également appelés « journaux des plans de données ». Les opérations Delete, Insert et readFeed sont des exemples d’opérations de plan de données. Le contenu de ces journaux d’activité varie en fonction du type de ressource.

Les indicateurs de performance de la plateforme et le journal d’activité sont collectés automatiquement, mais vous devez créer un paramètre de diagnostic pour collecter les journaux des ressources ou les transférer en dehors d’Azure Monitor. Vous pouvez activer les paramètres de diagnostic pour Azure Managed Instance pour Apache Cassandra, et envoyer les journaux des ressources aux sources suivantes :
- Espaces de travail Log Analytics
  - Les données envoyées à Log Analytics peuvent être écrites dans des tables **Diagnostics Azure (héritées)** ou **Spécifique à la ressource (préversion)** 
- Event Hub
- Compte de stockage
  
> [!NOTE]
> Nous vous recommandons de créer le paramètre de diagnostic dans un mode spécifique de la ressource.

## <a name="create-diagnostic-settings-via-the-azure-portal"></a><a id="create-setting-portal"></a> Créer des paramètres de diagnostic via le portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Accédez à votre ressource de cluster Azure Managed Instance pour Apache Cassandra. 

    :::image type="content" source="./media/azure-monitor/cluster.png" alt-text="Sélectionner un cluster":::
 
1. Ouvrez le volet **Paramètres de diagnostic** sous la **section Surveillance**, puis sélectionnez **Ajouter un paramètre de diagnostic**.

    :::image type="content" source="./media/azure-monitor/settings.png" alt-text="Ajouter des paramètres de diagnostic":::


1. Dans le volet **paramètres de diagnostic**, choisissez un nom pour votre paramètre, puis sélectionnez **Détails des catégories**. La catégorie **CassandraAudit** enregistre les opérations d’audit et de CQL. La catégorie **CassandraLogs** enregistre les opérations du serveur Cassandra. Envoyez ensuite vos journaux à la destination de votre choix. Si vous envoyez des journaux à un **espace de travail Log Analytics**, assurez-vous de sélectionner **Ressource spécifique** comme table Destination. 

    :::image type="content" source="./media/azure-monitor/preferred-categories.png" alt-text="Sélectionner une catégorie":::

   > [!WARNING]
   > Si vous envoyez des journaux à un espace de travail Log Analytics, le premier affichage des journaux peut prendre jusqu’à **20 minutes** . En attendant, les tables spécifiques des ressources (présentées ci-après sous Azure Managed Instance pour Apache Cassandra) ne sont pas visibles.  


1. Une fois que la journalisation des diagnostics est configurée et que les données circulent, vous pouvez accéder à l’onglet **Journaux** et interroger les journaux de diagnostic disponibles à l’aide d’Azure Data Explorer. Pour plus d’informations sur Azure Monitor et le langage de requête Kusto, consultez [cet article](../azure-monitor/logs/log-query-overview.md). 

    :::image type="content" source="./media/azure-monitor/query.png" alt-text="Journaux des requêtes":::

## <a name="create-diagnostic-setting-via-azure-cli"></a><a id="create-setting-cli"></a> Créer un paramètre de diagnostic via Azure CLI
Pour créer un paramètre de diagnostic avec Azure CLI, utilisez la commande [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create). Pour une description des paramètres de cette commande, consultez sa documentation.

```azurecli-interactive
    logs='[{"category":"CassandraAudit","enabled":true,"retentionPolicy":{"enabled":true,"days":3}},{"category":"CassandraLogs","enabled":true,"retentionPolicy":{"enabled":true,"days":3}}]'
    resourceId='/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDB/cassandraClusters/{CLUSTER_NAME}'
    workspace='/subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}'

    az monitor diagnostic-settings create  --name tvk-doagnostic-logs-cassandra --resource $resourceId --logs  $logs --workspace $workspace --export-to-resource-specific true
```

## <a name="create-diagnostic-setting-via-rest-api"></a><a id="create-diagnostic-setting"></a> Créer un paramètre de diagnostic via l’API REST
Utilisez l’[API REST Azure Monitor](/rest/api/monitor/diagnosticsettings/createorupdate) pour créer un paramètre de diagnostic via la console interactive.
> [!Note]
> Si vous utilisez l’API SQL, définissez la propriété **logAnalyticsDestinationType** sur **Dedicated** pour activer les tables spécifiques à la ressource.

### <a name="request"></a>Requête

   ```HTTP
   PUT
   https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
   ```

### <a name="headers"></a>headers

   |Paramètres/en-têtes  | Valeur/description  |
   |---------|---------|
   |name     |  Nom de votre paramètre de diagnostic      |
   |resourceUri     |   subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/{ACCOUNT_NAME}/providers/microsoft.insights/diagnosticSettings/{DIAGNOSTIC_SETTING_NAME}      |
   |api-version     |    2017-05-01-preview     |
   |Content-Type     |    application/json     |

### <a name="body"></a>Corps

```json
{
    "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/{ACCOUNT_NAME}/providers/microsoft.insights/diagnosticSettings/{DIAGNOSTIC_SETTING_NAME}",
    "type": "Microsoft.Insights/diagnosticSettings",
    "name": "name",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": null,
        "serviceBusRuleId": null,
        "workspaceId": "/subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "logs": [
            {
                "category": "CassandraAudit",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "CassandraLogs",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ],
        "logAnalyticsDestinationType": "Dedicated"
    },
    "identity": null
}
```


## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez l’article [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md).