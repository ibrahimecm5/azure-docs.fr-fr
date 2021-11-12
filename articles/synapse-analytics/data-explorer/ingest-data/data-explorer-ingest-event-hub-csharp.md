---
title: Utiliser l’ingestion C\# pour ingérer des données dans l’explorateur de données Azure Synapse à partir d’Event Hub (préversion)
description: Découvrez comment utiliser C\# pour ingérer (charger) des données dans l’explorateur de données Azure Synapse à partir d’Event Hub.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 9e8cf21f2cb54246152015e42d6c0e5b2cabcad5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479103"
---
# <a name="create-an-event-hub-data-connection-for-azure-synapse-data-explorer-by-using-c-preview"></a>Créer une connexion de données au hub d’événements pour l’explorateur de données Azure Synapse à l’aide de C\# (préversion)

> [!div class="op_single_selector"]
> * [Portail](data-explorer-ingest-event-hub-portal.md)
> * [En un clic](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Modèle Azure Resource Manager](data-explorer-ingest-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

Dans cet article, vous créez une connexion de données au hub d’événements pour l’explorateur de données Azure Synapse à l’aide de C\#.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- [Hub d’événements avec des données pour l’ingestion](data-explorer-ingest-event-hub-portal.md#create-an-event-hub).

> [!NOTE]
> L’ingestion de données à partir d’Event Hub dans des pools de l’explorateur de données ne fonctionne pas si votre espace de travail Synapse utilise un réseau virtuel géré avec la protection contre l’exfiltration des données activée.

- Visual Studio 2019. Téléchargez et utilisez **gratuitement** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Activez le **développement Azure** lors de l’installation de Visual Studio.

<!-- * Set [database and table policies](database-table-policies-csharp.md) (optional). -->

[!INCLUDE [data-explorer-ingest-event-hub-table-mapping](../includes/data-explorer-ingest-event-hub-table-mapping.md)]

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Ajouter une connexion de données au hub d’événements

L’exemple suivant montre comment ajouter programmatiquement une connexion de données au hub d’événements. Consultez [Se connecter au hub d’événements](data-explorer-ingest-event-hub-portal.md#connect-to-the-event-hub) pour plus d’informations sur l’ajout d’une connexion de données au hub d’événements à l’aide du portail Azure.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The Event Hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
var compression = "None";
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat, compression: compression));
```

|**Paramètre** | **Valeur suggérée** | **Description du champ**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Votre ID de client. Également appelé ID de répertoire.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID d’abonnement que vous utilisez pour la création de ressources.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client de l’application qui peut accéder aux ressources figurant dans votre locataire.|
| clientSecret | *xxxxxxxxxxxxxx* | Secret client de l’application qui peut accéder aux ressources figurant dans votre locataire.|
| resourceGroupName | *testrg* | Nom du groupe de ressources qui contient votre cluster.|
| clusterName | *mykustocluster* | Nom de votre cluster.|
| databaseName | *mykustodatabase* | Nom de la base de données cible dans votre cluster.|
| dataConnectionName | *myeventhubconnect* | Nom souhaité de votre connexion de données.|
| tableName | *StormEvents* | Nom de la table cible dans la base de données cible.|
| mappingRuleName | *StormEvents_CSV_Mapping* | Nom de votre mappage de colonnes associé à la table cible.|
| dataFormat | *csv* | Format de données du message.|
| eventHubResourceId | *ID de ressource* | ID de ressource de votre hub d’événements qui contient les données à des fins d’ingestion. |
| consumerGroup | *$Default* | Groupe de consommateurs de votre hub d’événements.|
| location | *USA Centre* | Emplacement de la ressource de connexion de données.|
| compression | *Gzip* ou *Aucune* | Type de compression de données. |

## <a name="generate-data"></a>Générer les données

Consultez l’[exemple d’application](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) qui génère des données et les envoie à un hub d’événements.

Un événement peut contenir un ou plusieurs enregistrements, jusqu’à sa limite de taille. Dans l’exemple suivant, nous envoyons deux événements, chacun avec cinq enregistrements ajoutés :

```csharp
var events = new List<EventData>();
var data = string.Empty;
var recordsPerEvent = 5;
var rand = new Random();
var counter = 0;

for (var i = 0; i < 10; i++)
{
    // Create the data
    var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = rand.Next(-30, 50) };
    var data += JsonConvert.SerializeObject(metric) + Environment.NewLine;
    counter++;

    // Create the event
    if (counter == recordsPerEvent)
    {
        var eventData = new EventData(Encoding.UTF8.GetBytes(data));
        events.Add(eventData);

        counter = 0;
        data = string.Empty;
    }
}

// Send events
eventHubClient.SendAsync(events).Wait();
```

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../includes/data-explorer-data-connection-clean-resources-csharp.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Analyser avec l’explorateur de données](../../get-started-analyze-data-explorer.md)
- [Surveiller les pools de l’explorateur de données](../data-explorer-monitor-pools.md)
