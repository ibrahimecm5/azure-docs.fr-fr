---
title: Surveiller les données d’Azure Cache pour Redis à l’aide de paramètres de diagnostic
titleSuffix: Azure Cache for Redis
description: Découvrez comment utiliser les paramètres de diagnostic pour surveiller les adresses IP connectées à votre instance Azure Cache pour Redis.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: how-to
ms.date: 11/3/2021
ms.custom: template-how-to
ms.openlocfilehash: 8fd6ae4eb82c7b8cb1439f621f26eef7860bcc0d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132340221"
---
# <a name="monitor-azure-cache-for-redis-data-using-diagnostic-settings"></a>Surveiller les données d’Azure Cache pour Redis à l’aide de paramètres de diagnostic

Les paramètres de diagnostic dans Azure sont utilisés pour collecter les journaux des ressources. Les journaux de ressources Azure sont émis par une ressource et fournissent des données riches et fréquentes sur le fonctionnement de cette ressource. Ces journaux sont capturés par requête et sont également appelés journaux de plans de données. Le contenu de ces journaux d’activité varie en fonction du type de ressource.

Azure Cache pour Redis utilise les paramètres de diagnostic Azure pour consigner des informations sur toutes les connexions clientes à votre cache. La journalisation et l’analyse de ce paramètre de diagnostic vous permettent de comprendre qui se connecte à vos caches et fournissent le timestamp de ces connexions. Les données de journal peuvent être utilisées pour identifier l’étendue d’une violation de sécurité et pour effectuer un audit de sécurité.

Une fois configuré, votre cache commence à enregistrer les connexions clientes entrantes par adresse IP. Il enregistre également le nombre de connexions provenant de chaque adresse IP unique. Les journaux ne sont pas cumulatifs. Ils représentent des instantanés ponctuels pris à intervalles de dix secondes.

Vous pouvez activer les paramètres de diagnostic pour les instances Azure Cache pour Redis et envoyer les journaux des ressources aux destinations suivantes :

- **Espace de travail Log Analytics** : Il n’est pas obligatoire que l’espace de travail réside dans la même région que la ressource supervisée.
- **Compte de stockage** : Il doit se trouver dans la même région que le cache.
- **Event Hub** : Les paramètres de diagnostic ne peuvent pas accéder aux ressources de l’Event Hub lorsque les réseaux virtuels sont activés. Activez le paramètre **Autoriser les services Microsoft approuvés à contourner ce pare-feu ?** dans les Event Hubs pour accorder l’accès aux ressources de votre Event Hub. L’Event Hub doit se trouver dans la même région que le cache.

Pour plus d’informations sur les exigences de diagnostic, consultez [Paramètres de diagnostic](../azure-monitor/essentials/diagnostic-settings.md?tabs=CMD).

Lorsque vous envoyez des journaux de diagnostic vers l’une ou l’autre destination, les tarifs de données normaux pour l’utilisation du compte de stockage et de l’Event Hub vous sont facturés. Vous êtes facturé sous Azure Monitor et non sous Azure Cache pour Redis. Lorsque vous envoyez des journaux à **Log Analytics**, seule l’ingestion des données par Log Analytics vous est facturée.

Pour plus d’informations, consultez [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="create-diagnostics-settings-via-the-azure-portal"></a> Créer des paramètres de diagnostic via le Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez à votre compte Azure Cache pour Redis. Ouvrez le volet **Paramètres de diagnostic** sous la section **Supervision** à gauche. Sélectionnez ensuite **Ajouter un paramètre de diagnostic**.

   :::image type="content" source="media/cache-monitor-diagnostic-settings/cache-monitor-diagnostic-setting.png" alt-text="Sélectionner les diagnostics":::

1. Dans le volet **Paramètres de diagnostic**, sélectionnez **ConnectedClientList** dans **Détails de la catégorie**.

   |Category  | Définition  | Propriétés de clé   |
   |---------|---------|---------|
   |ConnectedClientList |  Adresses IP et nombre de clients connectés au cache, enregistrés à intervalles réguliers. | `connectedClients` et imbriqués dans : `ip`, `count`, `privateLinkIpv6` |

   Pour plus d’informations sur les autres champs, consultez [Journaux de ressources](#resource-logs) ci-dessous.

1. Après avoir sélectionné vos **détails de catégorie**, envoyez vos journaux à votre destination préférée. Sélectionnez les informations sur la droite.

    :::image type="content" source="media/cache-monitor-diagnostic-settings/diagnostics-resource-specific.png" alt-text="Sélectionnez Activer la ressource spécifique":::

## <a name="create-diagnostic-setting-via-rest-api"></a> Créer un paramètre de diagnostic via l’API REST

Utilisez l’API REST Azure Monitor pour créer un paramètre de diagnostic via la console interactive. Pour plus d’informations, voir [Créer ou mettre à jour](/rest/api/monitor/diagnostic-settings/create-or-update).

### <a name="request"></a>Requête

```http
PUT https://management.azure.com/{resourceUri}/providers/Microsoft.Insights/diagnosticSettings/{name}?api-version=2017-05-01-preview
```

### <a name="headers"></a>headers

   | Paramètres/en-têtes | Valeur/description |
   |---------|---------|
   | `name` | Nom de votre paramètre de diagnostic |
   | `resourceUri` | subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Cache/Redis/{CACHE_NAME} |
   | `api-version` | 2017-05-01-preview |
   | `Content-Type` | application/json |

### <a name="body"></a>Corps

```json
{
    "properties": {
      "storageAccountId": "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/apptest/providers/Microsoft.Storage/storageAccounts/appteststorage1",
      "eventHubAuthorizationRuleId": "/subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/eventhubs/myeventhub/authorizationrules/myrule",
      "eventHubName": "myeventhub",
      "workspaceId": "/subscriptions/4b9e8510-67ab-4e9a-95a9-e2f1e570ea9c/resourceGroups/insights-integration/providers/Microsoft.OperationalInsights/workspaces/myworkspace",
      "logs": [
        {
          "category": "ConnectedClientList",
          "enabled": true,
          "retentionPolicy": {
            "enabled": false,
            "days": 0
          }
        }
      ]
    }
}
```

## <a name="create-diagnostic-setting-via-azure-cli"></a>Créer un paramètre de diagnostic via Azure CLI

Pour créer un paramètre de diagnostic avec Azure CLI, utilisez la commande `az monitor diagnostic-settings create`. Pour plus d’informations sur la commande et la description des paramètres, consultez [Créer des paramètres de diagnostic pour envoyer des journaux et des métriques de plateforme à différentes destinations](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest&branch=main&preserve-view=true#az_monitor_diagnostic_settings_create).

```azurecli
az monitor diagnostic-settings create 
    --resource /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Cache/Redis/myname
    --name constoso-setting
    --logs '[{"category": "ConnectedClientList","enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]'    
    --event-hub MyEventHubName 
    --event-hub-rule /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/authorizationrules/RootManageSharedAccessKey 
    --storage-account /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Storage/storageAccounts/myuserspace
    --workspace /subscriptions/4b9e8510-67ab-4e9a-95a9-e2f1e570ea9c/resourceGroups/insights-integration/providers/Microsoft.OperationalInsights/workspaces/myworkspace
```

## <a name="resource-logs"></a>Journaux de ressources

Ces champs et propriétés s’affichent dans la catégorie journal `ConnectedClientList`. Dans **Azure Monitor**, les journaux d’activité sont collectés dans la table `ACRConnectedClientList` sous le nom du fournisseur de ressources `MICROSOFT.CACHE`.

| Propriété ou champ du stockage Azure | Propriété des journaux Azure Monitor | Description |
| --- | --- | --- |
| `time` | `TimeGenerated` | Timestamp de la date à laquelle le journal a été généré, en UTC. |
| `location` | `Location` | Emplacement (région) dans lequel l’accès à l’instance Azure Cache pour Redis a été effectué. |
| `category` | n/a | Catégories journal disponibles : `ConnectedClientList`. |
| `resourceId` | `_ResourceId` | Ressource Azure Cache pour Redis pour laquelle les journaux sont activés.|
| `operationName` | `OperationName` | Opération Redis associée à l’enregistrement de journal. |
| `properties` | n/a | Le contenu de ce champ est décrit dans les lignes suivantes. |
| `tenant` | `CacheName` | Nom de l’instance Azure Cache pour Redis. |
| `roleInstance` | `RoleInstance` | Instance de rôle qui a consigné la liste des clients. |
| `connectedClients.ip` | `ClientIp` | Adresse IP du client Redis. |
| `connectedClients.privateLinkIpv6` | `PrivateLinkIpv6` | Adresse IPv6 de liaison privée du client Redis (le cas échéant). |
| `connectedClients.count` | `ClientCount` | Nombre de connexions au client Redis à partir de l’adresse IP associée. |

### <a name="sample-storage-account-log"></a>Exemple de journal de compte de stockage

Si vous envoyez vos journaux à un compte de stockage, le contenu des journaux ressemble à ce qui suit.

```json
{
    "time": "2021-08-05T21:04:58.0466086Z",
    "location": "canadacentral",
    "category": "ConnectedClientList",
    "properties": {
        "tenant": "mycache", 
        "connectedClients": [
            {
                "ip": "192.123.43.36", 
                "count": 86
            },
            {
                "ip": "10.1.1.4",
                "privateLinkIpv6": "fd40:8913:31:6810:6c31:200:a01:104", 
                "count": 1
            }
        ],
        "roleInstance": "1"
    },
    "resourceId": "/SUBSCRIPTIONS/E6761CE7-A7BC-442E-BBAE-950A121933B5/RESOURCEGROUPS/AZURE-CACHE/PROVIDERS/MICROSOFT.CACHE/REDIS/MYCACHE", 
    "Level": 4,
    "operationName": "Microsoft.Cache/ClientList"
}
```

## <a name="log-analytics-queries"></a>Requêtes Log Analytics

Voici quelques requêtes de base à utiliser comme modèles.

- Connexions au client Azure Cache pour Redis par heure dans la plage d’adresses IP spécifiée :

```kusto
let IpRange = "10.1.1.0/24";
ACRConnectedClientList
// For particular datetime filtering, add '| where TimeGenerated between (StartTime .. EndTime)'
| where ipv4_is_in_range(ClientIp, IpRange)
| summarize ConnectionCount = sum(ClientCount) by TimeRange = bin(TimeGenerated, 1h)
```

- Adresses IP uniques des clients Redis qui se sont connectées au cache :

```kusto
ACRConnectedClientList
| summarize count() by ClientIp
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez l’article [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md).
