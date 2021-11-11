---
title: Connexion de données Event Grid pour Azure Synapse Data Explorer (préversion)
description: Cet article fournit une vue d’ensemble de la façon d’ingérer (charger) des données dans Azure Synapse Data Explorer à partir d’Event Grid.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 8eb32d529c8024733ba6d0bbdec72cc370ebb5d6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478963"
---
# <a name="event-grid-data-connection-preview"></a>Connexion de données Event Grid (préversion)

L’ingestion Event Grid est un pipeline qui écoute le stockage Azure et met à jour Azure Data Explorer pour extraire des informations quand des événements associés à des abonnements se produisent. L’Explorateur de données offre une ingestion continue depuis Stockage Azure (Stockage Blob et ADLSv2) avec un abonnement [Azure Event Grid](/azure/event-grid/overview) pour des notifications d’objet blob créé ou renommé, et le streaming de ces notifications vers l’Explorateur de données via un hub d’événements.

Le pipeline d’ingestion Event Grid passe par plusieurs étapes. Vous créez une table cible dans l’Explorateur de données dans laquelle les [données d’un format particulier](#data-format) sont ingérées. Vous créez ensuite une connexion de données Event Grid dans l’Explorateur de données. La connexion de données Event Grid doit connaître les informations de [routage des événements](#events-routing), telles que la table à laquelle envoyer les données et le mappage de table. Vous spécifiez également les [propriétés d’ingestion](#ingestion-properties), qui décrivent les données à ingérer, la table cible et le mappage. Vous pouvez générer des exemples de données et [charger des objets blob](#upload-blobs) ou [renommer des objets blob](#rename-blobs) pour tester votre connexion. [Supprimez des objets blob](#delete-blobs-using-storage-lifecycle) après ingestion. Ce processus peut également être géré via le [portail Azure](data-explorer-ingest-event-grid-portal.md). <!-- , using [one-click ingestion](one-click-ingestion-new-table.md), programmatically with [C#](data-connection-event-grid-csharp.md) or [Python](data-connection-event-grid-python.md), or with the [Azure Resource Manager template](data-connection-event-grid-resource-manager.md). -->

<!-- For general information about data ingestion in Data Explorer, see [Data Explorer data ingestion overview](ingest-data-overview.md). -->

## <a name="data-format"></a>Format de données

- Examinez les [formats pris en charge](data-explorer-ingest-data-supported-formats.md).
- Examinez les [compressions prises en charge](data-explorer-ingest-data-supported-formats.md#supported-data-compression-formats).
    - La taille des données non compressées d’origine doit faire partie des métadonnées d’objets blob, sinon l’Explorateur de données l’estimera. La limite de taille décompressée d’ingestion par fichier est de 4 Go.

> [!NOTE]
> L’abonnement aux notifications Event Grid peut être défini sur des comptes de stockage Azure pour `BlobStorage`, `StorageV2` ou [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction).

## <a name="ingestion-properties"></a>Propriétés d’ingestion

Vous pouvez spécifier les [propriétés d’ingestion](data-explorer-ingest-data-properties.md) de l’ingestion d’objets blob via les métadonnées d’objet blob.
Vous pouvez définir les propriétés suivantes :

[!INCLUDE [ingestion-properties-event-grid](../includes/data-explorer-event-grid-ingestion-properties.md)]

## <a name="events-routing"></a>Routage d’événements

Quand vous configurez une connexion de stockage Blob vers un cluster Data Explorer, spécifiez les propriétés de la table cible :

- Nom de la table
- format de données
- mapping

Cette configuration est le routage par défaut pour vos données, parfois appelé `static routing`.
Vous pouvez également spécifier des propriétés de la table cible pour chaque objet blob, à l’aide des métadonnées d’objet blob. Les données sont routées dynamiquement, comme spécifié par les [propriétés d’ingestion](#ingestion-properties).

L’exemple suivant montre comment définir les propriétés d’ingestion sur les métadonnées de l’objet blob avant de le charger. Les objets blob sont routés vers différentes tables.

Pour plus d’informations, consultez [Charger des objets blob](#upload-blobs).

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="upload-blobs"></a>Charger des objets blob

Vous pouvez créer un objet blob à partir d’un fichier local, définir des propriétés d’ingestion sur les métadonnées de l’objet blob, puis le charger. Par exemple, consultez [Ingérer des objets blob dans l’Explorateur de données en s’abonnant à des notifications Event Grid](data-explorer-ingest-event-grid-portal.md#generate-sample-data)

> [!NOTE]
> - Utilisez `BlockBlob` pour générer les données. `AppendBlob` n’est pas pris en charge.
> - L’utilisation du SDK de stockage Azure Data Lake Gen2 requiert l’utilisation de `CreateFile` pour charger des fichiers et de `Flush` à la fin avec le paramètre de fermeture défini sur « true ».
<!-- > For a detailed example of Data Lake Gen2 SDK correct usage, see [upload file using Azure Data Lake SDK](data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk). -->
> - Quand le point de terminaison Event Hub n’accuse pas réception d’un événement, Azure Event Grid active un mécanisme de nouvelle tentative. Si cette nouvelle tentative de remise échoue, Event Grid peut délivrer les événements non remis à un compte de stockage en utilisant un processus de *mise en file d’attente de lettres mortes*. Pour plus d’informations, consultez la page [Distribution et nouvelle tentative de distribution de messages avec Event Grid](/azure/event-grid/delivery-and-retry#retry-schedule-and-duration).

## <a name="rename-blobs"></a>Renommer des objets blob

Lorsque vous utilisez ADLSv2, vous pouvez renommer un objet blob pour déclencher l’ingestion d’objets blob dans l’Explorateur de données. Par exemple, consultez [Ingérer des objets blob dans l’Explorateur de données en s’abonnant à des notifications Event Grid](data-explorer-ingest-event-grid-portal.md#generate-sample-data).

> [!NOTE]
> - Le renommage de répertoire est possible dans ADLSv2, mais ne déclenche pas d’événements de *renommage d’objets blob* ni l’ingestion d’objets blob dans le répertoire. Pour ingérer les objets blob suite au renommage, renommez directement les objets blob souhaités.
> - Si vous avez défini des filtres pour suivre des sujets spécifiques lors de [la création de la connexion de données](data-explorer-ingest-event-grid-portal.md#create-an-event-grid-data-connection).<!-- or while creating [Event Grid resources manually](ingest-data-event-grid-manual.md#create-an-event-grid-subscription), these filters are applied on the destination file path. -->

## <a name="delete-blobs-using-storage-lifecycle"></a>Supprimer des objets blob à l’aide du cycle de vie du stockage

L’Explorateur de données ne supprimera pas les objets blob après l’ingestion. Pour gérer la suppression des objets blob, utilisez le [cycle de vie du stockage Blob Azure](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal). Il est recommandé de conserver les objets blob pendant trois à cinq jours.

## <a name="known-event-grid-issues"></a>Problèmes connus liés à Event Grid

- Lorsque vous utilisez l’Explorateur de données pour [exporter](/azure/data-explorer/kusto/management/data-export/export-data-to-storage?context=/azure/synapse-analytics/context/context) les fichiers utilisés pour l’ingestion Event Grid, notez les éléments suivants : 
    - Les notifications Event Grid ne sont pas déclenchées si la chaîne de connexion fournie à la commande d’exportation ou à une [table externe](/azure/data-explorer/kusto/management/data-export/export-data-to-an-external-table?context=/azure/synapse-analytics/context/context) est une chaîne de connexion au [format ADLS Gen2](/azure/data-explorer/kusto/api/connection-strings/storage?context=/azure/synapse-analytics/context/context#azure-data-lake-storage-gen2) (par exemple, `abfss://filesystem@accountname.dfs.core.windows.net`), mais que le compte de stockage n’est pas activé pour l’espace de noms hiérarchique.
    - Si le compte n’est pas activé pour l’espace de noms hiérarchique, la chaîne de connexion doit utiliser le format de [stockage Blob](/azure/data-explorer/kusto/api/connection-strings/storage?context=/azure/synapse-analytics/context/context#azure-blob-storage) (par exemple, `https://accountname.blob.core.windows.net`). L’exportation fonctionne comme prévu même si vous utilisez la chaîne de connexion ADLS Gen2, mais les notifications ne seront pas déclenchées et l’ingestion Event Grid ne fonctionnera pas.

## <a name="next-steps"></a>Étapes suivantes

- [Ingérer des objets blob dans l’Explorateur de données en s’abonnant à des notifications Event Grid](data-explorer-ingest-event-grid-portal.md)
<!-- - [Create an Event Grid data connection for Data Explorer by using C#](data-connection-event-grid-csharp.md)
- [Create an Event Grid data connection for Data Explorer by using Python](data-connection-event-grid-python.md)
- [Create an Event Grid data connection for Data Explorer by using Azure Resource Manager template](data-connection-event-grid-resource-manager.md)
- [Use one-click ingestion to ingest CSV data from a container to a new table in Data Explorer](one-click-ingestion-new-table.md) -->
