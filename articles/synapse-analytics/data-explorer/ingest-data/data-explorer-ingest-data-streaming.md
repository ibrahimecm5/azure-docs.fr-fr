---
title: Configurer l’ingestion en streaming dans votre pool Azure Synapse Data Explorer
description: Découvrez comment configurer votre pool Azure Synapse Data Explorer et commencer à charger des données avec l’ingestion en streaming.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: d7fdc238adda294a570b7656d62368a5d4feae9b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479133"
---
# <a name="configure-streaming-ingestion-on-your-azure-synapse-data-explorer-pool-preview"></a>Configurer l’ingestion en streaming dans votre pool Azure Synapse Data Explorer (préversion)

L’ingestion en streaming permet de charger des données lorsque vous avez besoin d’une faible latence entre l’ingestion et l’interrogation. Vous pouvez utiliser l’ingestion en streaming dans les scénarios suivants :

- Lorsqu’une latence inférieure à une seconde est nécessaire.
- Pour optimiser le traitement opérationnel d’un grand nombre de tables, lorsque le flux de données de chaque table est relativement faible (quelques enregistrements par seconde), mais que le volume global d’ingestion de données est élevé (des milliers d’enregistrements par seconde).

Si le flux de données de chaque table est élevé (plus de 4 Go par heure), nous vous conseillons d’utiliser l’[ingestion par lots](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context).

Pour plus d’informations sur les méthodes d’ingestion, consultez [Vue d’ensemble de l’ingestion des données](data-explorer-ingest-data-overview.md).

## <a name="choose-the-appropriate-streaming-ingestion-type"></a>Choisir le type d’ingestion de streaming approprié

Deux types d’ingestion de streaming sont pris en charge :

| Type d’ingestion | Description |
| -- | -- |
| **Event Hub** ou **IoT Hub** | Les hubs sont configurés comme des sources de données de streaming de table.<br />Pour plus d’informations sur la configuration de ces paramètres, consultez [**Event Hub**](data-explorer-ingest-event-hub-overview.md). <!--  or [**IoT Hub**](ingest-data-iot-hub.md) data ingestion methods --> |
| **Ingestion personnalisée** | Une ingestion personnalisée vous demande d’écrire une application qui utilise l’une des [bibliothèques de client](/azure/data-explorer/kusto/api/client-libraries?context=/azure/synapse-analytics/context/context) Azure Synapse Data Explorer.<br />Utilisez les informations de cette rubrique pour configurer l’ingestion personnalisée. L’[exemple d’application pour l’ingestion en streaming avec C#](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) peut également vous être utile. |

Aidez-vous du tableau suivant pour choisir le type d’ingestion le mieux adapté à votre environnement :

|Critère|Event Hub/IoT Hub|Ingestion personnalisée|
|---------|---------|---------|
|Délai de données entre le lancement de l’ingestion et le moment où les données sont disponibles pour une requête | Délai plus long | Délai plus court |
|Surcharge de développement | Installation rapide et facile, aucune surcharge de développement | Frais de développement élevés pour créer une application, ingérer des données, gérer les erreurs et garantir la cohérence des données |

> [!NOTE]
> L’ingestion de données à partir d’Event Hub dans des pools de l’Explorateur de données ne fonctionne pas si votre espace de travail Synapse utilise un réseau virtuel managé avec la protection contre l’exfiltration des données activée.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Obtenez les points de terminaison de requête et d’ingestion de données.
    [!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]

## <a name="performance-and-operational-considerations"></a>Considérations relatives aux performances et aux opérations

Les contributeurs principaux qui peuvent impacter l’ingestion en streaming sont les suivants :

- **Spécification de calcul** : niveau de performance de l’ingestion en streaming et échelles de capacité avec des tailles de pool Data Explorer accrues. Le nombre de demandes d’ingestion simultanées est limité à six par cœur. Par exemple, pour un type de charge de travail à 16 cœurs, tel qu’Optimisé pour le calcul (Grand) et À stockage optimisé (Grand), la charge maximale prise en charge est de 96 demandes d’ingestion simultanées. Pour 2 types de charges de travail de base, tels qu’Optimisé pour le calcul (Très petit), la charge maximale prise en charge est de 12 demandes d’ingestion simultanées.
- **Limite de taille des données** : la taille limite des données par demande d’ingestion en streaming est de 4 Mo.
- **Mises à jour des schémas** : les mises à jour des schémas, telles que la création et la modification des tables et des mappages d’ingestion, peuvent prendre jusqu’à cinq minutes pour le service d’ingestion de streaming. Pour plus d’informations, consultez [Ingestion de streaming et changements de schéma](/azure/data-explorer/kusto/management/data-ingestion/streaming-ingestion-schema-changes?context=/azure/synapse-analytics/context/context).
- **Capacité du disque SSD** : l’activation de l’ingestion en streaming sur un pool Data Explorer, même lorsque les données ne sont pas ingérées via streaming, utilise une partie du disque SSD local des machines du pool Data Explorer pour les données d’ingestion en streaming et réduit le stockage disponible pour le cache chaud.

## <a name="enable-streaming-ingestion-on-your-data-explorer-pool"></a>Activer l’ingestion en streaming sur votre pool Data Explorer

Avant de pouvoir utiliser l’ingestion en streaming, vous devez activer cette capacité sur votre pool Data Explorer et définir une [stratégie d’ingestion en streaming](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context). Vous pouvez activer cette capacité lors de la [création du pool Data Explorer](#enable-streaming-ingestion-while-creating-a-new-data-explorer-pool) ou l’[ajouter à un pool Data Explorer existant](#enable-streaming-ingestion-on-an-existing-data-explorer-pool).

> [!WARNING]
> Avant d’activer l’ingestion en streaming, passez en revue les [limitations](#limitations).

### <a name="enable-streaming-ingestion-while-creating-a-new-data-explorer-pool"></a>Activer l’ingestion en streaming lors de la création d’un nouveau pool Data Explorer

Vous pouvez activer l’ingestion en streaming lors de la création d’un pool Data Explorer à l’aide d’Azure Synapse Studio ou du portail Azure.

#### <a name="studio"></a>[Studio](#tab/azure-studio)

Lorsque vous créez un pool Data Explorer à l’aide des étapes décrites dans [Créer un pool Data Explorer à l’aide de Synapse Studio](../data-explorer-create-pool-studio.md#create-a-new-data-explorer-pool), dans l’onglet **Paramètres supplémentaires**, sélectionnez **Ingestion en streaming** > **Activée**.

:::image type="content" source="../media/ingest-data-streaming/create-data-explorer-pool-advanced-settings-studio.png" alt-text="Activer l’ingestion en streaming lors de la création d’un pool Data Explorer dans Azure Synapse Data Explorer.":::

#### <a name="portal"></a>[Portail](#tab/azure-portal)

Lorsque vous créez un pool Data Explorer à l’aide des étapes décrites dans [Créer un pool Data Explorer à l’aide du portail Azure](../data-explorer-create-pool-portal.md#create-a-new-data-explorer-pool), dans l’onglet **Paramètres supplémentaires**, sélectionnez **Ingestion en streaming** > **Activée**.

:::image type="content" source="../media/ingest-data-streaming/create-data-explorer-pool-advanced-settings-portal.png" alt-text="Activer l’ingestion en streaming lors de la création d’un pool Data Explorer dans Azure Synapse Data Explorer.":::

---

### <a name="enable-streaming-ingestion-on-an-existing-data-explorer-pool"></a>Activer l’ingestion en streaming sur un pool Data Explorer existant

Si vous disposez d’un pool Data Explorer existant, vous pouvez activer l’ingestion en streaming à l’aide du portail Azure.

1. Dans le portail Azure, accédez à votre pool Data Explorer.
1. Dans **Paramètres**, sélectionnez **Configurations**.
1. Dans le volet **Configurations**, sélectionnez **Activé** pour activer **Ingestion de streaming**.
1. Sélectionnez **Enregistrer**.

### <a name="create-a-target-table-and-define-the-policy"></a>Créer une table cible et définir la stratégie

Créez une table pour recevoir les données d’ingestion en streaming et définissez la stratégie associée à l’aide d’Azure Synapse Studio ou du portail Azure.

#### <a name="studio"></a>[Studio](#tab/azure-studio)

1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Développer**.
1. Sous **Scripts KQL**, sélectionnez **&plus;** (Ajouter une nouvelle ressource) > **Script KQL**. Dans le volet de droite, vous pouvez nommer votre script.
1. Dans le menu **Connecter à**, sélectionnez *contosodataexplorer*.
1. Dans le menu **Utiliser la base de données**, sélectionnez *TestDatabase*.
1. Collez la commande suivante, puis sélectionnez **Exécuter** pour créer la table.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

1. Copiez l’une des commandes suivantes dans le **volet de requête**, puis sélectionnez **Exécuter**. Cela définit la [stratégie d’ingestion en streaming](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context) pour la table que vous avez créée ou la base de données qui contient cette table.

    > [!TIP]
    > Une stratégie qui est définie au niveau de la base de données s’applique à toutes les tables existantes et futures de la base de données.

    - Pour définir la stratégie de la table que vous avez créée, utilisez ceci :

        ```kusto
        .alter table TestTable policy streamingingestion enable
        ```

    - Pour définir la stratégie de la base de données contenant la table que vous avez créée, utilisez ceci :

        ```kusto
        .alter database StreamingTestDb policy streamingingestion enable
        ```

#### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le portail Azure Synapse Analytics, accédez à votre pool Data Explorer.
1. Sélectionnez **Requête**.
1. Pour créer la table qui recevra les données par le biais de l’ingestion de streaming, copiez la commande suivante dans le **volet de requête**, puis sélectionnez **Exécuter**.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

1. Copiez l’une des commandes suivantes dans le **volet de requête**, puis sélectionnez **Exécuter**. Cela définit la [stratégie d’ingestion en streaming](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context) pour la table que vous avez créée ou la base de données qui contient cette table.

    > [!TIP]
    > Une stratégie qui est définie au niveau de la base de données s’applique à toutes les tables existantes et futures de la base de données.

    - Pour définir la stratégie de la table que vous avez créée, utilisez ceci :

        ```kusto
        .alter table TestTable policy streamingingestion enable
        ```

    - Pour définir la stratégie de la base de données contenant la table que vous avez créée, utilisez ceci :

        ```kusto
        .alter database StreamingTestDb policy streamingingestion enable
        ```

---

## <a name="create-a-streaming-ingestion-application-to-ingest-data-to-your-data-explorer-pool"></a>Créer une application d’ingestion en streaming pour ingérer des données dans votre pool Data Explorer

Créez votre application pour ingérer des données dans votre pool Data Explorer à l’aide de votre langage préféré. Pour la variable *poolPath*, utilisez le point de terminaison de requête que vous avez enregistré dans les [prérequis](#prerequisites).

### <a name="c"></a>[C#](#tab/csharp)

```csharp
using Kusto.Data;
using Kusto.Ingest;
using System.IO;
using Kusto.Data.Common;

namespace StreamingIngestion
{
    class Program
    {
        static void Main(string[] args)
        {
            string poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net";
            string appId = "<appId>";
            string appKey = "<appKey>";
            string appTenant = "<appTenant>";
            string dbName = "<dbName>";
            string tableName = "<tableName>";

            // Create Kusto connection string with App Authentication
            var csb =
                new KustoConnectionStringBuilder(poolPath)
                    .WithAadApplicationKeyAuthentication(
                        applicationClientId: appId,
                        applicationKey: appKey,
                        authority: appTenant
                    );

            // Create a disposable client that will execute the ingestion
            using (IKustoIngestClient client = KustoIngestFactory.CreateStreamingIngestClient(csb))
            {
                // Initialize client properties
                var ingestionProperties =
                    new KustoIngestionProperties(
                        databaseName: dbName,
                        tableName: tableName
                    );

                // Ingest from a compressed file
                var fileStream = File.Open("MyFile.gz", FileMode.Open);
                // Create source options
                var sourceOptions = new StreamSourceOptions()
                {
                    CompressionType = DataSourceCompressionType.GZip,
                };
                // Ingest from stream
                var status = client.IngestFromStreamAsync(fileStream, ingestionProperties, sourceOptions).GetAwaiter().GetResult();
            }
        }
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
from Azure Synapse Analytics.kusto.data import KustoConnectionStringBuilder

from Azure Synapse Analytics.kusto.ingest import (
    IngestionProperties,
    DataFormat,
    KustoStreamingIngestClient
)

poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net"
appId = "<appId>"
appKey = "<appKey>"
appTenant = "<appTenant>"
dbName = "<dbName>"
tableName = "<tableName>"

csb = KustoConnectionStringBuilder.with_aad_application_key_authentication(
    poolPath,
    appId,
    appKey,
    appTenant
)
client = KustoStreamingIngestClient(csb)

ingestionProperties = IngestionProperties(
    database=dbName,
    table=tableName,
    data_format=DataFormat.CSV
)

# Ingest from file
# Automatically detects gz format
client.ingest_from_file("MyFile.gz", ingestion_properties=ingestionProperties) 
```

### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```nodejs
// Load modules using ES6 import statements:
import { DataFormat, IngestionProperties, StreamingIngestClient } from "azure-kusto-ingest";
import { KustoConnectionStringBuilder } from "azure-kusto-data";

// For earlier version, load modules using require statements:
// const IngestionProperties = require("azure-kusto-ingest").IngestionProperties;
// const KustoConnectionStringBuilder = require("azure-kusto-data").KustoConnectionStringBuilder;
// const {DataFormat} = require("azure-kusto-ingest").IngestionPropertiesEnums;
// const StreamingIngestClient = require("azure-kusto-ingest").StreamingIngestClient;

const poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net";
const appId = "<appId>";
const appKey = "<appKey>";
const appTenant = "<appTenant>";
const dbName = "<dbName>";
const tableName = "<tableName>";
const mappingName = "<mappingName>"; // Required for JSON formatted files

const ingestionProperties = new IngestionProperties({
    database: dbName, // Your database
    table: tableName, // Your table
    format: DataFormat.JSON,
    ingestionMappingReference: mappingName
});

// Initialize client with engine endpoint
const client = new StreamingIngestClient(
    KustoConnectionStringBuilder.withAadApplicationKeyAuthentication(
        poolPath,
        appId,
        appKey,
        appTenant
    ),
    ingestionProperties
);

// Automatically detects gz format
await client.ingestFromFile("MyFile.gz", ingestionProperties);
```

### <a name="go"></a>[Go](#tab/go)

```go
import (
    "context"
    "github.com/Azure Synapse Analytics/azure-kusto-go/kusto"
    "github.com/Azure Synapse Analytics/azure-kusto-go/kusto/ingest"
    "github.com/Azure Synapse Analytics/go-autorest/autorest/Azure Synapse Analytics/auth"
)

func ingest() {
    poolPath := "https://<Poolname>.<WorkspaceName>.kusto.windows.net"
    appId := "<appId>"
    appKey := "<appKey>"
    appTenant := "<appTenant>"
    dbName := "<dbName>"
    tableName := "<tableName>"
    mappingName := "<mappingName>" // Optional, can be nil

    // Creates a Kusto Authorizer using your client identity, secret, and tenant identity.
    // You may also uses other forms of authorization, see GoDoc > Authorization type.
    // auth package is: "github.com/Azure Synapse Analytics/go-autorest/autorest/Azure Synapse Analytics/auth"
    authorizer := kusto.Authorization{
        Config: auth.NewClientCredentialsConfig(appId, appKey, appTenant),
    }

    // Create a client
    client, err := kusto.New(poolPath, authorizer)
    if err != nil {
        panic("add error handling")
    }

    // Create an ingestion instance
    // Pass the client, the name of the database, and the name of table you wish to ingest into.
    in, err := ingest.New(client, dbName, tableName)
    if err != nil {
        panic("add error handling")
    }

    // Go currently only supports streaming from a byte array with a maximum size of 4 MB.
    jsonEncodedData := []byte("{\"a\":  1, \"b\":  10}\n{\"a\":  2, \"b\":  20}")

    // Ingestion from a stream commits blocks of fully formed data encodes (JSON, AVRO, ...) into Kusto:
    if err := in.Stream(context.Background(), jsonEncodedData, ingest.JSON, mappingName); err != nil {
        panic("add error handling")
    }
}
```

### <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.Azure Synapse Analytics.kusto.data.auth.ConnectionStringBuilder;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.IngestClient;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.IngestClientFactory;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.IngestionProperties;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.result.OperationStatus;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.source.CompressionType;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.source.StreamSourceInfo;
import java.io.FileInputStream;
import java.io.InputStream;

public class FileIngestion {
    public static void main(String[] args) throws Exception {
        String poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net";
        String appId = "<appId>";
        String appKey = "<appKey>";
        String appTenant = "<appTenant>";
        String dbName = "<dbName>";
        String tableName = "<tableName>";

        // Build connection string and initialize
        ConnectionStringBuilder csb =
            ConnectionStringBuilder.createWithAadApplicationCredentials(
                poolPath,
                appId,
                appKey,
                appTenant
            );

        // Initialize client and its properties
        IngestClient client = IngestClientFactory.createClient(csb);
        IngestionProperties ingestionProperties =
            new IngestionProperties(
                dbName,
                tableName
            );

        // Ingest from a compressed file
        // Create Source info
        InputStream zipInputStream = new FileInputStream("MyFile.gz");
        StreamSourceInfo zipStreamSourceInfo = new StreamSourceInfo(zipInputStream);
        // If the data is compressed
        zipStreamSourceInfo.setCompressionType(CompressionType.gz);
        // Ingest from stream
        OperationStatus status = client.ingestFromStream(zipStreamSourceInfo, ingestionProperties).getIngestionStatusCollection().get(0).status;
    }
}
```

---

## <a name="disable-streaming-ingestion-on-your-data-explorer-pool"></a>Désactiver l’ingestion en streaming sur votre pool Data Explorer

> [!WARNING]
> La désactivation de l’ingestion de streaming peut prendre plusieurs heures.

Avant de désactiver l’ingestion en streaming sur votre pool Data Explorer, supprimez la [stratégie d’ingestion en streaming](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context) de toutes les tables et bases de données concernées. La suppression de la stratégie d’ingestion en streaming déclenche la réorganisation des données au sein de votre pool Data Explorer. Les données d’ingestion de streaming sont déplacées du stockage initial vers le stockage permanent dans la banque de colonnes (étendues ou partitions). Ce processus peut prendre de quelques secondes à quelques heures, selon la quantité de données qui se trouvent dans le stockage initial.

### <a name="drop-the-streaming-ingestion-policy"></a>Supprimer la stratégie d’ingestion en streaming

Vous pouvez supprimer la stratégie d’ingestion en streaming à l’aide d’Azure Synapse Studio ou du portail Azure.

#### <a name="studio"></a>[Studio](#tab/azure-studio)

1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Développer**.
1. Sous **Scripts KQL**, sélectionnez **&plus;** (Ajouter une nouvelle ressource) > **Script KQL**. Dans le volet de droite, vous pouvez nommer votre script.
1. Dans le menu **Connecter à**, sélectionnez *contosodataexplorer*.
1. Dans le menu **Utiliser la base de données**, sélectionnez *TestDatabase*.
1. Collez la commande suivante, puis sélectionnez **Exécuter** pour créer la table.

    ```kusto
    .delete table TestTable policy streamingingestion
    ```

1. Dans le portail Azure, accédez à votre pool Data Explorer.
1. Dans **Paramètres**, sélectionnez **Configurations**.
1. Dans le volet **Configurations**, sélectionnez **Activé** pour activer **Ingestion de streaming**.
1. Sélectionnez **Enregistrer**.

#### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le portail Azure, accédez à votre pool Data Explorer et sélectionnez **Requête**.
1. Pour supprimer la stratégie d’ingestion de streaming de la table, copiez la commande suivante dans le **volet de requête**, puis sélectionnez **Exécuter**.

    ```Kusto
    .delete table TestTable policy streamingingestion
    ```

1. Dans **Paramètres**, sélectionnez **Configurations**.
1. Dans le volet **Configurations**, sélectionnez **Désactivé** pour désactiver **Ingestion de streaming**.
1. Sélectionnez **Enregistrer**.

---

## <a name="limitations"></a>Limites

- Les [curseurs de base de données](/azure/data-explorer/kusto/management/databasecursor?context=/azure/synapse-analytics/context/context) ne sont pas pris en charge pour une base de données si une [stratégie d’ingestion de streaming](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context) est définie et activée pour la base de données ou l’une de ses tables.
- Les [mappages de données](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context) doivent être [précréés](/azure/data-explorer/kusto/management/create-ingestion-mapping-command?context=/azure/synapse-analytics/context/context) pour être utilisés dans une ingestion de streaming. Les demandes d’ingestion en streaming individuelles ne prennent pas en charge les mappages de données inline.
- Les [étiquettes d’étendue](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#extent-tagging) ne peuvent pas être définies dans les données d’ingestion de streaming.
- [Stratégie de mise à jour](/azure/data-explorer/kusto/management/updatepolicy?context=/azure/synapse-analytics/context/context). La stratégie de mise à jour ne peut référencer que les données nouvellement ingérées dans la table source et aucune autre donnée ou table de la base de données.
- Si l’ingestion en streaming est utilisée sur l’une des tables de la base de données, cette dernière ne peut pas être utilisée en tant que leader pour les bases de données abonnées, ni en tant que fournisseur de données pour Azure Synapse Analytics Data Share.

## <a name="next-steps"></a>Étapes suivantes

- [Analyser avec l’Explorateur de données](../../get-started-analyze-data-explorer.md)
- [Surveiller les pools de l’Explorateur de données](../data-explorer-monitor-pools.md)
