---
title: Utiliser l’ingestion Python pour ingérer des données dans l’Explorateur de données Azure Synapse à partir d’Event Hub (préversion)
description: Découvrez comment utiliser Python pour ingérer (charger) des données dans l’Explorateur de données Azure Synapse à partir d’Event Hub.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 64327836a5b2b9559699c4516cfaf4710b2754bb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479087"
---
# <a name="create-an-event-hub-data-connection-for-azure-synapse-data-explorer-by-using-python-preview"></a>Créer une connexion de données au hub d’événements pour l’Explorateur de données Azure Synapse à l’aide de Python (préversion)

> [!div class="op_single_selector"]
> * [Portail](data-explorer-ingest-event-hub-portal.md)
> * [En un clic](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Modèle Azure Resource Manager](data-explorer-ingest-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

Dans cet article, vous créez une connexion de données au hub d’événements pour l’Explorateur de données Azure Synapse à l’aide de Python. 

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- [Hub d’événements avec des données pour l’ingestion](data-explorer-ingest-event-hub-portal.md#create-an-event-hub).
- [Python 3.4+](https://www.python.org/downloads/).
<!-- - [Database and table policies](database-table-policies-python.md) (optional). -->

[!INCLUDE [data-explorer-ingest-event-hub-table-mapping](../includes/data-explorer-ingest-event-hub-table-mapping.md)]

[!INCLUDE [data-explorer-data-connection-install-package-python](../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Ajouter une connexion de données au hub d’événements

L’exemple suivant montre comment ajouter programmatiquement une connexion de données au hub d’événements. Consultez [Se connecter au hub d’événements](data-explorer-ingest-event-hub-portal.md#connect-to-the-event-hub) pour ajouter une connexion de données au hub d’événements à l’aide du portail Azure.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Paramètre** | **Valeur suggérée** | **Description du champ**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Votre ID de client. Également appelé ID de répertoire.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID d’abonnement que vous utilisez pour la création de ressources.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client de l’application qui peut accéder aux ressources figurant dans votre locataire.|
| client_secret | *xxxxxxxxxxxxxx* | Secret client de l’application qui peut accéder aux ressources figurant dans votre locataire. |
| resource_group_name | *testrg* | Nom du groupe de ressources qui contient votre cluster.|
| nom_cluster | *mykustocluster* | Nom de votre cluster.|
| database_name | *mykustodatabase* | Nom de la base de données cible dans votre cluster.|
| data_connection_name | *myeventhubconnect* | Nom souhaité de votre connexion de données.|
| table_name | *StormEvents* | Nom de la table cible dans la base de données cible.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Nom de votre mappage de colonnes associé à la table cible.|
| data_format | *csv* | Format de données du message.|
| event_hub_resource_id | *ID de ressource* | ID de ressource de votre hub d’événements qui contient les données à des fins d’ingestion. |
| consumer_group | *$Default* | Groupe de consommateurs de votre hub d’événements.|
| location | *USA Centre* | Emplacement de la ressource de connexion de données.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../includes/data-explorer-data-connection-clean-resources-python.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Analyser avec l’Explorateur de données](../../get-started-analyze-data-explorer.md)
- [Surveiller les pools de l’Explorateur de données](../data-explorer-monitor-pools.md)
